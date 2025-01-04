# createRootRouteWithContext 함수

`createRootRouteWithContext` 함수는 라우터가 생성될 때 컨텍스트 타입이 필요로 하는 루트 라우트 인스턴스를 생성하는 데 사용할 수 있는 헬퍼 함수입니다.


## createRootRouteWithContext 제네릭

`createRootRouteWithContext` 함수는 하나의 제네릭 인자를 받습니다:


### `TRouterContext` 제네릭

- 타입: `TRouterContext`
- 선택 사항이지만, **권장됨**
- 라우터가 생성될 때 충족되어야 하는 컨텍스트 타입을 정의


## createRootRouteWithContext 반환값

- 새로운 [`createRootRoute`](./createRootRouteFunction.md) 인스턴스를 생성하는 데 사용할 수 있는 팩토리 함수를 반환합니다.
- 이 함수는 [`createRootRoute`](./createRootRouteFunction.md) 함수와 동일한 단일 인자를 받습니다.


## 예제

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'
import { QueryClient } from '@tanstack/react-query'

// 라우터 컨텍스트 인터페이스 정의
interface MyRouterContext {
  queryClient: QueryClient
}

// 루트 라우트 생성
const rootRoute = createRootRouteWithContext()({
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


