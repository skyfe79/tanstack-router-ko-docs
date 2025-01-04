# 404 에러 처리

> ⚠️ 이 페이지는 404 에러를 처리하기 위한 새로운 `notFound` 함수와 `notFoundComponent` API에 대해 다룹니다. `NotFoundRoute` 라우트는 더 이상 사용되지 않으며, 향후 릴리스에서 제거될 예정입니다. 자세한 내용은 [`NotFoundRoute`에서 마이그레이션하기](#migrating-from-notfoundroute)를 참고하세요.


## 개요

TanStack Router에서 **not-found 오류**는 두 가지 상황에서 사용됩니다:

1. **일치하지 않는 라우트 경로**:
   - 경로가 알려진 라우트 패턴과 일치하지 않거나, 부분적으로 일치하지만 추가 경로 세그먼트가 있는 경우
   - **라우터**는 경로가 알려진 라우트 패턴과 일치하지 않을 때 자동으로 not-found 오류를 발생시킵니다.
   - 라우터의 `notFoundMode`가 `fuzzy`로 설정된 경우, 가장 가까운 상위 라우트 중 `notFoundComponent`가 있는 라우트가 오류를 처리합니다. `notFoundMode`가 `root`로 설정된 경우, 루트 라우트가 오류를 처리합니다.
   - 예시:
     - `/users` 라우트가 없을 때 `/users`에 접근하려는 경우
     - 라우트 트리가 `/posts/$postId`만 처리할 때 `/posts/1/edit`에 접근하려는 경우

2. **리소스 누락**:
   - 주어진 ID의 게시물이나 비동기 데이터와 같은 리소스를 찾을 수 없는 경우
   - **개발자**는 리소스를 찾을 수 없을 때 not-found 오류를 직접 발생시켜야 합니다. 이는 `beforeLoad` 또는 `loader` 함수에서 `notFound` 유틸리티를 사용해 수행할 수 있습니다.
   - 가장 가까운 상위 라우트 중 `notFoundComponent`가 있는 라우트 또는 루트 라우트가 오류를 처리합니다.
   - 예시:
     - ID가 1인 게시물이 없을 때 `/posts/1`에 접근하려는 경우
     - 문서가 존재하지 않을 때 `/docs/path/to/document`에 접근하려는 경우

내부적으로, 두 경우 모두 동일한 `notFound` 함수와 `notFoundComponent` API를 사용해 구현됩니다.


## `notFoundMode` 옵션

TanStack Router는 **경로명(pathname)**이 알려진 라우트 패턴과 일치하지 않거나, 라우트 패턴과 부분적으로 일치하지만 추가적인 경로명 세그먼트가 있는 경우, 자동으로 **not-found 오류**를 발생시킵니다.

`notFoundMode` 옵션에 따라, 라우터는 이러한 자동 오류를 다르게 처리합니다:

- ["fuzzy" 모드](#notfoundmode-fuzzy) (기본값): 라우터는 가장 가까운 적합한 라우트를 지능적으로 찾아 `notFoundComponent`를 표시합니다.
- ["root" 모드](#notfoundmode-root): 모든 not-found 오류는 가장 가까운 일치하는 라우트와 상관없이 루트 라우트의 `notFoundComponent`에 의해 처리됩니다.


### `notFoundMode: 'fuzzy'`

기본적으로 라우터의 `notFoundMode`는 `fuzzy`로 설정되어 있습니다. 이는 경로명이 알려진 라우트와 일치하지 않을 때, 라우터가 가장 가까운 일치하는 라우트를 찾아 자식 라우트(또는 아웃렛)와 설정된 `notFoundComponent`를 사용하려고 시도한다는 것을 의미합니다.

> **❓ 왜 이게 기본값일까요?** 사용자가 도착할 것으로 예상한 위치를 기반으로 유용한 위치로 이동할 수 있도록, 가능한 한 많은 부모 레이아웃을 보존하기 위해 퍼지 매칭을 사용합니다.

가장 가까운 적합한 라우트는 다음 기준에 따라 찾습니다:

- 라우트는 자식 라우트를 가지고 있어야 하며, 따라서 `notFoundComponent`를 렌더링할 `Outlet`이 있어야 합니다.
- 라우트는 `notFoundComponent`가 설정되어 있거나, 라우터에 `defaultNotFoundComponent`가 설정되어 있어야 합니다.

예를 들어, 다음 라우트 트리를 생각해 보겠습니다:

- `__root__` (`notFoundComponent`가 설정됨)
  - `posts` (`notFoundComponent`가 설정됨)
    - `$postId` (`notFoundComponent`가 설정됨)

만약 `/posts/1/edit` 경로를 제공하면, 다음과 같은 컴포넌트 구조가 렌더링됩니다:

- `<Root>`
  - `<Posts>`
    - `<Posts.notFoundComponent>`

`posts` 라우트의 `notFoundComponent`가 렌더링됩니다. 이는 **자식 라우트(또는 아웃렛)와 `notFoundComponent`가 설정된 가장 가까운 적합한 부모 라우트**이기 때문입니다.


### `notFoundMode: 'root'`

`notFoundMode`를 `root`로 설정하면, 모든 **찾을 수 없음(not-found)** 오류는 가장 가까운 퍼지 매칭(fuzzy-matched) 라우트에서 버블링(bubbling)되지 않고, 루트 라우트의 `notFoundComponent`에서 처리됩니다.

예를 들어, 다음과 같은 라우트 트리가 있다고 가정해 보겠습니다:

- `__root__` (`notFoundComponent`가 설정됨)
  - `posts` (`notFoundComponent`가 설정됨)
    - `$postId` (`notFoundComponent`가 설정됨)

만약 `/posts/1/edit` 경로로 접근한다면, 다음과 같은 컴포넌트 구조가 렌더링됩니다:

```html
<Root>
  <Root.notFoundComponent />
</Root>
```

`notFoundMode`가 `root`로 설정되어 있기 때문에, `__root__` 라우트의 `notFoundComponent`가 렌더링됩니다.


## 라우트의 `notFoundComponent` 설정하기

두 가지 유형의 찾을 수 없는 오류를 처리하기 위해 라우트에 `notFoundComponent`를 연결할 수 있습니다. 이 컴포넌트는 찾을 수 없는 오류가 발생했을 때 렌더링됩니다.

예를 들어, 존재하지 않는 설정 페이지를 처리하기 위해 `/settings` 라우트에 `notFoundComponent`를 설정하는 방법은 다음과 같습니다:

```tsx
export const Route = createFileRoute('/settings')({
  component: () => {
    return (
      <div>
        Settings page
      </div>
    )
  },
  notFoundComponent: () => {
    return <div>This setting page doesn't exist!</div>
  },
})
```

또는 존재하지 않는 게시물을 처리하기 위해 `/posts/$postId` 라우트에 `notFoundComponent`를 설정하는 방법은 다음과 같습니다:

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound()
    return { post }
  },
  component: ({ post }) => {
    return (
      <div>
        <h1>{post.title}</h1>
        <p>{post.body}</p>
      </div>
    )
  },
  notFoundComponent: () => {
    return <div>Post not found!</div>
  },
})
```


## 기본 라우터 전체 404 처리

여러분은 앱 내의 모든 라우트에 대해 기본 404 컴포넌트를 제공하고 싶을 수 있습니다. 특히 자식 라우트가 있는 경우에 유용합니다.

> 왜 자식 라우트가 있는 경우에만 적용할까요? **리프 노드 라우트(자식 라우트가 없는 라우트)는 `Outlet`을 렌더링하지 않기 때문에 404 오류를 처리할 수 없습니다.**

이를 구현하려면 `createRouter` 함수에 `defaultNotFoundComponent`를 전달하면 됩니다.

```tsx
const router = createRouter({
  defaultNotFoundComponent: () => {
    return (
      <div>
        <p>페이지를 찾을 수 없습니다!</p>
        <a href="/">홈으로 이동</a>
      </div>
    )
  },
})
```


## 직접 `notFound` 에러 던지기

로더 메서드와 컴포넌트에서 `notFound` 함수를 사용해 직접 **찾을 수 없음** 에러를 던질 수 있습니다. 이는 특정 리소스를 찾을 수 없음을 알릴 때 유용합니다.

`notFound` 함수는 `redirect` 함수와 비슷한 방식으로 동작합니다. **찾을 수 없음** 에러를 발생시키려면 `notFound()`를 던지면 됩니다.

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    // 포스트가 존재하지 않으면 `null` 반환
    const post = await getPost(postId)
    if (!post) {
      throw notFound()
      // 또는 `notFound` 함수가 에러를 던지도록 설정할 수도 있음:
      // notFound({ throw: true })
    }
    // 여기서는 포스트가 반드시 존재함 (에러를 던졌기 때문)
    return { post }
  },
})
```

위 코드에서 발생한 **찾을 수 없음** 에러는 `notFoundComponent` 라우트 옵션이나 `defaultNotFoundComponent` 라우터 옵션이 설정된 동일한 라우트 또는 가장 가까운 상위 라우트에서 처리됩니다.

에러를 처리할 수 있는 적절한 라우트나 상위 라우트가 없다면, 루트 라우트가 에러를 처리합니다. 이때 TanStack Router의 **극도로 단순한 (의도적으로 바람직하지 않은)** 기본 **찾을 수 없음** 컴포넌트가 사용됩니다. 이 컴포넌트는 단순히 `<div>Not Found</div>`를 렌더링합니다. 따라서 루트 라우트에 최소한 하나의 `notFoundComponent`를 추가하거나, 라우터 전체에 적용되는 `defaultNotFoundComponent`를 설정하여 **찾을 수 없음** 에러를 처리하는 것이 좋습니다.


## 특정 라우트에서 404 오류 처리하기

특정 부모 라우트에서 404 오류를 발생시키고, 일반적인 404 컴포넌트 전파를 우회하고 싶을 때가 있습니다. 이 경우 `notFound` 함수의 `route` 옵션에 라우트 ID를 전달하면 됩니다.

```tsx
// _layout.tsx
export const Route = createFileRoute('/_layout')({
  // 이 컴포넌트가 렌더링됨
  notFoundComponent: () => {
    return <div>Not found (in _layout)</div>
  },
  component: () => {
    return (
      <div>
        This is a layout!
      </div>
    )
  },
})

// _layout/a.tsx
export const Route = createFileRoute('/_layout/a')({
  loader: async () => {
    // 이렇게 하면 LayoutRoute가 404 오류를 처리함
    throw notFound({ routeId: '/_layout' })
    //                      ^^^^^^^^^ 등록된 라우터에서 자동 완성됨
  },
  // 이 컴포넌트는 렌더링되지 않음
  notFoundComponent: () => {
    return <div>Not found (in _layout/a)</div>
  },
})
```

위 예제에서 `_layout/a.tsx`의 `loader`에서 `notFound` 함수를 호출할 때 `routeId`를 `/_layout`으로 지정했습니다. 이렇게 하면 `_layout` 라우트가 404 오류를 처리하고, `_layout/a`의 `notFoundComponent`는 렌더링되지 않습니다.


### 루트 라우트를 수동으로 타겟팅하기

`notFound` 함수의 `route` 속성에 `rootRouteId` 변수를 전달하여 루트 라우트를 직접 타겟팅할 수도 있습니다:

```tsx
import { rootRouteId } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound({ routeId: rootRouteId })
    return { post }
  },
})
```

이 코드는 `postId`를 기반으로 게시물을 가져오고, 해당 게시물이 없을 경우 루트 라우트로 이동하도록 설정합니다.


### 컴포넌트에서 Not Found 에러 던지기

컴포넌트에서도 Not Found 에러를 던질 수 있습니다. 하지만 **로더 데이터의 타입을 정확히 지정하고 화면 깜빡임을 방지하려면, 컴포넌트 대신 로더 메서드에서 Not Found 에러를 던지는 것이 권장됩니다.**

TanStack Router는 `CatchBoundary`와 유사한 `CatchNotFound` 컴포넌트를 제공합니다. 이 컴포넌트를 사용하면 컴포넌트에서 발생한 Not Found 에러를 잡아 적절한 UI를 표시할 수 있습니다.


### `notFoundComponent` 내부에서 데이터 로딩하기

`notFoundComponent`는 데이터 로딩과 관련해 특별한 경우입니다. **`SomeRoute.useLoaderData`는 여러분이 접근하려는 라우트와 `not-found` 오류가 발생한 위치에 따라 정의되지 않을 수 있습니다.** 하지만 `Route.useParams`, `Route.useSearch`, `Route.useRouteContext` 등은 정의된 값을 반환합니다.

**만약 `notFoundComponent`에 불완전한 로더 데이터를 전달해야 한다면,** `notFound` 함수의 `data` 옵션을 통해 데이터를 전달하고, `notFoundComponent` 내부에서 이를 검증하세요.

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post)
      throw notFound({
        // notFoundComponent에 일부 데이터 전달
        // data: someIncompleteLoaderData
      })
    return { post }
  },
  // `notFound` 호출 시 `data` 옵션을 통해 `data: unknown`이 컴포넌트로 전달됨
  notFoundComponent: ({ data }) => {
    // ❌ useLoaderData는 여기서 유효하지 않음: const { post } = Route.useLoaderData()

    // ✅:
    const { postId } = Route.useParams()
    const search = Route.useSearch()
    const context = Route.useRouteContext()

    return Post with id {postId} not found!
  },
})
```


## SSR과 함께 사용하기

자세한 내용은 [SSR 가이드](./ssr.md)를 참고하세요.


## `NotFoundRoute`에서 마이그레이션하기

`NotFoundRoute` API는 더 이상 사용되지 않으며, 대신 `notFoundComponent`를 사용해야 합니다. `NotFoundRoute` API는 향후 릴리스에서 제거될 예정입니다.

**`notFound` 함수와 `notFoundComponent`는 `NotFoundRoute`를 사용할 때 작동하지 않습니다.**

주요 차이점은 다음과 같습니다:

- `NotFoundRoute`는 부모 라우트에 `<Outlet>`이 있어야 렌더링되는 라우트입니다. 반면 `notFoundComponent`는 어떤 라우트에든 연결할 수 있는 컴포넌트입니다.
- `NotFoundRoute`를 사용할 때는 레이아웃을 사용할 수 없습니다. `notFoundComponent`는 레이아웃과 함께 사용할 수 있습니다.
- `notFoundComponent`를 사용할 때는 경로 매칭이 엄격합니다. 예를 들어 `/post/$postId` 라우트가 있을 때, `/post/1/2/3`에 접근하면 not-found 오류가 발생합니다. `NotFoundRoute`를 사용하면 `/post/1/2/3`이 `NotFoundRoute`와 매칭되고, `<Outlet>`이 있는 경우에만 렌더링됩니다.

`NotFoundRoute`에서 `notFoundComponent`로 마이그레이션하려면 다음 단계를 따르세요:

1. not-found 오류를 처리해야 하는 라우트에서 `NotFoundRoute`를 `notFoundComponent`로 교체하세요. "전역" not-found 오류를 처리하려면 루트 라우트에 `notFoundComponent`를 연결하세요.
2. `NotFoundRoute`를 사용했던 라우트에서 `<Outlet>`을 제거하세요.

```jsx
// 이전: NotFoundRoute 사용
<Route path="/" element={<Layout />}>
  <Route path="posts" element={<Posts />} />
  <NotFoundRoute element={<NotFound />} />
</Route>

// 이후: notFoundComponent 사용
<Route path="/" element={<Layout />} notFoundComponent={<NotFound />}>
  <Route path="posts" element={<Posts />} />
</Route>
```


