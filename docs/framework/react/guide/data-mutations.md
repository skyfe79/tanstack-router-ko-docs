# Table of Contents

- [데이터 뮤테이션](#데이터-뮤테이션)
  - [뮤테이션 후 TanStack Router 무효화하기](#뮤테이션-후-tanstack-router-무효화하기)
  - [장기적인 뮤테이션 상태](#장기적인-뮤테이션-상태)
  - [뮤테이션 키 사용하기](#뮤테이션-키-사용하기)
  - [`router.subscribe` 메서드 사용하기](#routersubscribe-메서드-사용하기)

# 데이터 뮤테이션

TanStack Router는 데이터를 저장하거나 캐싱하지 않기 때문에, 외부 뮤테이션 이벤트로 인한 URL 부작용에 반응하는 것 외에는 데이터 뮤테이션에서의 역할이 거의 없습니다. 그렇지만, 여러분에게 유용할 수 있는 뮤테이션 관련 기능과 이를 구현한 라이브러리 목록을 정리해 보았습니다.

다음과 같은 기능을 지원하는 뮤테이션 유틸리티를 찾아 사용해 보세요:

- 제출 상태 처리 및 캐싱
- 로컬 및 글로벌 낙관적 UI 지원
- 무효화를 위한 내장 훅 제공 (또는 자동 지원)
- 동시에 여러 개의 진행 중인 뮤테이션 처리
- 뮤테이션 상태를 전역적으로 접근 가능한 리소스로 구성
- 제출 상태 기록 및 가비지 컬렉션

추천 라이브러리:

- [TanStack Query](https://tanstack.com/query/latest/docs/react/guides/mutations)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [urql](https://formidable.com/open-source/urql/)
- [Relay](https://relay.dev/)
- [Apollo](https://www.apollographql.com/docs/react/)

또는...

- [Zustand](https://zustand-demo.pmnd.rs/)
- [Jotai](https://jotai.org/)
- [Recoil](https://recoiljs.org/)
- [Redux](https://redux.js.org/)

데이터 페칭과 마찬가지로, 뮤테이션 상태도 만능 솔루션이 아닙니다. 따라서 여러분과 팀의 요구에 맞는 솔루션을 선택해야 합니다. 몇 가지 다른 솔루션을 시도해 보고 가장 적합한 것을 찾아보는 것을 권장합니다.

> ⚠️ 아직 여기 있나요? 제출 상태는 지속성(persistence)과 관련해 흥미로운 주제입니다. 모든 뮤테이션을 영원히 보관해야 할까요? 언제 제거해야 하는지 어떻게 알 수 있을까요? 사용자가 화면을 떠났다가 다시 돌아오면 어떻게 해야 할까요? 이제 이 주제를 깊이 파고들어 보겠습니다!


## 뮤테이션 후 TanStack Router 무효화하기

TanStack Router는 기본적으로 단기 캐싱 기능을 제공합니다. 라우트 매치가 언마운트된 후 데이터를 저장하지 않더라도, 라우터에 저장된 데이터와 관련된 뮤테이션이 발생하면 현재 라우트 매치의 데이터가 오래된 상태가 될 가능성이 높습니다.

로더 데이터와 관련된 뮤테이션이 발생했을 때, `router.invalidate`를 사용하여 현재 라우트 매치를 강제로 다시 로드할 수 있습니다:

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    router.invalidate()
  } catch {
    //
  }
}
```

현재 라우트 매치를 무효화하는 작업은 백그라운드에서 진행됩니다. 따라서 새로운 데이터가 준비될 때까지 기존 데이터가 계속 제공되며, 이는 새로운 라우트로 네비게이션하는 것과 동일한 방식으로 작동합니다.

모든 로더가 완료될 때까지 무효화를 기다리려면, `router.invalidate`에 `{sync: true}`를 전달하면 됩니다:

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    await router.invalidate({ sync: true })
  } catch {
    //
  }
}
```


## 장기적인 뮤테이션 상태

사용하는 뮤테이션 라이브러리와 상관없이, 뮤테이션은 종종 제출과 관련된 상태를 생성합니다. 대부분의 뮤테이션은 한 번 설정하고 잊어버리는 경우가 많지만, 일부 뮤테이션 상태는 더 오래 지속됩니다. 이는 낙관적 UI(Optimistic UI)를 지원하거나 사용자에게 제출 상태에 대한 피드백을 제공하기 위함입니다. 대부분의 상태 관리자는 이 제출 상태를 올바르게 유지하고 노출하여 로딩 스피너, 성공 메시지, 오류 메시지 등의 UI 요소를 표시할 수 있게 합니다.

다음과 같은 상호작용을 생각해 봅시다:

1. 사용자가 `/posts/123/edit` 화면으로 이동하여 게시물을 편집합니다.
2. 사용자가 `123`번 게시물을 편집하고 성공하면, 편집기 아래에 "게시물이 업데이트되었습니다"라는 성공 메시지가 표시됩니다.
3. 사용자가 `/posts` 화면으로 이동합니다.
4. 사용자가 다시 `/posts/123/edit` 화면으로 돌아옵니다.

라우트 변경을 뮤테이션 관리 라이브러리에 알리지 않으면, 제출 상태가 여전히 남아 있을 가능성이 있습니다. 이 경우 사용자가 이전 화면으로 돌아왔을 때 **"게시물이 성공적으로 업데이트되었습니다"**라는 메시지를 계속 보게 됩니다. 이는 이상적이지 않습니다. 분명히, 우리는 이 뮤테이션 상태를 영원히 유지하려는 의도가 아니었습니다. 그렇죠?


## 뮤테이션 키 사용하기

가장 간단한 방법은 뮤테이션 라이브러리가 키(key) 메커니즘을 지원하도록 하는 것입니다. 이렇게 하면 키가 변경될 때 뮤테이션의 상태를 초기화할 수 있습니다.

```tsx
const routeApi = getRouteApi('/posts/$postId/edit')

function EditPost() {
  const { roomId } = routeApi.useParams()

  const sendMessageMutation = useCoolMutation({
    fn: sendMessage,
    // roomId가 변경되면 뮤테이션 상태를 초기화
    // 제출 상태도 포함
    key: ['sendMessage', roomId],
  })

  // 여러 메시지 전송
  const test = () => {
    sendMessageMutation.mutate({ roomId, message: '안녕!' })
    sendMessageMutation.mutate({ roomId, message: '어떻게 지내?' })
    sendMessageMutation.mutate({ roomId, message: '잘 가!' })
  }

  return (
    <>
      {sendMessageMutation.submissions.map((submission) => {
        return (
          
            {submission.status}
            {submission.message}
          
        )
      })}
    
  )
}
```

이 코드에서 `key`는 뮤테이션 상태를 관리하는 데 사용됩니다. `roomId`가 변경되면 뮤테이션 상태가 자동으로 초기화됩니다. 이를 통해 여러 메시지를 전송할 때 각 메시지의 상태를 독립적으로 관리할 수 있습니다.


## `router.subscribe` 메서드 사용하기

키(key) 메커니즘이 없는 라이브러리의 경우, 사용자가 화면을 벗어날 때 뮤테이션 상태를 수동으로 초기화해야 할 수 있습니다. 이를 해결하기 위해 TanStack Router의 `invalidate`와 `subscribe` 메서드를 사용하여 사용자가 더 이상 필요하지 않은 뮤테이션 상태를 지울 수 있습니다.

`router.subscribe` 메서드는 다양한 라우터 이벤트에 콜백을 등록하는 함수입니다. 여기서 특히 사용할 이벤트는 `onResolved` 이벤트입니다. 이 이벤트는 **위치 경로가 변경되었을 때(단순히 새로고침된 경우가 아닌) 최종적으로 해결되었을 때** 발생한다는 점을 이해하는 것이 중요합니다.

이 이벤트는 이전 뮤테이션 상태를 초기화하기에 적합한 위치입니다. 다음은 예제 코드입니다:

```tsx
const router = createRouter()
const coolMutationCache = createCoolMutationCache()

const unsubscribeFn = router.subscribe('onResolved', () => {
  // 라우트가 변경될 때 뮤테이션 상태 초기화
  coolMutationCache.clear()
})
```


