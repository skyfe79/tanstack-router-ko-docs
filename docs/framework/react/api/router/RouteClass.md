# Table of Contents

- [Route 클래스](#route-클래스)
  - [`Route` 생성자](#route-생성자)
    - [생성자 옵션](#생성자-옵션)
    - [생성자 반환값](#생성자-반환값)
  - [예제](#예제)

# Route 클래스

> [!CAUTION]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 대신 [`createRoute`](./createRouteFunction.md) 함수를 사용해 주세요.

`Route` 클래스는 `RouteApi` 클래스를 구현하며, 라우트 인스턴스를 생성하는 데 사용됩니다. 라우트 인스턴스는 라우트 트리를 만드는 데 활용할 수 있습니다.


## `Route` 생성자

`Route` 생성자는 객체 하나를 인자로 받습니다.


### 생성자 옵션

- 타입: [`RouteOptions`](./RouteOptionsType.md)
- 필수 여부: 필수
- 설명: 라우트 인스턴스를 구성하는 데 사용되는 옵션들


### 생성자 반환값

새로운 [`Route`](./RouteType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { Route } from '@tanstack/react-router'
import { rootRoute } from './__root'

const indexRoute = new Route({
  getParentRoute: () => rootRoute,
  path: '/',
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = indexRoute.useLoaderData()
  return {data}
}
```

이 예제는 `@tanstack/react-router` 라이브러리를 사용하여 라우트를 정의하고 컴포넌트를 렌더링하는 방법을 보여줍니다. `indexRoute`는 루트 라우트(`rootRoute`)를 부모로 가지며, 경로는 `'/'`입니다. `loader` 함수는 `'Hello World'` 문자열을 반환하고, `IndexComponent`는 이 데이터를 사용하여 렌더링합니다.


