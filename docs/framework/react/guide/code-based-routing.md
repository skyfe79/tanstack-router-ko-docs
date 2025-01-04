# Table of Contents

- [Code-Based Routing](#code-based-routing)
  - [⚠️ 시작하기 전에](#-시작하기-전에)
  - [라우트 트리](#라우트-트리)
  - [파일 기반 라우팅 vs 코드 기반 라우팅](#파일-기반-라우팅-vs-코드-기반-라우팅)
  - [루트 라우트 생성하기](#루트-라우트-생성하기)
  - [라우트의 구조](#라우트의-구조)
    - [`getParentRoute` 옵션](#getparentroute-옵션)
    - [`path` 옵션](#path-옵션)
      - [선행/후행 슬래시](#선행후행-슬래시)
  - [라우트 트리를 수동으로 구성하기](#라우트-트리를-수동으로-구성하기)
  - [정적 라우트](#정적-라우트)
  - [인덱스 라우트](#인덱스-라우트)
  - [동적 라우트 세그먼트](#동적-라우트-세그먼트)
  - [Splat / Catch-All Routes (스플랫/캐치올 라우트)](#splat--catch-all-routes-스플랫캐치올-라우트)
  - [경로 없는 라우트(Pathless Routes)](#경로-없는-라우트pathless-routes)
  - [중첩되지 않은 라우트](#중첩되지-않은-라우트)
  - [404 / `NotFoundRoute` 설정](#404--notfoundroute-설정)

# Code-Based Routing





## ⚠️ 시작하기 전에

- [파일 기반 라우팅](./file-based-routing.md)을 사용 중이라면, **이 가이드는 건너뛰세요**.
- TanStack Router를 처음 사용한다면, **파일 기반 라우팅이 TanStack Router를 설정하는 권장 방식**이라는 점을 알아두세요. 어떤 방식을 사용할지 확실하지 않다면, 먼저 [파일 기반 라우팅](./file-based-routing.md) 가이드를 읽어보세요.
- 여전히 코드 기반 라우팅을 사용하려면, 반드시 [파일 기반 라우팅](./file-based-routing.md) 가이드를 먼저 읽어야 합니다. 해당 가이드에는 여기서 다루지 않는 라우터의 핵심 개념도 포함되어 있습니다.


## 라우트 트리

코드 기반 라우팅은 파일 기반 라우팅과 마찬가지로 라우트 트리 개념을 사용하여 라우트를 구성하고, 매칭하며, 매칭된 라우트를 컴포넌트 트리로 합성합니다. 유일한 차이점은 파일 시스템을 사용하는 대신 코드를 사용하여 라우트를 구성한다는 점입니다.

[라우트 트리 & 중첩](./route-trees.md#route-trees) 가이드에서 사용한 동일한 라우트 트리를 코드 기반 라우팅으로 변환해 보겠습니다.

다음은 파일 기반 버전입니다:

```
routes/
├── __root.tsx
├── index.tsx
├── about.tsx
├── posts/
│   ├── index.tsx
│   ├── $postId.tsx
├── posts.$postId.edit.tsx
├── settings/
│   ├── profile.tsx
│   ├── notifications.tsx
├── _layout.tsx
├── _layout/
│   ├── layout-a.tsx
├── ├── layout-b.tsx
├── files/
│   ├── $.tsx
```

다음은 요약된 코드 기반 버전입니다:

```tsx
import { createRootRoute, createRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})

const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})

const postRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
})

const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts/$postId/edit',
})

const settingsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'settings',
})

const profileRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'profile',
})

const notificationsRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'notifications',
})

const layoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'layout',
})

const layoutARoute = createRoute({
  getParentRoute: () => layoutRoute,
  path: 'layout-a',
})

const layoutBRoute = createRoute({
  getParentRoute: () => layoutRoute,
  path: 'layout-b',
})

const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files/$',
})
```


## 파일 기반 라우팅 vs 코드 기반 라우팅

믿기 어렵겠지만, 파일 기반 라우팅은 사실 코드 기반 라우팅의 상위 집합입니다. 파일 시스템과 약간의 코드 생성 추상화를 사용하여 위에서 본 구조를 자동으로 생성합니다.

여러분이 [파일 기반 라우팅](./file-based-routing.md) 가이드를 읽고 다음 주요 개념들에 익숙하다고 가정하겠습니다:

- 루트 라우트
- 정적 라우트
- 인덱스 라우트
- 동적 라우트 세그먼트
- 스플랫 / 캐치-올 라우트
- 경로 없는 라우트
- 중첩되지 않은 라우트
- 404 라우트

**이제 각 라우트 타입을 코드로 어떻게 생성하는지 살펴보겠습니다.**


## 루트 라우트 생성하기

코드 기반 라우팅에서 루트 라우트를 만드는 방법은 파일 기반 라우팅과 동일합니다. `createRootRoute()` 함수를 호출하면 됩니다.

하지만 파일 기반 라우팅과 달리, 루트 라우트를 꼭 내보낼 필요는 없습니다. 전체 라우트 트리와 애플리케이션을 하나의 파일에 작성하는 것은 권장하지 않습니다. (예제에서는 라우팅 개념을 간단히 보여주기 위해 이렇게 작성하긴 했습니다.)

```tsx
import { createRootRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()
```

> 🧠 `createRootRouteWithContext<TContext>()` 함수를 사용해 루트 라우트를 생성할 수도 있습니다. 이 방법은 전체 라우터에 대해 타입 안전한 방식으로 의존성 주입을 수행합니다. 자세한 내용은 [컨텍스트 섹션](./router-context.md)에서 확인할 수 있습니다.


## 라우트의 구조

루트 라우트를 제외한 모든 라우트는 `createRoute` 함수를 사용하여 설정합니다:

```tsx
const route = createRoute({
  getParentRoute: () => rootRoute, // 부모 라우트 지정
  path: '/posts', // 라우트 경로
  component: PostsComponent, // 해당 라우트에서 사용할 컴포넌트
})
```

이 코드는 `/posts` 경로에 접근했을 때 `PostsComponent`를 렌더링하도록 설정합니다. `getParentRoute` 함수는 현재 라우트의 부모 라우트를 지정하며, 여기서는 `rootRoute`를 부모로 설정했습니다.


### `getParentRoute` 옵션

`getParentRoute` 옵션은 여러분이 생성하는 라우트의 부모 라우트를 반환하는 함수입니다.

**❓❓❓ "잠깐만, 모든 라우트를 만들 때마다 부모 라우트를 전달해야 한다고?"**

그렇습니다! 부모 라우트를 전달해야 하는 이유는 **TanStack Router의 마법 같은 타입 안전성**과 깊은 관련이 있습니다. 부모 라우트가 없다면, TypeScript는 여러분의 라우트에 어떤 타입을 제공해야 할지 전혀 알 수 없습니다!


### `path` 옵션

**루트 라우트나 경로가 없는 라우트가 아닌 모든 라우트**에는 `path` 옵션이 필수입니다. 이 옵션은 URL 경로와 매칭되어 해당 라우트가 일치하는지 여부를 결정합니다.


#### 선행/후행 슬래시

코드를 통해 라우트를 설정할 때, 라우트 경로는 선행 및 후행 슬래시를 무시합니다. (단, "index" 라우트 경로인 `/`는 제외됩니다.) 여러분이 원한다면 슬래시를 포함할 수 있지만, TanStack Router가 내부적으로 이를 정규화합니다. 다음은 유효한 경로와 그들이 정규화된 결과를 보여주는 표입니다:

| 경로     | 정규화된 경로 |
| -------- | --------------- |
| `/`      | `/`             |
| `/about` | `about`         |
| `about/` | `about`         |
| `about`  | `about`         |
| `$`      | `$`             |
| `/$`     | `$`             |
| `/$/`    | `$`             |


## 라우트 트리를 수동으로 구성하기

코드로 라우트 트리를 구성할 때, 각 라우트의 부모 라우트를 정의하는 것만으로는 충분하지 않습니다. 파일 기반 라우팅과 달리 라우트 트리가 자동으로 구성되지 않기 때문에, 각 라우트를 부모 라우트의 `children` 배열에 추가하여 최종 라우트 트리를 직접 구성해야 합니다.

```tsx
/* prettier-ignore */
const routeTree = rootRoute.addChildren([
  indexRoute,
  aboutRoute,
  postsRoute.addChildren([
    postsIndexRoute,
    postRoute,
  ]),
  postEditorRoute,
  settingsRoute.addChildren([
    profileRoute,
    notificationsRoute,
  ]),
  layoutRoute.addChildren([
    layoutARoute,
    layoutBRoute,
  ]),
  filesRoute.addChildren([
    fileRoute,
  ]),
])
/* prettier-ignore-end */
```


## 정적 라우트

정적 라우트를 생성하려면 `createRoute` 함수에 일반적인 `path` 문자열을 전달하면 됩니다:

```tsx
const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})
```


## 인덱스 라우트

파일 기반 라우팅에서는 `index` 파일명을 사용해 인덱스 라우트를 표시하지만, 코드 기반 라우팅에서는 단일 슬래시 `/`를 사용해 인덱스 라우트를 나타냅니다. 예를 들어, 위의 예제 라우트 트리에서 `posts.index.tsx` 파일은 코드 기반 라우팅에서 다음과 같이 표현됩니다:

```tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  // 여기서 단일 슬래시 `/`를 주목하세요
  path: '/',
})
```


## 동적 라우트 세그먼트

동적 라우트 세그먼트는 파일 기반 라우팅과 코드 기반 라우팅에서 동일하게 작동합니다. 경로의 세그먼트 앞에 `$`를 붙이면, 해당 세그먼트는 라우트의 `loader`나 `컴포넌트`의 `params` 객체에 캡처됩니다.

```tsx
const postIdRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
  // 로더에서 사용
  loader: ({ params }) => fetchPost(params.postId),
  // 컴포넌트에서 사용
  component: PostComponent,
})

function PostComponent() {
  const { postId } = postIdRoute.useParams()
  return Post ID: {postId}
}
```

> [!TIP]
> 컴포넌트가 코드 분할(code-split)된 경우, [getRouteApi 함수](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)를 사용하여 `postIdRoute` 설정을 임포트하지 않고도 타입이 지정된 `useParams()` 훅에 접근할 수 있습니다.


## Splat / Catch-All Routes (스플랫/캐치올 라우트)

코드 기반 라우팅에서도 파일 기반 라우팅과 마찬가지로 스플랫/캐치올 라우트가 동일하게 작동합니다. 경로의 세그먼트 앞에 `$`를 붙이면, 해당 부분이 `params` 객체의 `_splat` 키로 캡처됩니다.

```tsx
const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files',
})

const fileRoute = createRoute({
  getParentRoute: () => filesRoute,
  path: '$',
})
```

예를 들어, URL이 `/documents/hello-world`인 경우, `params` 객체는 다음과 같이 표시됩니다:

```js
{
  '_splat': 'documents/hello-world'
}
```

이렇게 하면 동적 경로를 쉽게 처리할 수 있습니다.


## 경로 없는 라우트(Pathless Routes)

파일 기반 라우팅에서는 경로 없는 라우트를 `_`로 시작하지만, 코드 기반 라우팅에서는 `path` 옵션 대신 `id`를 사용합니다. 코드 기반 라우팅은 파일 시스템을 사용하지 않기 때문에 경로가 없음을 나타내기 위해 `_`를 붙일 필요가 없습니다.

```tsx
const layoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'layout',
  component: LayoutComponent,
})

const layoutARoute = createRoute({
  getParentRoute: () => layoutRoute,
  path: 'layout-a',
})

const layoutBRoute = createRoute({
  getParentRoute: () => layoutRoute,
  path: 'layout-b',
})

const routeTree = rootRoute.addChildren([
  // layoutRoute는 경로가 없고 id만 있음
  // 따라서 자식 라우트는 layoutRoute 아래에 중첩됨
  layoutRoute.addChildren([layoutARoute, layoutBRoute]),
])
```

이제 `/layout-a`와 `/layout-b`는 모두 `LayoutComponent` 안에 내용을 렌더링합니다.

```tsx
// URL: /layout-a

  

// URL: /layout-b

  
```


## 중첩되지 않은 라우트

코드 기반 라우팅에서 중첩되지 않은 라우트를 구성할 때는 경로 끝에 `_`를 사용할 필요가 없습니다. 하지만 올바른 경로와 중첩 구조로 라우트와 라우트 트리를 구성해야 합니다. 예를 들어, 게시물 편집기가 게시물 라우트 아래에 중첩되지 않도록 하려는 경우를 생각해봅시다:

- `/posts_/$postId/edit`
- `/posts`
  - `$postId`

이를 구현하려면 게시물 편집기를 위한 별도의 라우트를 만들고, 해당 라우트가 중첩될 루트부터 전체 경로를 `path` 옵션에 포함시켜야 합니다. 이 경우 루트에서 시작합니다:

```tsx
// 게시물 편집기 라우트는 루트 라우트 아래에 중첩됨
const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  // 경로에는 매칭할 전체 경로가 포함됨
  path: 'posts/$postId/edit',
})

const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
})

const routeTree = rootRoute.addChildren([
  // 게시물 편집기 라우트는 루트 라우트 아래에 중첩됨
  postEditorRoute,
  postsRoute.addChildren([postRoute]),
])
```


## 404 / `NotFoundRoute` 설정

`NotFoundRoute`를 구성하는 방법은 [Not Found Errors](./not-found-errors.md) 가이드에서 다룹니다.


