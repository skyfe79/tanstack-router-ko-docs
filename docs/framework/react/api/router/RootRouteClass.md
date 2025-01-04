# RootRoute 클래스

> [!CAUTION]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 대신 [`createRootRoute`](./createRootRouteFunction.md) 함수를 사용해 주세요.

`RootRoute` 클래스는 `Route` 클래스를 상속받으며, 루트 라우트 인스턴스를 생성하는 데 사용됩니다. 루트 라우트 인스턴스는 라우트 트리를 만드는 데 활용할 수 있습니다.


## `RootRoute` 생성자

`RootRoute` 생성자는 객체를 유일한 인자로 받습니다.


### 생성자 옵션

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

- [`RouteOptions`](./RouteOptionsType.md)
- 선택 사항


## 생성자 반환 값

새로운 [`Route`](./RouteType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { RootRoute, createRouter, Outlet } from '@tanstack/react-router'

const rootRoute = new RootRoute({
  component: () => ,
  // 루트 라우트 옵션
})

const routeTree = rootRoute.addChildren([
  // 다른 라우트들
])

const router = createRouter({
  routeTree,
})
```


