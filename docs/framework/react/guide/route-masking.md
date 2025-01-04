# Table of Contents

- [라우트 마스킹(Route Masking)](#라우트-마스킹route-masking)
  - [라우트 마스킹은 어떻게 동작하나요?](#라우트-마스킹은-어떻게-동작하나요)
  - [라우트 마스킹 사용 방법](#라우트-마스킹-사용-방법)
    - [명령형 라우트 마스킹](#명령형-라우트-마스킹)
    - [선언적 라우트 마스킹](#선언적-라우트-마스킹)
  - [URL 공유 시 마스킹 해제](#url-공유-시-마스킹-해제)
  - [로컬 환경에서의 마스킹 해제 기본값](#로컬-환경에서의-마스킹-해제-기본값)
  - [페이지 새로고침 시 URL 마스크 해제](#페이지-새로고침-시-url-마스크-해제)

# 라우트 마스킹(Route Masking)

라우트 마스킹은 브라우저의 히스토리와 URL 바에 표시되는 실제 URL을 가리는 방법입니다. 이 기능은 실제로 이동하는 URL과 다른 URL을 보여주고 싶을 때 유용합니다. 이후 해당 URL이 공유되거나 (선택적으로) 페이지가 다시 로드될 때 표시된 URL로 돌아갈 수 있습니다. 몇 가지 예시를 살펴보겠습니다:

- `/photo/5/modal`과 같은 모달 라우트로 이동하지만, 실제 URL을 `/photos/5`로 마스킹
- `/post/5/comments`와 같은 모달 라우트로 이동하지만, 실제 URL을 `/posts/5`로 마스킹
- `?showLogin=true`와 같은 검색 파라미터가 포함된 라우트로 이동하지만, URL에서 검색 파라미터를 제거하여 마스킹
- `?modal=settings`와 같은 검색 파라미터가 포함된 라우트로 이동하지만, URL을 `/settings`로 마스킹

이러한 시나리오들은 라우트 마스킹을 통해 구현할 수 있으며, [병렬 라우트(parallel routes)](./parallel-routes.md)와 같은 더 복잡한 패턴을 지원하도록 확장할 수도 있습니다.


## 라우트 마스킹은 어떻게 동작하나요?

> [!IMPORTANT]
> 라우트 마스킹을 사용하기 위해 이 동작 방식을 이해할 필요는 없습니다. 이 섹션은 내부 동작이 궁금한 분들을 위한 설명입니다. 사용 방법을 알고 싶다면 [라우트 마스킹 사용법](#how-do-i-use-route-masking)으로 건너뛰세요!

라우트 마스킹은 `location.state` API를 활용하여 URL에 기록될 위치 안에 실제로 이동할 위치를 저장합니다. 이때, 실제 위치는 `__tempLocation` 상태 속성에 저장됩니다:

```tsx
const location = {
  pathname: '/photos/5',
  search: '',
  hash: '',
  state: {
    key: 'wesdfs',
    __tempKey: 'sadfasd',
    __tempLocation: {
      pathname: '/photo/5/modal',
      search: '',
      hash: '',
      state: {},
    },
  },
}
```

라우터가 `location.state.__tempLocation` 속성을 포함한 위치를 히스토리에서 파싱할 때, URL에서 파싱된 위치 대신 이 속성에 저장된 위치를 사용합니다. 이를 통해 `/photos/5`로 이동하더라도 실제로는 `/photo/5/modal`로 이동하게 됩니다. 이 과정에서 히스토리 위치는 `location.maskedLocation` 속성에 다시 저장됩니다. 이는 **실제 URL**을 알아야 할 때를 대비하기 위함입니다. 예를 들어, Devtools에서는 라우트가 마스킹되었는지 감지하고 마스킹된 URL 대신 실제 URL을 표시합니다.

이 모든 과정은 자동으로 처리되므로 여러분이 직접 신경 쓸 필요는 없습니다!


## 라우트 마스킹 사용 방법

라우트 마스킹은 두 가지 방식으로 사용할 수 있는 간단한 API입니다:

1. `<Link>`와 `navigate()` API에서 제공하는 `mask` 옵션을 통해 명령형으로 사용
2. 라우터의 `routeMasks` 옵션을 통해 선언적으로 사용

라우트 마스킹 API를 사용할 때, `mask` 옵션은 `<Link>`와 `navigate()` API가 받는 것과 동일한 네비게이션 객체를 받습니다. 즉, 여러분이 이미 익숙한 `to`, `replace`, `state`, `search` 옵션을 그대로 사용할 수 있습니다. 유일한 차이점은 `mask` 옵션이 네비게이션 대상 라우트의 URL을 마스킹하는 데 사용된다는 점입니다.

> 🧠 `mask` 옵션은 **타입 안전(type-safe)**합니다! TypeScript를 사용 중이라면, `mask` 옵션에 잘못된 네비게이션 객체를 전달하려고 하면 타입 에러가 발생합니다. 멋지죠!

```jsx
// 명령형 사용 예제
<Link to="/actual-path" mask="/masked-path">
  마스킹된 링크
</Link>

// 선언형 사용 예제
const router = createRouter({
  routeMasks: {
    "/actual-path": "/masked-path"
  }
});
```

이렇게 하면 실제 경로는 `/actual-path`이지만, 사용자에게는 `/masked-path`로 표시됩니다. 라우트 마스킹을 활용하면 URL을 깔끔하게 유지하면서도 내부적으로 복잡한 경로를 처리할 수 있습니다.


### 명령형 라우트 마스킹

`<Link>`와 `navigate()` API는 모두 `mask` 옵션을 지원합니다. 이 옵션을 사용하면 이동할 라우트의 URL을 마스킹할 수 있습니다. 아래는 `<Link>` 컴포넌트와 함께 사용하는 예제입니다:

```tsx
<Link
  to="/photos/$photoId/modal"
  params={{ photoId: 5 }}
  mask={{
    to: '/photos/$photoId',
    params: {
      photoId: 5,
    },
  }}
>
  사진 열기
</Link>
```

다음은 `navigate()` API와 함께 사용하는 예제입니다:

```tsx
const navigate = useNavigate()

function onOpenPhoto() {
  navigate({
    to: '/photos/$photoId/modal',
    params: { photoId: 5 },
    mask: {
      to: '/photos/$photoId',
      params: {
        photoId: 5,
      },
    },
  })
}
```

이 예제들은 라우트를 이동할 때 URL을 마스킹하는 방법을 보여줍니다. `mask` 옵션을 사용하면 실제 이동할 URL과 표시할 URL을 다르게 설정할 수 있습니다.


### 선언적 라우트 마스킹

명령형 API 외에도, 여러분은 라우터의 `routeMasks` 옵션을 사용하여 선언적으로 라우트를 마스킹할 수 있습니다. 모든 `<Link>`나 `navigate()` 호출에 `mask` 옵션을 전달할 필요 없이, 특정 패턴과 일치하는 라우트를 마스킹하기 위해 라우터에 라우트 마스크를 생성할 수 있습니다. 아래는 위와 동일한 라우트 마스크를 `routeMasks` 옵션을 사용하여 구현한 예제입니다:

```tsx
import { createRouteMask } from '@tanstack/react-router'

const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: (prev) => ({
    photoId: prev.photoId,
  }),
})

const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```

라우트 마스크를 생성할 때, 최소한 다음 인자를 전달해야 합니다:

- `routeTree` - 라우트 마스크가 적용될 라우트 트리
- `from` - 라우트 마스크가 적용될 라우트 ID
- `...navigateOptions` - `<Link>`와 `navigate()` API가 허용하는 표준 `to`, `search`, `params`, `replace` 등의 옵션

> 🧠 `createRouteMask` 옵션은 **타입 안전**합니다! TypeScript를 사용하는 경우, 유효하지 않은 라우트 마스크를 `routeMasks` 옵션에 전달하려고 하면 타입 오류가 발생합니다.


## URL 공유 시 마스킹 해제

URL을 공유할 때 자동으로 마스킹이 해제됩니다. 브라우저의 로컬 히스토리 스택에서 URL이 분리되면, URL 마스킹 데이터는 더 이상 사용할 수 없기 때문입니다. 기본적으로, 히스토리에서 URL을 복사하여 붙여넣는 순간, 마스킹 데이터는 사라집니다. 결국, 이것이 URL을 마스킹하는 목적이니까요!


## 로컬 환경에서의 마스킹 해제 기본값

**기본적으로, 로컬에서 페이지를 다시 로드할 때 URL은 마스킹 해제되지 않습니다.** 마스킹 데이터는 히스토리 위치의 `location.state` 속성에 저장됩니다. 따라서 히스토리 스택에 히스토리 위치가 여전히 메모리에 남아 있는 한, 마스킹 데이터는 유지되고 URL은 계속 마스킹된 상태로 유지됩니다.


## 페이지 새로고침 시 URL 마스크 해제

**위에서 언급한 대로, 기본적으로 페이지를 새로고침할 때 URL은 마스크 해제되지 않습니다.**

페이지를 새로고침할 때 로컬에서 URL을 마스크 해제하려면, 다음 3가지 방법 중 하나를 선택할 수 있습니다. 각 방법은 이전 방법보다 우선순위가 높으며, 설정된 경우 이전 설정을 덮어씁니다:

1. 라우터의 기본 `unmaskOnReload` 옵션을 `true`로 설정
2. `createRouteMask()`로 라우트 마스크를 생성할 때, 마스킹 함수에서 `unmaskOnReload: true` 옵션 반환
3. `<Link>` 컴포넌트나 `navigate()` API에 `unmaskOnReload: true` 옵션 전달


