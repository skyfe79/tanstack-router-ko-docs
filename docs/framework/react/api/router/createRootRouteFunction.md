# createRootRoute 함수

`createRootRoute` 함수는 새로운 루트 라우트 인스턴스를 반환합니다. 이 루트 라우트 인스턴스는 라우트 트리를 생성하는 데 사용할 수 있습니다.


## 루트 라우트 생성 옵션

루트 라우트 인스턴스를 구성하는 데 사용되는 옵션입니다.

- 타입:

```tsx
Omit<
  RouteOptions,
  | 'path'
  | 'id'
  | 'getParentRoute'
  | 'caseSensitive'
  | 'parseParams'
  | 'stringifyParams'
>
```

- [`RouteOptions`](./RouteOptionsType.md) 참조
- 선택 사항


## createRootRoute 반환값

새로운 [`Route`](./RouteType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { createRootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = createRootRoute({
  component: () => ,
  // ... 루트 라우트 옵션
})

const routeTree = rootRoute.addChildren([
  // ... 다른 라우트들
])

const router = createRouter({
  routeTree,
})
```


