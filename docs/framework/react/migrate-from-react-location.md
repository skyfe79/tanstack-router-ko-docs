# Table of Contents

- [React Location에서 마이그레이션하기](#react-location에서-마이그레이션하기)
  - [React Location과 TanStack Router의 차이점](#react-location과-tanstack-router의-차이점)
  - [마이그레이션 가이드](#마이그레이션-가이드)
    - [2단계: 파일 기반 라우팅 감시기 사용하기](#2단계-파일-기반-라우팅-감시기-사용하기)
    - [3단계: 프로젝트에 파일 기반 설정 파일 추가하기](#3단계-프로젝트에-파일-기반-설정-파일-추가하기)
    - [4단계: routes 디렉토리 생성하기](#4단계-routes-디렉토리-생성하기)
    - [5단계: 루트 라우트 파일 생성하기](#5단계-루트-라우트-파일-생성하기)
    - [6단계: 인덱스 라우트 파일 생성하기](#6단계-인덱스-라우트-파일-생성하기)
    - [7단계: posts 라우트 파일 생성하기](#7단계-posts-라우트-파일-생성하기)
    - [8단계: 게시물 목록 라우트 파일 생성하기](#8단계-게시물-목록-라우트-파일-생성하기)
    - [9단계: 게시물 ID 라우트 파일 생성하기](#9단계-게시물-id-라우트-파일-생성하기)
    - [10단계: 라우트 트리 생성하기](#10단계-라우트-트리-생성하기)
    - [11단계: 라우터를 렌더링하기 위해 메인 엔트리 파일 업데이트](#11단계-라우터를-렌더링하기-위해-메인-엔트리-파일-업데이트)
    - [마이그레이션 완료!](#마이그레이션-완료)

# React Location에서 마이그레이션하기

React Location에서 마이그레이션을 시작하기 전에, TanStack Router에서 사용하는 [라우팅 개념](./guide/routing-concepts.md)과 [디자인 결정](./decisions-on-dx.md)을 잘 이해하는 것이 중요합니다.


## React Location과 TanStack Router의 차이점

React Location과 TanStack Router는 많은 설계 개념을 공유하지만, 몇 가지 중요한 차이점이 있습니다. 이 차이점을 이해하는 것이 중요합니다.

- **타입 추론 방식**: React Location은 `제네릭(generics)`을 사용하여 라우트 타입을 추론하는 반면, TanStack Router는 `모듈 선언 병합(module declaration merging)`을 사용하여 타입을 추론합니다.
  
- **라우트 구성 방식**: React Location은 라우트 정의를 단일 배열로 구성합니다. 반면 TanStack Router는 [루트 라우트](./guide/routing-concepts.md#the-root-route)로 시작하는 트리 구조로 라우트를 정의합니다.

- **라우트 정의 방식**: TanStack Router는 [파일 기반 라우팅](./guide/file-based-routing.md)을 권장합니다. React Location은 코드 기반 접근 방식을 사용하여 단일 파일에서만 라우트를 정의할 수 있습니다.
  - TanStack Router도 [코드 기반 접근 방식](./guide/code-based-routing.md)을 지원하지만, 대부분의 경우 파일 기반 라우팅을 권장합니다. 이에 대한 자세한 내용은 [파일 기반 라우팅이 선호되는 이유](./decisions-on-dx.md#3-why-is-file-based-routing-the-preferred-way-to-define-routes)에서 확인할 수 있습니다.

이러한 차이점을 이해하면 프로젝트에 적합한 라우팅 라이브러리를 선택하는 데 도움이 됩니다.


## 마이그레이션 가이드

이 가이드에서는 [React Location Basic 예제](https://github.com/TanStack/router/tree/react-location/examples/basic)를 파일 기반 라우팅을 사용하여 TanStack Router로 마이그레이션하는 과정을 설명합니다. 최종 목표는 원래 예제와 동일한 기능을 구현하는 것입니다. (스타일링 및 라우팅과 관련 없는 코드는 생략합니다.)

> [!TIP]
> 코드 기반으로 라우트를 정의하려면 [코드 기반 라우팅 가이드](./guide/code-based-routing.md)를 참고하세요.


###


### 2단계: 파일 기반 라우팅 감시기 사용하기

여러분의 프로젝트가 Vite(또는 지원되는 번들러 중 하나)를 사용한다면, TanStack Router 플러그인을 사용하여 라우트 파일의 변경 사항을 감시하고 자동으로 라우트 구성을 업데이트할 수 있습니다.

Vite 플러그인 설치 방법:

```sh
npm install -D @tanstack/router-plugin
```

그리고 `vite.config.js`에 추가합니다:

```js
import { defineConfig } from 'vite'
import viteReact from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  // ...
  plugins: [TanStackRouterVite(), viteReact()],
})
```

하지만, 여러분의 애플리케이션이 Vite를 사용하지 않는다면, [지원되는 다른 번들러](./guide/file-based-routing.md#installation) 중 하나를 사용하거나, `@tanstack/router-cli` 패키지를 사용하여 라우트 파일의 변경 사항을 감시하고 자동으로 라우트 구성을 업데이트할 수 있습니다.


### 3단계: 프로젝트에 파일 기반 설정 파일 추가하기

프로젝트 루트에 `tsr.config.json` 파일을 생성하고, 아래 내용을 추가합니다.

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts"
}
```

`tsr.config.json` 파일의 전체 옵션 목록은 [여기](./guide/file-based-routing.md#options)에서 확인할 수 있습니다.


### 4단계: routes 디렉토리 생성하기

프로젝트의 `src` 디렉토리 안에 `routes` 디렉토리를 생성합니다.

```sh
mkdir src/routes
```


### 5단계: 루트 라우트 파일 생성하기

```tsx
// src/routes/__root.tsx
import { createRootRoute, Outlet, Link } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: () => {
    return (
      <>
        <nav>
          <Link to="/">Home</Link>
          <Link to="/posts">Posts</Link>
        </nav>
        <Outlet />
        <TanStackRouterDevtools />
      </>
    )
  },
})
```

이 코드는 루트 라우트를 생성하는 예제입니다. `createRootRoute`를 사용하여 루트 라우트를 정의하고, `component` 함수 내에서 네비게이션 링크와 `Outlet`을 렌더링합니다. `Outlet`은 하위 라우트의 컴포넌트를 렌더링하는 역할을 합니다. 또한, `TanStackRouterDevtools`를 사용하여 라우터 개발 도구를 추가했습니다.


### 6단계: 인덱스 라우트 파일 생성하기

```tsx
// src/routes/index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: Index,
})
```

> `src/index.tsx` 파일에서 인덱스 라우트와 관련된 컴포넌트와 로직을 `src/routes/index.tsx` 파일로 옮겨야 합니다.


### 7단계: posts 라우트 파일 생성하기

```tsx
// src/routes/posts.tsx
import { createFileRoute, Link, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: Posts,
  loader: async () => {
    const posts = await fetchPosts()
    return {
      posts,
    }
  },
})

function Posts() {
  const { posts } = Route.useLoaderData()
  return (
    <div>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <Link
              to={`/posts/$postId`}
              params={{ postId: post.id }}
            >
              {post.title}
            </Link>
          </li>
        ))}
      </ul>
      <Outlet />
    </div>
  )
}
```

> `src/index.tsx` 파일에서 posts 라우트와 관련된 컴포넌트와 로직을 `src/routes/posts.tsx` 파일로 옮겨야 합니다.


### 8단계: 게시물 목록 라우트 파일 생성하기

```tsx
// src/routes/posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  component: PostsIndex,
})
```

> `src/index.tsx` 파일에서 게시물 목록 라우트와 관련된 컴포넌트와 로직을 `src/routes/posts.index.tsx` 파일로 옮겨야 합니다.


### 9단계: 게시물 ID 라우트 파일 생성하기

```tsx
// src/routes/posts.$postId.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  component: PostsId,
  loader: async ({ params: { postId } }) => {
    const post = await fetchPost(postId)
    return {
      post,
    }
  },
})

function PostsId() {
  const { post } = Route.useLoaderData()
  // ...
}
```

> `src/index.tsx` 파일에서 게시물 ID 라우트와 관련된 컴포넌트와 로직을 `src/routes/posts.$postId.tsx` 파일로 옮겨야 합니다.


### 10단계: 라우트 트리 생성하기

지원되는 번들러를 사용 중이라면, 개발 스크립트를 실행할 때 자동으로 라우트 트리가 생성됩니다.

지원되는 번들러를 사용하지 않는 경우, 다음 커맨드를 실행하여 라우트 트리를 생성할 수 있습니다:

```sh
npx tsr generate
```


### 11단계: 라우터를 렌더링하기 위해 메인 엔트리 파일 업데이트

라우트 트리를 생성한 후, `src/index.tsx` 파일을 업데이트하여 라우터 인스턴스를 생성하고 렌더링할 수 있습니다.

```tsx
// src/index.tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { createRouter, RouterProvider } from '@tanstack/react-router'

// 생성된 라우트 트리를 가져옴
import { routeTree } from './routeTree.gen'

// 새로운 라우터 인스턴스 생성
const router = createRouter({ routeTree })

// 타입 안전성을 위해 라우터 인스턴스 등록
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

const domElementId = 'root' // 'root' id를 가진 루트 엘리먼트가 있다고 가정

// 앱 렌더링
const rootElement = document.getElementById(domElementId)
if (!rootElement) {
  throw new Error(`Element with id ${domElementId} not found`)
}

ReactDOM.createRoot(rootElement).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
)
```

이 코드는 `@tanstack/react-router`를 사용하여 라우터를 생성하고, 이를 `RouterProvider`를 통해 렌더링합니다. `React.StrictMode`는 개발 중에 잠재적인 문제를 찾는 데 도움을 줍니다.


### 마이그레이션 완료!

이제 여러분은 파일 기반 라우팅을 사용해 React Location에서 TanStack Router로 애플리케이션을 성공적으로 마이그레이션했을 것입니다.

React Location에는 애플리케이션에서 사용 중인 몇 가지 추가 기능이 있을 수 있습니다. 해당 기능을 마이그레이션하는 데 도움이 되는 가이드입니다:

- [검색 파라미터](./guide/search-params.md)
- [데이터 로딩](./guide/data-loading.md)
- [히스토리 타입](./guide/history-types.md)
- [와일드카드 / 스플랫 / 캐치-올 라우트](./guide/routing-concepts.md#splat--catch-all-routes)
- [인증된 라우트](./guide/authenticated-routes.md)

TanStack Router에는 탐색해 볼 만한 몇 가지 추가 기능도 있습니다:

- [라우터 컨텍스트](./guide/router-context.md)
- [미리 로딩](./guide/preloading.md)
- [경로 없는 / 레이아웃 라우트](./guide/routing-concepts.md#pathless--layout-routes)
- [라우트 마스킹](./guide/route-masking.md)
- [SSR](./guide/ssr.md)
- ... 그리고 더 많은 기능들!

문제가 발생하거나 궁금한 점이 있다면, TanStack Discord에서 도움을 요청하세요.


