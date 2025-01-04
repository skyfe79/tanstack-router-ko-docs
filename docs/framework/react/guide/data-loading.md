# Table of Contents

- [데이터 로딩](#데이터-로딩)
  - [라우트 로딩 생명주기](#라우트-로딩-생명주기)
  - [라우터 캐시를 사용할까요, 말까요?](#라우터-캐시를-사용할까요-말까요)
    - [TanStack 라우터 캐시의 장점](#tanstack-라우터-캐시의-장점)
    - [TanStack 라우터 캐시의 단점](#tanstack-라우터-캐시의-단점)
  - [라우터 캐시 사용하기](#라우터-캐시-사용하기)
  - [라우트 `loader` 함수](#라우트-loader-함수)
  - [`loader` 함수의 매개변수](#loader-함수의-매개변수)
  - [`loader`에서 데이터 사용하기](#loader에서-데이터-사용하기)
  - [의존성 기반 Stale-While-Revalidate 캐싱](#의존성-기반-stale-while-revalidate-캐싱)
    - [주요 옵션](#주요-옵션)
    - [⚠️ 몇 가지 중요한 기본값](#-몇-가지-중요한-기본값)
    - [`loaderDeps`를 사용해 검색 파라미터에 접근하기](#loaderdeps를-사용해-검색-파라미터에-접근하기)
    - [`staleTime`을 사용하여 데이터가 신선하다고 간주되는 시간 제어하기](#staletime을-사용하여-데이터가-신선하다고-간주되는-시간-제어하기)
  - [stale-while-revalidate 캐싱 비활성화하기](#stale-while-revalidate-캐싱-비활성화하기)
  - [캐싱에서 제외하기 위해 `shouldReload`와 `gcTime` 사용하기](#캐싱에서-제외하기-위해-shouldreload와-gctime-사용하기)
    - [캐싱을 사용하지 않으면서도 미리 가져오기 활용하기](#캐싱을-사용하지-않으면서도-미리-가져오기-활용하기)
  - [모든 로더 이벤트를 외부 캐시로 전달하기](#모든-로더-이벤트를-외부-캐시로-전달하기)
  - [라우터 컨텍스트 사용하기](#라우터-컨텍스트-사용하기)
  - [경로 파라미터 사용하기](#경로-파라미터-사용하기)
  - [라우트 컨텍스트 사용하기](#라우트-컨텍스트-사용하기)
  - [로더에서 검색 파라미터 사용하기](#로더에서-검색-파라미터-사용하기)
    - [`routeOptions.loaderDeps`를 통해 검색 파라미터 접근하기](#routeoptionsloaderdeps를-통해-검색-파라미터-접근하기)
  - [Abort Signal 사용하기](#abort-signal-사용하기)
  - [`preload` 플래그 사용하기](#preload-플래그-사용하기)
  - [느린 로더 처리하기](#느린-로더-처리하기)
  - [로딩 중인 컴포넌트 표시하기](#로딩-중인-컴포넌트-표시하기)
  - [Pending 컴포넌트 깜빡임 방지](#pending-컴포넌트-깜빡임-방지)
  - [에러 처리하기](#에러-처리하기)
    - [`routeOptions.onError`로 에러 처리하기](#routeoptionsonerror로-에러-처리하기)
    - [`routeOptions.onCatch`를 사용한 에러 처리](#routeoptionsoncatch를-사용한-에러-처리)
    - [`routeOptions.errorComponent`로 에러 처리하기](#routeoptionserrorcomponent로-에러-처리하기)
    - [기본 `ErrorComponent` 사용하기](#기본-errorcomponent-사용하기)

# 데이터 로딩

데이터 로딩은 웹 애플리케이션에서 흔히 발생하는 문제이며, 라우팅과 밀접한 관련이 있습니다. 애플리케이션의 페이지를 로드할 때, 페이지의 모든 비동기 요구사항을 가능한 한 빠르고 병렬로 가져와 처리하는 것이 이상적입니다. 라우터는 이러한 비동기 의존성을 조율하기에 가장 적합한 위치입니다. 일반적으로 라우터는 콘텐츠가 렌더링되기 전에 사용자가 어디로 향하는지 알고 있는 유일한 곳이기 때문입니다.

여러분은 Next.js의 `getServerSideProps`나 Remix/React-Router의 `loader`에 익숙할 수 있습니다. TanStack Router도 라우트별로 자산을 미리 로드하거나 병렬로 로드하는 유사한 기능을 제공합니다. 이를 통해 React는 Suspense를 통해 데이터를 가져오는 동안 가능한 한 빠르게 렌더링할 수 있습니다.

일반적인 라우터의 기대치를 넘어, TanStack Router는 **내장된 SWR 캐싱** 기능을 제공합니다. 이는 라우트 로더를 위한 장기적인 메모리 내 캐싱 레이어입니다. 따라서 TanStack Router를 사용하면 라우트 데이터를 미리 로드하여 즉시 로드되도록 하거나, 이전에 방문한 라우트의 데이터를 임시로 캐시하여 나중에 다시 사용할 수 있습니다.


## 라우트 로딩 생명주기

URL 또는 히스토리 업데이트가 감지될 때마다, 라우터는 다음 순서대로 작업을 실행합니다:

1. **라우트 매칭 (상위에서 하위로)**
   - `route.params.parse`
   - `route.validateSearch`

2. **라우트 사전 로딩 (순차적)**
   - `route.beforeLoad`
   - `route.onError`
     - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`

3. **라우트 로딩 (병렬)**
   - `route.component.preload?`
   - `route.loader`
     - `route.pendingComponent` (선택 사항)
     - `route.component`
   - `route.onError`
     - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`

이 과정을 통해 라우터는 URL 변경에 따라 필요한 컴포넌트와 데이터를 효율적으로 로드하고, 오류 발생 시 적절한 대응을 할 수 있습니다.


## 라우터 캐시를 사용할까요, 말까요?

TanStack의 라우터 캐시는 대부분의 중소규모 애플리케이션에 적합할 가능성이 높습니다. 하지만 이를 사용할 때의 장단점과 TanStack Query와 같은 더 강력한 캐싱 솔루션과의 차이를 이해하는 것이 중요합니다.

### TanStack 라우터 캐시의 장점

- **내장형으로 사용이 간편하며 추가 의존성이 필요 없음**
- **라우트 단위로 중복 제거, 미리 가져오기, 로딩, stale-while-revalidate, 백그라운드 리페칭 처리**
- **전체 라우트와 캐시를 한 번에 무효화하는 간단한 무효화 방식**
- **자동 가비지 컬렉션**
- **라우트 간 데이터 공유가 적은 앱에 적합**
- **SSR에서 "그냥 작동"**

### TanStack 라우터 캐시의 단점

- **지속성 어댑터/모델이 없음**
- **라우트 간 공유 캐싱/중복 제거 기능 없음**
- **내장된 뮤테이션 API가 없음 (많은 예제에서 제공되는 기본 `useMutation` 훅으로 충분할 수 있음)**
- **내장된 캐시 수준의 낙관적 업데이트 API가 없음 (컴포넌트 수준에서 `useMutation` 훅과 같은 임시 상태를 사용하여 이를 달성할 수 있음)**

> [!TIP]
> TanStack Query와 같은 더 강력한 솔루션을 사용하고 싶거나 필요하다면, [외부 데이터 로딩](./external-data-loading.md) 가이드로 바로 이동하세요.


## 라우터 캐시 사용하기

라우터 캐시는 내장되어 있으며, 라우트의 `loader` 함수에서 데이터를 반환하는 것만큼 쉽게 사용할 수 있습니다. 함께 알아봅시다!


## 라우트 `loader` 함수

라우트 `loader` 함수는 라우트가 매치될 때 호출됩니다. 이 함수는 유용한 속성들을 담고 있는 객체를 단일 인자로 받습니다. 이 속성들에 대해서는 잠시 후에 살펴보겠습니다. 먼저, 라우트 `loader` 함수의 예제를 살펴보겠습니다:

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
})
```


## `loader` 함수의 매개변수

`loader` 함수는 다음과 같은 속성을 가진 단일 객체를 받습니다:

- `abortController` - 라우트의 abortController입니다. 라우트가 언로드되거나 더 이상 관련이 없어지고 현재 `loader` 함수 호출이 오래된 경우, 이 신호가 취소됩니다.
- `cause` - 현재 라우트 매칭의 원인으로, `enter` 또는 `stay` 중 하나입니다.
- `context` - 라우트의 컨텍스트 객체로, 다음 두 가지가 합쳐진 형태입니다:
  - 부모 라우트 컨텍스트
  - `beforeLoad` 옵션에서 제공된 현재 라우트의 컨텍스트
- `deps` - `Route.loaderDeps` 함수에서 반환된 객체 값입니다. `Route.loaderDeps`가 정의되지 않은 경우, 빈 객체가 제공됩니다.
- `location` - 현재 위치
- `params` - 라우트의 경로 매개변수
- `parentMatchPromise` - `Promise<RouteMatch>` (루트 라우트의 경우 `undefined`)
- `preload` - 라우트가 로드되는 대신 미리 로드될 때 `true`가 되는 불리언 값
- `route` - 라우트 자체

이 매개변수들을 사용하면 다양한 작업을 수행할 수 있습니다. 하지만 먼저, `loader` 함수가 어떻게 제어되고 언제 호출되는지 살펴보겠습니다.


## `loader`에서 데이터 사용하기

`loader`에서 데이터를 사용하려면 라우트 객체에 정의된 `useLoaderData` 훅을 사용합니다.

```tsx
const posts = Route.useLoaderData()
```

만약 라우트 객체에 바로 접근할 수 없는 경우(예: 현재 라우트의 깊은 컴포넌트 트리 내부에 있는 경우), `getRouteApi`를 사용하여 동일한 훅(또는 라우트 객체의 다른 훅들)에 접근할 수 있습니다. 이 방법은 라우트 객체를 직접 임포트하는 것보다 선호되어야 합니다. 라우트 객체를 임포트하면 순환 의존성이 발생할 가능성이 높기 때문입니다.

```tsx
import { getRouteApi } from '@tanstack/react-router'

// 컴포넌트 내부에서

const routeApi = getRouteApi('/posts')
const data = routeApi.useLoaderData()
```


## 의존성 기반 Stale-While-Revalidate 캐싱

TanStack Router는 라우트 로더를 위한 내장된 Stale-While-Revalidate 캐싱 계층을 제공합니다. 이 캐싱은 라우트의 의존성을 기반으로 키를 생성합니다:

- 라우트의 완전히 파싱된 경로명
  - 예: `/posts/1` vs `/posts/2`
- `loaderDeps` 옵션을 통해 제공된 추가 의존성
  - 예: `loaderDeps: ({ search: { pageIndex, pageSize } }) => ({ pageIndex, pageSize })`

이러한 의존성을 키로 사용하여, TanStack Router는 라우트의 `loader` 함수에서 반환된 데이터를 캐시에 저장합니다. 이후 동일한 라우트 매칭에 대한 요청이 들어오면 캐시된 데이터를 사용하여 응답합니다. 즉, 라우트 데이터가 이미 캐시에 있는 경우, 즉시 반환된 후 데이터의 **신선도**에 따라 백그라운드에서 다시 가져올 수 있습니다.


### 주요 옵션

TanStack Router는 라우트 로더의 키(key) 설정과 캐싱 동작을 제어하기 위한 다양한 옵션을 제공합니다. 이 옵션들을 여러분이 사용할 가능성이 높은 순서대로 살펴보겠습니다:

- `routeOptions.loaderDeps`
  - 라우터의 검색 파라미터를 제공하고, `loader` 함수에서 사용할 의존성 객체를 반환하는 함수입니다. 네비게이션 간에 이 의존성이 변경되면, `staleTime` 설정과 상관없이 라우트가 다시 로드됩니다. 의존성은 깊은 비교(deep equality check)를 통해 비교됩니다.
  
- `routeOptions.staleTime`
- `routerOptions.defaultStaleTime`
  - 라우트 데이터가 신선하다고 간주되는 시간(밀리초 단위)을 설정합니다. 이 시간 동안 데이터를 다시 로드하지 않습니다.
  
- `routeOptions.preloadStaleTime`
- `routerOptions.defaultPreloadStaleTime`
  - 라우트 데이터를 미리 가져올 때(preload) 신선하다고 간주되는 시간(밀리초 단위)을 설정합니다.
  
- `routeOptions.gcTime`
- `routerOptions.defaultGcTime`
  - 라우트 데이터가 캐시에 유지되는 시간(밀리초 단위)을 설정합니다. 이 시간이 지나면 가비지 컬렉션(garbage collection) 대상이 됩니다.
  
- `routeOptions.shouldReload`
  - `beforeLoad`와 `loaderContext` 파라미터를 받아 라우트를 다시 로드할지 여부를 나타내는 불리언 값을 반환하는 함수입니다. 이 옵션은 `staleTime`과 `loaderDeps`를 넘어서 라우트를 언제 다시 로드할지에 대한 추가적인 제어를 제공하며, Remix의 `shouldLoad` 옵션과 유사한 패턴을 구현하는 데 사용할 수 있습니다.

이 옵션들을 활용하면 라우트 데이터의 신선도와 캐싱 동작을 세밀하게 제어할 수 있습니다.


### ⚠️ 몇 가지 중요한 기본값

- 기본적으로 `staleTime`은 `0`으로 설정되어 있습니다. 이는 라우트의 데이터가 항상 오래된 것으로 간주되며, 라우트가 다시 매칭될 때마다 백그라운드에서 항상 다시 로드된다는 의미입니다.
- 기본적으로, 이전에 미리 로드된 라우트는 **30초 동안** 신선한 상태로 간주됩니다. 즉, 라우트가 미리 로드된 후 30초 이내에 다시 미리 로드되면 두 번째 미리 로드는 무시됩니다. 이는 너무 자주 발생하는 불필요한 미리 로드를 방지하기 위함입니다. **라우트가 일반적으로 로드될 때는 표준 `staleTime`이 적용됩니다.**
- 기본적으로 `gcTime`은 **30분**으로 설정되어 있습니다. 이는 30분 동안 접근되지 않은 모든 라우트 데이터가 가비지 컬렉션되어 캐시에서 제거된다는 의미입니다.
- `router.invalidate()`을 사용하면 모든 활성 라우트가 즉시 로더를 다시 로드하고, 캐시된 모든 라우트의 데이터를 오래된 것으로 표시합니다.


### `loaderDeps`를 사용해 검색 파라미터에 접근하기

`/posts` 라우트가 `offset`과 `limit`이라는 검색 파라미터를 통해 페이지네이션을 지원한다고 가정해 봅시다. 캐시가 이 데이터를 고유하게 저장하려면, `loaderDeps` 함수를 통해 이러한 검색 파라미터에 접근해야 합니다. 이를 명시적으로 식별함으로써, 서로 다른 `offset`과 `limit` 값을 가진 `/posts` 라우트 매치가 서로 섞이지 않게 됩니다!

이러한 의존성(deps)을 설정하면, 의존성이 변경될 때마다 라우트가 항상 다시 로드됩니다.

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps: { offset, limit } }) =>
    fetchPosts({
      offset,
      limit,
    }),
})
```


### `staleTime`을 사용하여 데이터가 신선하다고 간주되는 시간 제어하기

기본적으로 네비게이션의 `staleTime`은 `0`ms로 설정되어 있습니다(미리 가져오기(preload)의 경우 30초). 이는 라우트의 데이터가 항상 오래된 것으로 간주되며, 라우트가 매칭되고 네비게이션될 때마다 백그라운드에서 항상 다시 로드된다는 의미입니다.

**이 설정은 대부분의 경우에 적합하지만, 일부 라우트 데이터는 더 정적이거나 로드 비용이 높을 수 있습니다.** 이런 경우에는 `staleTime` 옵션을 사용하여 라우트 데이터가 신선하다고 간주되는 시간을 제어할 수 있습니다. 예제를 살펴보겠습니다:

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  // 라우트 데이터를 10초 동안 신선하다고 간주
  staleTime: 10_000,
})
```

`staleTime` 옵션에 `10_000`을 전달하면, 라우터는 라우트 데이터를 10초 동안 신선하다고 간주합니다. 즉, 사용자가 `/about`에서 `/posts`로 10초 이내에 다시 네비게이션하면, 라우트 데이터는 다시 로드되지 않습니다. 하지만 10초가 지난 후에 `/about`에서 `/posts`로 네비게이션하면, 라우트 데이터는 **백그라운드에서** 다시 로드됩니다.


## stale-while-revalidate 캐싱 비활성화하기

특정 라우트에서 stale-while-revalidate 캐싱을 비활성화하려면 `staleTime` 옵션을 `Infinity`로 설정합니다:

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  staleTime: Infinity,
})
```

모든 라우트에 대해 이 기능을 비활성화하려면 라우터의 `defaultStaleTime` 옵션을 설정하면 됩니다:

```tsx
const router = createRouter({
  routeTree,
  defaultStaleTime: Infinity,
})
```


## 캐싱에서 제외하기 위해 `shouldReload`와 `gcTime` 사용하기

Remix의 기본 기능과 유사하게, 특정 라우트가 진입할 때나 중요한 로더 의존성이 변경될 때만 로드되도록 설정할 수 있습니다. 이를 위해 `gcTime` 옵션과 `shouldReload` 옵션을 함께 사용할 수 있습니다. `shouldReload`는 `boolean` 값을 받거나, `beforeLoad`와 `loaderContext` 파라미터를 받아 라우트를 다시 로드할지 여부를 나타내는 `boolean`을 반환하는 함수를 받을 수 있습니다.

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps }) => fetchPosts(deps),
  // 이 라우트의 데이터를 언로드 후 캐시하지 않음
  gcTime: 0,
  // 사용자가 라우트로 이동하거나 의존성이 변경될 때만 다시 로드
  shouldReload: false,
})
```


### 캐싱을 사용하지 않으면서도 미리 가져오기 활용하기

라우트 데이터에 대한 단기 캐싱을 사용하지 않기로 결정했더라도, 미리 가져오기의 이점을 여전히 누릴 수 있습니다. 위의 설정을 사용하면 기본 `preloadGcTime`과 함께 미리 가져오기가 그대로 작동합니다. 즉, 라우트가 미리 가져오기되고 네비게이션으로 이동하면, 해당 라우트의 데이터는 최신 상태로 간주되어 다시 불러오지 않습니다.

미리 가져오기를 사용하지 않으려면, `routerOptions.defaultPreload` 또는 `routeOptions.preload` 옵션을 통해 활성화하지 않으면 됩니다.


## 모든 로더 이벤트를 외부 캐시로 전달하기

이 사용 사례는 [외부 데이터 로딩](./external-data-loading.md) 페이지에서 자세히 설명하지만, TanStack Query와 같은 외부 캐시를 사용하고 싶다면 모든 로더 이벤트를 외부 캐시로 전달할 수 있습니다. 기본 설정을 사용 중이라면, 라우터의 `defaultPreloadStaleTime` 옵션을 `0`으로 설정하는 것만 변경하면 됩니다.

```tsx
const router = createRouter({
  routeTree,
  defaultPreloadStaleTime: 0,
})
```

이렇게 하면 모든 미리 가져오기(preload), 로드(load), 그리고 다시 로드(reload) 이벤트가 `loader` 함수를 트리거하게 됩니다. 이 이벤트들은 외부 캐시에서 처리되고 중복 제거될 수 있습니다.


## 라우터 컨텍스트 사용하기

`loader` 함수에 전달되는 `context` 인자는 다음 두 가지를 병합한 객체입니다:

- 상위 라우트의 컨텍스트
- `beforeLoad` 옵션을 통해 제공된 현재 라우트의 컨텍스트

라우터의 최상단에서 `context` 옵션을 통해 초기 컨텍스트를 라우터에 전달할 수 있습니다. 이 컨텍스트는 라우터의 모든 라우트에서 사용 가능하며, 각 라우트가 매칭될 때 복사되고 확장됩니다. 이는 `beforeLoad` 옵션을 통해 라우트에 컨텍스트를 전달함으로써 이루어집니다. 이 컨텍스트는 해당 라우트의 모든 자식 라우트에서도 사용할 수 있습니다. 결과적으로 생성된 컨텍스트는 라우트의 `loader` 함수에서 사용 가능합니다.

이 예제에서는 라우트 컨텍스트에 게시물을 가져오는 함수를 만들고, `loader` 함수에서 이를 사용해 보겠습니다.

> 🧠 컨텍스트는 의존성 주입을 위한 강력한 도구입니다. 이를 통해 서비스, 훅, 그리고 다른 객체들을 라우터와 라우트에 주입할 수 있습니다. 또한 라우트의 `beforeLoad` 옵션을 사용해 라우트 트리의 모든 라우트에 데이터를 전달할 수도 있습니다.

- `/utils/fetchPosts.tsx`

```tsx
export const fetchPosts = async () => {
  const res = await fetch(`/api/posts?page=${pageIndex}`);
  if (!res.ok) throw new Error('게시물을 가져오는 데 실패했습니다');
  return res.json();
};
```

- `/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router';

// createRootRouteWithContext() 함수를 사용해 루트 라우트를 생성하고,
// 라우터 컨텍스트에서 사용할 타입을 전달합니다.
export const Route = createRootRouteWithContext<{
  fetchPosts: typeof fetchPosts;
}>()(); // NOTE: createRootRouteWithContext는 팩토리 함수이므로 두 번 호출합니다.
```

- `/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router';

// postsRoute가 컨텍스트에서 fetchPosts 함수를 참조하는 방식을 주목하세요.
// 이는 라우터와 라우트 간의 의존성 주입을 위한 강력한 도구가 될 수 있습니다.
export const Route = createFileRoute('/posts')({
  loader: ({ context: { fetchPosts } }) => fetchPosts(),
});
```

- `/router.tsx`

```tsx
import { routeTree } from './routeTree.gen';

// routerContext를 사용해 새로운 라우터를 생성합니다.
// 이때 라우터 컨텍스트의 타입 요구사항을 충족해야 합니다.
const router = createRouter({
  routeTree,
  context: {
    // 라우터 컨텍스트에 fetchPosts 함수를 제공합니다.
    fetchPosts,
  },
});
```


## 경로 파라미터 사용하기

`loader` 함수에서 경로 파라미터를 사용하려면, 함수의 매개변수에 있는 `params` 속성을 통해 접근할 수 있습니다. 아래 예제를 참고하세요:

```tsx
// routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: ({ params: { postId } }) => fetchPostById(postId),
})
```

이 예제에서는 `postId`라는 경로 파라미터를 사용하여 `fetchPostById` 함수를 호출합니다. `params` 객체에서 `postId`를 추출하여 사용하는 방식입니다.


## 라우트 컨텍스트 사용하기

전역 컨텍스트를 라우터에 전달하는 것은 좋지만, 특정 라우트에만 적용되는 컨텍스트를 제공하고 싶다면 어떻게 해야 할까요? 이때 `beforeLoad` 옵션이 유용합니다. `beforeLoad`는 라우트를 로드하기 직전에 실행되는 함수로, `loader`와 동일한 매개변수를 받습니다. 이 함수는 리다이렉트를 처리하거나 로더 요청을 차단하는 등의 기능 외에도, 라우트 컨텍스트에 병합될 객체를 반환할 수 있습니다. 아래 예제를 통해 `beforeLoad` 옵션을 사용해 라우트 컨텍스트에 데이터를 주입하는 방법을 살펴보겠습니다.

```tsx
// /routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // fetchPosts 함수를 라우트 컨텍스트에 전달
  beforeLoad: () => ({
    fetchPosts: () => console.info('foo'),
  }),
  loader: ({ context: { fetchPosts } }) => {
    console.info(fetchPosts()) // 'foo'

    // ...
  },
})
```

이 예제에서는 `beforeLoad` 함수가 `fetchPosts` 함수를 반환하고, 이 함수는 라우트 컨텍스트에 추가됩니다. 이후 `loader` 함수에서 이 컨텍스트를 사용해 `fetchPosts`를 호출할 수 있습니다.


## 로더에서 검색 파라미터 사용하기

> ❓ 잠깐만요, Tanner... 내 검색 파라미터는 어디에 있나요?!

여러분은 왜 `search`가 `loader` 함수의 매개변수에 직접적으로 제공되지 않는지 궁금할 수 있습니다. 이는 의도적으로 설계된 부분으로, 여러분이 더 나은 결과를 얻을 수 있도록 돕기 위함입니다. 그 이유를 살펴보겠습니다.

- **검색 파라미터**가 로더 함수에서 사용된다면, 이는 해당 검색 파라미터가 로드되는 데이터를 고유하게 식별하는 데에도 사용되어야 한다는 좋은 지표입니다. 예를 들어, `pageIndex`와 같은 검색 파라미터를 사용하는 라우트가 있다면, 이 파라미터는 라우트 매치 내부의 데이터를 고유하게 식별하는 데 사용됩니다. 또는 `/users/user` 라우트에서 `userId` 검색 파라미터를 사용하여 특정 사용자를 식별한다면, URL을 `/users/user?userId=123`과 같이 모델링할 수 있습니다. 이 경우 `user` 라우트는 특정 사용자를 식별하기 위해 추가적인 도움이 필요합니다.
- 로더 함수에서 검색 파라미터에 직접 접근하면, 캐싱 및 미리 가져오기(preloading)에서 버그가 발생할 수 있습니다. 이는 로드되는 데이터가 현재 URL 경로와 검색 파라미터에 고유하지 않기 때문입니다. 예를 들어, `/posts` 라우트에서 2페이지의 결과를 미리 가져오도록 요청할 수 있지만, 라우트 설정에서 페이지를 구분하지 않으면, `/posts` 또는 `?page=1` 화면에서 2페이지의 데이터를 가져와 저장하고 표시하게 됩니다. 이는 미리 가져오기가 배경에서 이루어지는 것이 아니라, 잘못된 화면에 표시되는 결과를 초래합니다.
- 검색 파라미터와 로더 함수 사이에 임계값을 두면, 라우터가 여러분의 의존성과 반응성을 이해할 수 있습니다.

```tsx
// /routes/users.user.tsx
export const Route = createFileRoute('/users/user')({
  validateSearch: (search) =>
    search as {
      userId: string
    },
  loaderDeps: ({ search: { userId } }) => ({
    userId,
  }),
  loader: async ({ deps: { userId } }) => getUser(userId),
})
```

이 코드는 `/users/user` 라우트에서 `userId` 검색 파라미터를 사용하여 특정 사용자 데이터를 가져오는 예제입니다. `validateSearch`를 통해 검색 파라미터를 검증하고, `loaderDeps`를 통해 의존성을 명시적으로 정의합니다. 이렇게 하면 라우터가 데이터를 올바르게 캐싱하고 미리 가져올 수 있습니다.


### `routeOptions.loaderDeps`를 통해 검색 파라미터 접근하기

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  // zod를 사용해 검색 파라미터를 검증하고 파싱
  validateSearch: z.object({
    offset: z.number().int().nonnegative().catch(0),
  }),
  // loaderDeps 함수를 통해 offset을 로더 의존성에 전달
  loaderDeps: ({ search: { offset } }) => ({ offset }),
  // 컨텍스트에서 offset을 가져와 로더 함수에서 사용
  loader: async ({ deps: { offset } }) =>
    fetchPosts({
      offset,
    }),
})
```

이 코드는 `/posts` 라우트를 정의하며, 검색 파라미터로 전달된 `offset` 값을 사용해 데이터를 불러오는 예제입니다. `validateSearch`를 통해 `offset` 값을 검증하고, `loaderDeps`를 통해 로더 함수에 필요한 의존성을 전달합니다. 마지막으로, `loader` 함수에서 `offset` 값을 사용해 데이터를 가져옵니다.


## Abort Signal 사용하기

`loader` 함수의 `abortController` 속성은 [AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)입니다. 이 시그널은 라우트가 언로드되거나 `loader` 호출이 더 이상 유효하지 않을 때 취소됩니다. 이 기능은 라우트가 언로드되거나 라우트의 파라미터가 변경될 때 네트워크 요청을 취소하는 데 유용합니다. 아래는 `fetch` 호출과 함께 사용하는 예제입니다:

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: ({ abortController }) =>
    fetchPosts({
      // 이 시그널을 fetch 호출이나 시그널을 지원하는 어떤 것에 전달
      signal: abortController.signal,
    }),
})
```


## `preload` 플래그 사용하기

`loader` 함수의 `preload` 속성은 라우트가 로드되는 대신 미리 로드될 때 `true`로 설정되는 불리언 값입니다. 일부 데이터 로딩 라이브러리는 일반적인 fetch와 다르게 미리 로드를 처리할 수 있으므로, 여러분은 `preload`를 데이터 로딩 라이브러리에 전달하거나 적절한 데이터 로딩 로직을 실행하는 데 사용할 수 있습니다:

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: async ({ preload }) =>
    fetchPosts({
      maxAge: preload ? 10_000 : 0, // 미리 로드된 데이터는 조금 더 오래 유지
    }),
})
```


## 느린 로더 처리하기

이상적으로는 대부분의 라우트 로더가 짧은 시간 내에 데이터를 해결할 수 있어야 합니다. 이렇게 되면 플레이스홀더 스피너를 렌더링할 필요 없이, 다음 라우트가 완전히 준비되었을 때 suspense를 통해 렌더링할 수 있습니다. 하지만 라우트 컴포넌트를 렌더링하는 데 필요한 중요한 데이터가 느리게 로드되는 경우, 두 가지 옵션이 있습니다:

1. 빠른 데이터와 느린 데이터를 별도의 Promise로 분리하고, 느린 데이터는 `defer`를 사용해 빠른 데이터가 로드된 후에 처리합니다. (자세한 내용은 [지연된 데이터 로딩](./deferred-data-loading.md) 가이드를 참고하세요.)
2. 모든 데이터가 준비될 때까지 낙관적인 suspense 임계값 이후에 보류 중인 컴포넌트를 표시합니다. (아래 내용을 참고하세요.)


## 로딩 중인 컴포넌트 표시하기

**기본적으로 TanStack Router는 1초 이상 걸리는 로더에 대해 로딩 중인 컴포넌트를 표시합니다.** 이는 낙관적인 임계값으로, 다음과 같은 방법으로 설정할 수 있습니다:

- `routeOptions.pendingMs` 또는
- `routerOptions.defaultPendingMs`

로딩 시간이 임계값을 초과하면, 라우터는 해당 라우트에 설정된 `pendingComponent` 옵션을 렌더링합니다.


## Pending 컴포넌트 깜빡임 방지

Pending 컴포넌트를 사용할 때, pending 시간 임계값에 도달한 직후 데이터가 바로 해결되면 pending 컴포넌트가 깜빡이는 현상이 발생할 수 있습니다. 이를 방지하기 위해 **TanStack Router는 기본적으로 pending 컴포넌트를 최소 500ms 동안 표시합니다**. 이 값은 낙관적인 임계값으로, 다음과 같은 방법으로 설정할 수 있습니다:

- `routeOptions.pendingMinMs` 또는
- `routerOptions.defaultPendingMinMs`


## 에러 처리하기

TanStack Router는 라우트 로딩 생명주기 동안 발생하는 에러를 처리하는 몇 가지 방법을 제공합니다. 이제 각 방법을 살펴보겠습니다.


### `routeOptions.onError`로 에러 처리하기

`routeOptions.onError` 옵션은 라우트 로딩 중 에러가 발생했을 때 호출되는 함수입니다.

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  onError: ({ error }) => {
    // 에러를 로그에 기록
    console.error(error)
  },
})
```


### `routeOptions.onCatch`를 사용한 에러 처리

`routeOptions.onCatch` 옵션은 라우터의 `CatchBoundary`에서 에러가 발생했을 때 호출되는 함수입니다.

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  onCatch: ({ error, errorInfo }) => {
    // 에러를 로그에 기록
    console.error(error)
  },
})
```


### `routeOptions.errorComponent`로 에러 처리하기

`routeOptions.errorComponent`는 라우트 로딩이나 렌더링 과정에서 에러가 발생했을 때 렌더링되는 컴포넌트입니다. 이 컴포넌트는 다음과 같은 프로퍼티를 받습니다:

- `error` - 발생한 에러
- `reset` - 내부 `CatchBoundary`를 리셋하는 함수

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error }) => {
    // 에러 메시지를 렌더링
    return {error.message}
  },
})
```

`reset` 함수를 사용하면 사용자가 에러 경계의 일반적인 자식들을 다시 렌더링하도록 할 수 있습니다:

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error, reset }) => {
    return (
      
        {error.message}
        <button
          onClick={() => {
            // 라우터 에러 경계를 리셋
            reset()
          }}
        >
          다시 시도
        </button>
      
    )
  },
})
```

만약 에러가 라우트 로딩 과정에서 발생했다면, `router.invalidate()`를 호출해야 합니다. 이 함수는 라우터를 다시 로드하고 에러 경계를 리셋하는 작업을 동시에 수행합니다:

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error, reset }) => {
    const router = useRouter()

    return (
      
        {error.message}
        <button
          onClick={() => {
            // 라우트를 무효화하여 로더를 다시 로드하고, 에러 경계도 리셋
            router.invalidate()
          }}
        >
          다시 시도
        </button>
      
    )
  },
})
```


### 기본 `ErrorComponent` 사용하기

TanStack Router는 라우트 로딩이나 렌더링 과정에서 오류가 발생했을 때 보여주는 기본 `ErrorComponent`를 제공합니다. 여러분이 라우트의 오류 컴포넌트를 커스텀하더라도, 잡히지 않은 오류는 기본 `ErrorComponent`로 폴백(fallback)하는 것이 좋습니다.

```tsx
// routes/posts.tsx
import { createFileRoute, ErrorComponent } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error }) => {
    if (error instanceof MyCustomError) {
      // 커스텀 오류 메시지 렌더링
      return {error.message}
    }

    // 기본 ErrorComponent로 폴백
    return <ErrorComponent error={error} />
  },
})
```


