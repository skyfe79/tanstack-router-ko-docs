# RouteApi 클래스

> [!주의]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 대신 [`getRouteApi`](./getRouteApiFunction.md) 함수를 사용해 주세요.

`RouteApi` 클래스는 특정 라우트 ID와 등록된 라우트 타입에 미리 바인딩된 `useParams`, `useSearch`, `useRouteContext`, `useNavigate`, `useLoaderData`, `useLoaderDeps`와 같은 일반적인 훅의 타입 안전 버전을 제공합니다.


## 생성자 옵션

`RouteApi` 생성자는 하나의 인자를 받습니다. 이 인자는 `RouteApi` 인스턴스를 구성하는 데 사용되는 `options`입니다.


### `opts.routeId` 옵션

- 타입: `string`
- 필수 여부: 필수
- 설명: `RouteApi` 인스턴스가 연결될 라우트 ID를 지정합니다.


## 생성자 반환값

- 생성자는 호출된 라우트 ID에 미리 바인딩된 [`RouteApi`](./RouteApiType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { RouteApi } from '@tanstack/react-router'

const routeApi = new RouteApi({ id: '/posts' })

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```

위 예제는 `@tanstack/react-router` 라이브러리를 사용하여 라우트 데이터를 가져오는 방법을 보여줍니다. `RouteApi` 클래스를 사용하여 `/posts` 경로에 대한 API를 생성하고, `useLoaderData` 훅을 통해 해당 라우트의 데이터를 가져옵니다. 이 데이터는 `posts` 변수에 저장되며, 컴포넌트 내에서 활용할 수 있습니다.


