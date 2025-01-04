# getRouteApi 함수

`getRouteApi` 함수는 특정 라우트 ID와 등록된 라우트 타입에 미리 바인딩된 `useParams`, `useSearch`, `useRouteContext`, `useNavigate`, `useLoaderData`, `useLoaderDeps`와 같은 일반적인 훅의 타입 안전 버전을 제공합니다.


## getRouteApi 옵션

`getRouteApi` 함수는 하나의 인자를 받습니다. 이 인자는 `routeId`라는 문자열 리터럴입니다.


### `routeId` 옵션

- 타입: `string`
- 필수 여부: 필수
- 설명: [`RouteApi`](./RouteApiClass.md) 인스턴스가 바인딩될 라우트 ID

```typescript
// 예제 코드
const routeApi = new RouteApi({
  routeId: 'exampleRouteId'
});
```

이 옵션은 `RouteApi` 인스턴스가 특정 라우트와 연결되도록 설정합니다. `routeId`는 문자열 타입으로, 반드시 제공해야 합니다.


## getRouteApi 반환값

- `getRouteApi` 함수가 호출된 라우트 ID에 미리 바인딩된 [`RouteApi`](./RouteApiType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { getRouteApi } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts')

export function PostsPage() {
  const posts = routeApi.useLoaderData()
  // ...
}
```


