# Table of Contents

- [createLazyRoute 함수](#createlazyroute-함수)
  - [createLazyRoute 옵션](#createlazyroute-옵션)
    - [`id`](#id)
    - [createLazyRoute 반환값](#createlazyroute-반환값)
    - [예제](#예제)

# createLazyRoute 함수

`createLazyRoute` 함수는 매칭될 때 지연 로딩되는 부분적인 코드 기반 라우트 인스턴스를 생성하는 데 사용됩니다. 이 라우트 인스턴스는 `component`, `pendingComponent`, `errorComponent`, `notFoundComponent`와 같은 [비중요 속성](../../guide/code-splitting.md#how-does-tanstack-router-split-code)만 설정하는 데 사용할 수 있습니다.


## createLazyRoute 옵션

`createLazyRoute` 함수는 라우트의 `id`를 나타내는 `string` 타입의 단일 인자를 받습니다.


### `id`

- 타입: `string`
- 필수 여부: 필수
- 설명: 라우트의 고유 식별자입니다.


### createLazyRoute 반환값

`createLazyRoute`는 [`RouteOptions`](./RouteOptionsType.md) 타입의 일부를 인자로 받는 새로운 함수를 반환합니다. 이 인자는 파일 기반 [`Route`](./RouteType.md) 인스턴스를 구성하는 데 사용됩니다.

- 타입:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 주의: 이 라우트 인스턴스는 `createRoute` 함수가 반환한 `lazy` 메서드를 사용해 해당 핵심 라우트 인스턴스에 대해 수동으로 지연 로딩해야 합니다.


### 예제

```tsx
// src/route-pages/index.tsx
import { createLazyRoute } from '@tanstack/react-router'

export const Route = createLazyRoute('/')({
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return {data}
}

// src/routeTree.tsx
import {
  createRootRouteWithContext,
  createRoute,
  Outlet,
} from '@tanstack/react-router'

interface MyRouterContext {
  foo: string
}

const rootRoute = createRootRouteWithContext()({
  component: () => ,
})

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
}).lazy(() => import('./route-pages/index').then((d) => d.Route))

export const routeTree = rootRoute.addChildren([indexRoute])
```

이 예제는 `@tanstack/react-router`를 사용하여 라우트를 설정하는 방법을 보여줍니다. `createLazyRoute`를 통해 지연 로딩되는 라우트를 생성하고, `createRootRouteWithContext`를 사용하여 루트 라우트를 정의합니다. 이후 `addChildren` 메서드를 통해 자식 라우트를 추가합니다.


