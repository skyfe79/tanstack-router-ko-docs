# Creating a Router





## `Router` 클래스

라우터를 사용할 준비가 되면, 새로운 `Router` 인스턴스를 생성해야 합니다. 이 라우터 인스턴스는 TanStack Router의 핵심이며, 라우트 트리 관리, 라우트 매칭, 네비게이션 및 라우트 트랜지션 조정을 담당합니다. 또한 라우터 전반의 설정을 구성하는 역할도 합니다.

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
})
```


## 라우트 트리

`Router` 생성자에는 `routeTree` 옵션이 필요하다는 것을 금방 알아차릴 수 있습니다. 이 라우트 트리는 라우터가 라우트를 매칭하고 컴포넌트를 렌더링하는 데 사용됩니다.

여러분이 [파일 기반 라우팅](./route-trees.md)을 사용하든 [코드 기반 라우팅](./code-based-routing.md)을 사용하든, `createRouter` 함수에 라우트 트리를 전달해야 합니다:

```javascript
const router = createRouter({
  routeTree: myRouteTree,
})
```


### 파일 시스템 기반 라우트 트리

여러분이 권장하는 파일 기반 라우팅을 사용했다면, 생성된 라우트 트리 파일이 기본 위치인 `src/routeTree.gen.ts`에 만들어졌을 가능성이 높습니다. 만약 커스텀 위치를 사용했다면, 해당 위치에서 라우트 트리를 임포트해야 합니다.

```tsx
import { routeTree } from './routeTree.gen'
```


### 코드 기반 라우트 트리

코드 기반 라우팅을 사용했다면, 여러분은 루트 라우트의 `addChildren` 메서드를 사용해 라우트 트리를 직접 생성했을 것입니다:

```tsx
const routeTree = rootRoute.addChildren([
  // ...
])
```


## 라우터 타입 안전성

> [!중요]
> 이 섹션을 건너뛰지 마세요! ⚠️

TanStack Router는 라이브러리에서 직접 임포트하는 것과 같은 예상치 못한 부분까지도 TypeScript를 훌륭하게 지원합니다. 이를 가능하게 하려면 TypeScript의 [선언 병합(Declaration Merging)](https://www.typescriptlang.org/docs/handbook/declaration-merging.html) 기능을 사용해 라우터의 타입을 등록해야 합니다. `@tanstack/react-router`의 `Register` 인터페이스를 확장하고, `router` 인스턴스의 타입을 가진 `router` 속성을 추가하면 됩니다.

```tsx
declare module '@tanstack/react-router' {
  interface Register {
    // 이렇게 하면 라우터의 타입을 추론하고 프로젝트 전체에 등록합니다
    router: typeof router
  }
}
```

라우터를 등록하면, 프로젝트 전체에서 라우팅과 관련된 모든 부분에 대해 타입 안전성을 얻을 수 있습니다.


## Not-Found 라우트

이전 가이드에서 약속한 대로, 이제 `notFoundRoute` 옵션에 대해 알아보겠습니다. 이 옵션은 다른 적절한 매치가 없을 때 렌더링할 라우트를 설정하는 데 사용됩니다. 404 페이지를 렌더링하거나 기본 라우트로 리다이렉트할 때 유용합니다.


### 파일 기반 라우팅

파일 기반 라우팅을 사용한다면, 루트 라우트를 해당 위치에서 불러온 후 `notFoundRoute` 메서드를 사용해 `NotFoundRoute` 인스턴스를 생성해야 합니다:

```tsx
import { NotFoundRoute } from '@tanstack/react-router'
import { Route as rootRoute } from './routes/__root.tsx'

const notFoundRoute = new NotFoundRoute({
  getParentRoute: () => rootRoute,
  component: () => '404 Not Found',
})

const router = createRouter({
  routeTree,
  notFoundRoute,
})
```


### 코드 기반 라우팅

코드 기반 라우팅을 사용한다면, 루트 라우트를 참조하는 `NotFoundRoute`를 생성한 후, 이를 라우터의 `notFoundRoute` 옵션에 전달해야 합니다.

```tsx
import { NotFoundRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

// ...

const notFoundRoute = new NotFoundRoute({
  getParentRoute: () => rootRoute,
  component: () => '404 Not Found',
})

const router = createRouter({
  routeTree,
  notFoundRoute,
})
```


## 기타 옵션

`Router` 생성자에 전달할 수 있는 다양한 옵션이 있습니다. 전체 목록은 [API 레퍼런스](../api/router/RouterOptionsType.md)에서 확인할 수 있습니다.


