# Table of Contents

- [외부 데이터 로딩](#외부-데이터-로딩)
  - [데이터를 **저장**할지, **조율**할지?](#데이터를-저장할지-조율할지)
  - [어떤 데이터 가져오기 라이브러리를 지원하나요?](#어떤-데이터-가져오기-라이브러리를-지원하나요)
  - [로더를 사용하여 데이터 로드 보장하기](#로더를-사용하여-데이터-로드-보장하기)
  - [TanStack Query를 사용한 실제 예제](#tanstack-query를-사용한-실제-예제)
    - [TanStack Query에서 에러 처리하기](#tanstack-query에서-에러-처리하기)
  - [SSR Dehydration/Hydration](#ssr-dehydrationhydration)
  - [중요한 데이터의 탈수/수화 처리](#중요한-데이터의-탈수수화-처리)

# 외부 데이터 로딩

> [!중요]
> 이 가이드는 외부 상태 관리 라이브러리와 TanStack Router의 통합을 위한 데이터 가져오기, SSR(서버 사이드 렌더링), 하이드레이션/디하이드레이션, 스트리밍에 초점을 맞춥니다. 아직 [표준 데이터 로딩 가이드](./data-loading.md)를 읽지 않았다면, 먼저 해당 가이드를 확인하세요.


## 데이터를 **저장**할지, **조율**할지?

Router는 대부분의 데이터 요구사항을 기본적으로 저장하고 관리할 수 있지만, 때로는 더 강력한 기능이 필요할 수 있습니다!

Router는 외부 데이터 가져오기 및 캐싱 라이브러리를 위한 완벽한 **조율자**로 설계되었습니다. 이는 여러분이 원하는 어떤 데이터 가져오기/캐싱 라이브러리도 사용할 수 있으며, 라우터가 사용자의 네비게이션과 데이터 신선도에 대한 기대에 맞춰 데이터 로딩을 조율한다는 의미입니다.


## 어떤 데이터 가져오기 라이브러리를 지원하나요?

TanStack Router는 **비동기 Promise를 지원하는 모든 데이터 가져오기 라이브러리**와 함께 사용할 수 있습니다. 이에는 다음과 같은 라이브러리가 포함됩니다:

- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [urql](https://formidable.com/open-source/urql/)
- [Relay](https://relay.dev/)
- [Apollo](https://www.apollographql.com/docs/react/)

또한, 다음과 같은 라이브러리도 사용 가능합니다:

- [Zustand](https://zustand-demo.pmnd.rs/)
- [Jotai](https://jotai.org/)
- [Recoil](https://recoiljs.org/)
- [Redux](https://redux.js.org/)

**Promise를 반환하고 데이터를 읽고 쓸 수 있는** 모든 라이브러리를 통합할 수 있습니다.


## 로더를 사용하여 데이터 로드 보장하기

라우터에 외부 캐싱/데이터 라이브러리를 통합하는 가장 쉬운 방법은 `route.loader`를 사용하는 것입니다. 이를 통해 라우트 내부에서 필요한 데이터가 로드되고 표시할 준비가 되었는지 확인할 수 있습니다.

> ⚠️ 하지만 왜 그래야 할까요? 렌더링에 필요한 중요한 데이터를 로더에서 미리 로드하는 것은 몇 가지 이유로 매우 중요합니다:
>
> - "로딩 중" 상태가 깜빡이지 않음
> - 컴포넌트 기반 데이터 가져오기로 인한 데이터 가져오기 지연(워터폴 현상) 방지
> - SEO에 유리함. 렌더링 시점에 데이터가 있으면 검색 엔진이 이를 인덱싱할 수 있음

다음은 라우트의 `loader` 옵션을 사용하여 데이터 캐시를 초기화하는 간단한 예제입니다(이렇게 하지 마세요):

```tsx
// src/routes/posts.tsx

let postsCache = []

export const Route = createFileRoute('/posts')({
  loader: async () => {
    postsCache = await fetchPosts()
  },
  component: () => {
    return (
      <div>
        {postsCache.map((post) => (
          <div key={post.id}>{post.title}</div>
        ))}
      </div>
    )
  },
})
```

이 예제는 **명백히 문제가 있지만**, 라우트의 `loader` 옵션을 사용하여 데이터로 캐시를 초기화할 수 있다는 점을 보여줍니다. 이제 TanStack Query를 사용한 더 현실적인 예제를 살펴보겠습니다.

- `fetchPosts`를 여러분이 선호하는 데이터 가져오기 라이브러리의 미리 가져오기 API로 대체하세요.
- `postsCache`를 여러분이 선호하는 데이터 가져오기 라이브러리의 읽기 또는 가져오기 API나 훅으로 대체하세요.


## TanStack Query를 사용한 실제 예제

TanStack Query를 활용한 더 현실적인 예제를 살펴보겠습니다.

```tsx
// src/routes/posts.tsx

const postsQueryOptions = queryOptions({
  queryKey: ['posts'],
  queryFn: () => fetchPosts(),
})

export const Route = createFileRoute('/posts')({
  // 데이터가 로드되도록 `loader` 옵션 사용
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  component: () => {
    // 캐시에서 데이터를 읽고 업데이트를 구독
    const {
      data: { posts },
    } = useSuspenseQuery(postsQueryOptions)

    return (
      <div>
        {posts.map((post) => (
          <div key={post.id}>{post.title}</div>
        ))}
      </div>
    )
  },
})
```


### TanStack Query에서 에러 처리하기

`Tanstack Query`와 `suspense`를 사용할 때 에러가 발생하면, 리렌더링 시 다시 시도하도록 쿼리에 알려야 합니다. 이는 `useQueryErrorResetBoundary` 훅에서 제공하는 `reset` 함수를 사용하여 처리할 수 있습니다. 이 함수를 에러 컴포넌트가 마운트되자마자 실행하는 효과(effect)에서 호출하면 됩니다. 이렇게 하면 쿼리가 리셋되고, 라우트 컴포넌트가 다시 렌더링될 때 데이터를 다시 가져오려고 시도합니다. 또한, 사용자가 `retry` 버튼을 클릭하지 않고 라우트를 벗어나는 경우에도 대응할 수 있습니다.

```tsx
export const Route = createFileRoute('/posts')({
  loader: () => queryClient.ensureQueryData(postsQueryOptions),
  errorComponent: ({ error, reset }) => {
    const router = useRouter()
    const queryErrorResetBoundary = useQueryErrorResetBoundary()

    React.useEffect(() => {
      // 쿼리 에러 바운더리 리셋
      queryErrorResetBoundary.reset()
    }, [queryErrorResetBoundary])

    return (
      <>
        {error.message}
        <button
          onClick={() => {
            // 라우트를 무효화하여 로더를 다시 로드하고, 라우터 에러 바운더리 리셋
            router.invalidate()
          }}
        >
          retry
        </button>
      </>
    )
  },
})
```


## SSR Dehydration/Hydration

TanStack Router의 편리한 Dehydration/Hydration API를 활용하면 서버와 클라이언트 간에 데이터를 전달하고 필요한 곳에서 다시 복원할 수 있습니다. 이제 서드파티의 중요 데이터와 지연 데이터를 어떻게 처리하는지 살펴보겠습니다.


## 중요한 데이터의 탈수/수화 처리

**첫 렌더링/페인트에 필요한 중요한 데이터**를 위해, TanStack Router는 라우터를 설정할 때 **`dehydrate`와 `hydrate`** 옵션을 지원합니다. 이 콜백 함수들은 서버와 클라이언트에서 라우터가 일반적으로 탈수 및 수화를 수행할 때 자동으로 호출되며, 여러분이 직접 데이터를 추가할 수 있게 해줍니다.

`dehydrate` 함수는 직렬화 가능한 JSON 데이터를 반환할 수 있으며, 이 데이터는 클라이언트로 전송되는 탈수된 페이로드에 병합되어 주입됩니다. 이 페이로드는 `DehydrateRouter` 컴포넌트를 통해 전달되며, 렌더링될 때 클라이언트의 `hydrate` 함수에서 데이터를 다시 제공합니다.

예를 들어, TanStack Query의 `QueryClient`를 탈수 및 수화하여 서버에서 가져온 데이터가 클라이언트에서 수화될 수 있도록 해보겠습니다.

```tsx
// src/router.tsx

export function createRouter() {
  // `createRouter` 함수 내부에서 로더 클라이언트나 유사한 데이터 저장소를 생성합니다.
  // 이렇게 하면 각 요청마다 고유한 데이터 저장소가 생성되고,
  // 서버와 클라이언트 모두에서 항상 존재하게 됩니다.
  const queryClient = new QueryClient()

  return createRouter({
    routeTree,
    // 필요에 따라 라우터 컨텍스트에 로더 클라이언트를 제공할 수 있습니다.
    // (라우터 컨텍스트에 원하는 무엇이든 제공할 수 있습니다!)
    context: {
      queryClient,
    },
    // 서버에서 로더 클라이언트를 탈수하여 라우터가 이를 직렬화하고
    // 클라이언트로 전송할 수 있게 합니다.
    dehydrate: () => {
      return {
        queryClientState: dehydrate(queryClient),
      }
    },
    // 클라이언트에서 서버에서 탈수한 데이터로 로더 클라이언트를 수화합니다.
    hydrate: (dehydrated) => {
      hydrate(queryClient, dehydrated.queryClientState)
    },
    // 필요에 따라 `Wrap`을 사용하여 라우터를 로더 클라이언트 프로바이더로 감쌀 수 있습니다.
    Wrap: ({ children }) => {
      return (
        
          {children}
        
      )
    },
  })
}
```


