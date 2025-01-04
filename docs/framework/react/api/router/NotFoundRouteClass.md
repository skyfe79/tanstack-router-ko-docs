# Table of Contents

- [NotFoundRoute 클래스](#notfoundroute-클래스)
  - [생성자 옵션](#생성자-옵션)
  - [예제](#예제)

# NotFoundRoute 클래스

> [!주의]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 라우트 설정 시 사용할 수 있는 `notFoundComponent` 라우트 옵션을 대신 사용해 주세요.
> 자세한 내용은 [Not Found Errors 가이드](../../guide/not-found-errors.md)를 참고하세요.

`NotFoundRoute` 클래스는 `Route` 클래스를 상속받습니다. 이 클래스를 사용하면 404(찾을 수 없음) 라우트 인스턴스를 생성할 수 있습니다. 생성한 라우트 인스턴스는 `routerOptions.notFoundRoute` 옵션에 전달하여, 라우트 트리의 모든 브랜치에 대한 기본 404 라우트를 설정할 수 있습니다.


## 생성자 옵션

`NotFoundRoute` 생성자는 객체를 유일한 인자로 받습니다.

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

- [RouteOptions](./RouteOptionsType.md) 참조
- 필수 항목
- 이 옵션들은 찾을 수 없는 라우트 인스턴스를 구성하는 데 사용됩니다.


## 예제

```tsx
import { NotFoundRoute, createRouter } from '@tanstack/react-router'
import { Route as rootRoute } from './routes/__root'
import { routeTree } from './routeTree.gen'

// 404 라우트 정의
const notFoundRoute = new NotFoundRoute({
  getParentRoute: () => rootRoute, // 부모 라우트 설정
  component: () => 'Not found!!!', // 404 페이지 컴포넌트
})

// 라우터 생성
const router = createRouter({
  routeTree, // 라우트 트리
  notFoundRoute, // 404 라우트
})

// ... 기타 코드
```

이 예제에서는 `@tanstack/react-router`를 사용하여 404 라우트를 정의하고 라우터를 생성하는 방법을 보여줍니다. `NotFoundRoute`를 통해 404 페이지를 설정하고, `createRouter` 함수로 라우터를 초기화합니다.


