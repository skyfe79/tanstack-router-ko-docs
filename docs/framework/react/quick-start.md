# Table of Contents

- [빠른 시작](#빠른-시작)
  - [파일 기반 라우트 생성 사용하기](#파일-기반-라우트-생성-사용하기)
    - [첫 번째 TanStack Router 프로젝트 시작하기](#첫-번째-tanstack-router-프로젝트-시작하기)
- [또는](#또는)
- [또는](#또는)
- [또는](#또는)
- [또는](#또는)
    - [Manual Setup](#manual-setup)
      - [TanStack Router, Vite 플러그인, 그리고 Router Devtools 설치하기](#tanstack-router-vite-플러그인-그리고-router-devtools-설치하기)
- [또는](#또는)
- [또는](#또는)
- [또는](#또는)
- [또는](#또는)
      - [Vite 플러그인 설정하기](#vite-플러그인-설정하기)
      - [`src/routes/__root.tsx`](#srcroutes__roottsx)
      - [`src/routes/index.lazy.tsx`](#srcroutesindexlazytsx)
      - [`src/routes/about.lazy.tsx`](#srcroutesaboutlazytsx)
      - [`src/main.tsx`](#srcmaintsx)
  - [코드 기반 라우트 설정 사용하기](#코드-기반-라우트-설정-사용하기)

# 빠른 시작

여러분이 서두르고 있어서 우리의 훌륭한 문서를 건너뛰고 싶다면, 파일 기반 라우트 생성과 코드 기반 라우트 설정을 모두 사용하여 TanStack Router를 시작하는 데 필요한 최소한의 내용을 소개합니다.


## 파일 기반 라우트 생성 사용하기

파일 기반 라우트 생성(Vite 및 기타 지원되는 번들러를 통해)은 TanStack Router를 사용하는 가장 권장되는 방법입니다. 이 방식은 최소한의 노력으로 최고의 경험, 성능, 그리고 편의성을 제공합니다.


### 첫 번째 TanStack Router 프로젝트 시작하기

```sh
npm create @tanstack/router@latest
# 또는
pnpm create @tanstack/router
# 또는
yarn create @tanstack/router
# 또는
bun create @tanstack/router
# 또는
deno init --npm @tanstack/router
```

프로젝트를 설정하기 위해 화면에 나타나는 안내를 따라주세요.


### Manual Setup





#### TanStack Router, Vite 플러그인, 그리고 Router Devtools 설치하기

```sh
npm install @tanstack/react-router
npm install -D @tanstack/router-plugin @tanstack/router-devtools
# 또는
pnpm add @tanstack/react-router
pnpm add -D @tanstack/router-plugin @tanstack/router-devtools
# 또는
yarn add @tanstack/react-router
yarn add -D @tanstack/router-plugin @tanstack/router-devtools
# 또는
bun add @tanstack/react-router
bun add -D @tanstack/router-plugin @tanstack/router-devtools
# 또는
deno add npm:@tanstack/react-router npm:@tanstack/router-plugin npm:@tanstack/router-devtools
```


#### Vite 플러그인 설정하기

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import viteReact from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    TanStackRouterVite(),
    viteReact(),
    // ...,
  ],
})
```

> [!TIP]
> Vite나 지원되는 번들러를 사용하지 않는다면, [파일 기반 라우팅](./guide/file-based-routing.md) 가이드를 참고하세요.

다음 파일들을 생성합니다:

- `src/routes/__root.tsx`
- `src/routes/index.lazy.tsx`
- `src/routes/about.lazy.tsx`
- `src/main.tsx`

모든 `routes/*.tsx` 파일은 **Route**라는 이름의 객체를 내보내야 합니다. 이 객체는 `createRootRoute` 또는 `createLazyFileRoute`를 사용해 생성됩니다.

> 🧠 `.lazy.tsx` 확장자를 가진 라우트 파일은 별도의 번들로 지연 로딩됩니다. 이를 통해 메인 번들 크기를 최대한 작게 유지할 수 있습니다.


#### `src/routes/__root.tsx`

```tsx
import { createRootRoute, Link, Outlet } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: () => (
    <>
      <div>
        <Link to="/">Home</Link>{' '}
        <Link to="/about">About</Link>
      </div>
      <hr />
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})
```

이 코드는 `@tanstack/react-router`를 사용하여 루트 라우트를 정의합니다. `createRootRoute` 함수를 통해 루트 라우트를 생성하고, `component` 속성에 컴포넌트를 정의합니다. 

- `Link` 컴포넌트는 클릭 시 해당 경로로 이동하는 링크를 생성합니다. `to` 속성에 이동할 경로를 지정합니다.
- `Outlet`은 중첩된 라우트의 컴포넌트를 렌더링하는 위치를 나타냅니다.
- `TanStackRouterDevtools`는 개발 중 라우터의 상태를 디버깅할 수 있는 도구입니다.

이 구조를 통해 홈(`/`)과 어바웃(`/about`) 페이지로 이동할 수 있는 기본적인 라우팅을 설정할 수 있습니다.


#### `src/routes/index.lazy.tsx`

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/')({
  component: Index,
})

function Index() {
  return (
    <div>
      Welcome Home!
    </div>
  )
}
```

이 코드는 `@tanstack/react-router` 라이브러리를 사용하여 라우트를 정의하고, 해당 라우트에 연결된 컴포넌트를 렌더링하는 예제입니다. `createLazyFileRoute` 함수를 통해 루트 경로(`/`)에 대한 라우트를 생성하고, `Index` 컴포넌트를 연결했습니다. `Index` 컴포넌트는 "Welcome Home!"이라는 메시지를 화면에 표시합니다.


#### `src/routes/about.lazy.tsx`

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/about')({
  component: About,
})

function About() {
  return <div>Hello from About!</div>
}
```

이 코드는 `/about` 경로에 대한 라우트를 정의하고, 해당 경로로 접근했을 때 `About` 컴포넌트를 렌더링합니다. `createLazyFileRoute`를 사용하여 라우트를 생성하고, `About` 컴포넌트는 간단한 메시지를 화면에 표시합니다.


#### `src/main.tsx`

`@tanstack/router-plugin` 패키지를 사용하든, 패키지 스크립트에서 `tsr watch` 또는 `tsr generate` 명령어를 수동으로 실행하든, `src/routeTree.gen.ts`에 다음 파일이 생성됩니다.

생성된 라우트 트리를 가져와 새로운 라우터 인스턴스를 만듭니다:

```tsx
import { StrictMode } from 'react'
import ReactDOM from 'react-dom/client'
import { RouterProvider, createRouter } from '@tanstack/react-router'

// 생성된 라우트 트리를 가져옵니다
import { routeTree } from './routeTree.gen'

// 새로운 라우터 인스턴스를 생성합니다
const router = createRouter({ routeTree })

// 타입 안전성을 위해 라우터 인스턴스를 등록합니다
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

// 앱을 렌더링합니다
const rootElement = document.getElementById('root')!
if (!rootElement.innerHTML) {
  const root = ReactDOM.createRoot(rootElement)
  root.render(
    <StrictMode>
      <RouterProvider router={router} />
    </StrictMode>
  )
}
```

이 패턴을 사용할 때는 `index.html` 파일의 루트 `<div>`의 `id`를 `<div id='root'></div>`로 변경해야 합니다.


## 코드 기반 라우트 설정 사용하기

> [!IMPORTANT]
> 아래 예제는 코드를 사용해 라우트를 설정하는 방법을 보여줍니다. 간단한 데모를 위해 모든 내용을 하나의 파일에 작성했지만, 코드 기반 생성 방식을 사용하면 많은 라우트와 라우터 인스턴스를 하나의 파일에서 선언할 수 있습니다. 그러나 애플리케이션이 커질수록 더 나은 조직화와 성능을 위해 라우트를 별도의 파일로 분리하는 것을 권장합니다.

```tsx
import { StrictMode } from 'react'
import ReactDOM from 'react-dom/client'
import {
  Outlet,
  RouterProvider,
  Link,
  createRouter,
  createRoute,
  createRootRoute,
} from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

// 루트 라우트 생성
const rootRoute = createRootRoute({
  component: () => (
    <>
      <nav>
        <Link to="/">Home</Link>{' '}
        <Link to="/about">About</Link>
      </nav>
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})

// 인덱스 라우트 생성
const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
  component: function Index() {
    return (
      <div>
        Welcome Home!
      </div>
    )
  },
})

// About 라우트 생성
const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/about',
  component: function About() {
    return <div>Hello from About!</div>
  },
})

// 라우트 트리 구성
const routeTree = rootRoute.addChildren([indexRoute, aboutRoute])

// 라우터 생성
const router = createRouter({ routeTree })

// 라우터 타입 등록
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

// 루트 엘리먼트에 렌더링
const rootElement = document.getElementById('app')!
if (!rootElement.innerHTML) {
  const root = ReactDOM.createRoot(rootElement)
  root.render(
    <StrictMode>
      <RouterProvider router={router} />
    </StrictMode>
  )
}
```

이 예제를 대충 훑어보거나 이해하지 못한 부분이 있다고 해도 걱정하지 마세요. TanStack Router를 제대로 활용하려면 배워야 할 것이 많기 때문입니다. 이제 다음 내용으로 넘어가 봅시다.


