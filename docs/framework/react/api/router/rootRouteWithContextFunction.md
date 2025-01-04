# Table of Contents

- [rootRouteWithContext 함수](#rootroutewithcontext-함수)
  - [rootRouteWithContext 제네릭](#rootroutewithcontext-제네릭)
    - [`TRouterContext` 제네릭](#troutercontext-제네릭)
  - [rootRouteWithContext 반환값](#rootroutewithcontext-반환값)
  - [예제](#예제)

# rootRouteWithContext 함수

> [!주의]
> 이 함수는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 대신 [`createRootRouteWithContext`](./createRootRouteWithContextFunction.md) 함수를 사용해 주세요.

`rootRouteWithContext` 함수는 라우터가 생성될 때 컨텍스트 타입이 필요로 하는 루트 라우트 인스턴스를 생성하는 데 사용할 수 있는 헬퍼 함수입니다.


## rootRouteWithContext 제네릭

`rootRouteWithContext` 함수는 하나의 제네릭 인자를 받습니다:


### `TRouterContext` 제네릭

- 타입: `TRouterContext`
- 선택 사항이지만, **권장**됩니다.
- 라우터가 생성될 때 충족되어야 하는 컨텍스트 타입을 정의합니다.

```typescript
type TRouterContext = {
  // 컨텍스트에 필요한 속성들을 정의
  user: User;
  permissions: string[];
};
```

이 제네릭 타입은 라우터가 동작하는 데 필요한 컨텍스트를 명시적으로 정의할 수 있게 해줍니다. 이를 통해 라우터를 생성할 때 필요한 데이터를 보다 명확하게 관리할 수 있습니다.


## rootRouteWithContext 반환값

- **새로운 [`createRootRoute`](./createRootRouteFunction.md) 인스턴스를 생성할 수 있는 팩토리 함수**를 반환합니다.
- 이 함수는 [`createRootRoute`](./createRootRouteFunction.md) 함수와 동일한 **단일 인자**를 받습니다.


## 예제

```tsx
import { rootRouteWithContext, createRouter } from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'

// 라우터 컨텍스트 인터페이스 정의
interface MyRouterContext {
  queryClient: QueryClient
}

// 루트 라우트 생성
const rootRoute = rootRouteWithContext()({
  component: () => ,
  // 루트 라우트 옵션
})

// 라우트 트리 구성
const routeTree = rootRoute.addChildren([
  // 다른 라우트들
])

// QueryClient 인스턴스 생성
const queryClient = new QueryClient()

// 라우터 생성
const router = createRouter({
  routeTree,
  context: {
    queryClient,
  },
})
```


