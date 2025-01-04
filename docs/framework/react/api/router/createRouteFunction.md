# createRoute 함수

`createRoute` 함수는 [`Route`](./RouteType.md) 인스턴스를 반환합니다. 이 라우트 인스턴스는 루트 라우트의 자식으로 전달되어 라우트 트리를 생성할 수 있습니다. 이후 이 라우트 트리는 라우터에 전달됩니다.


## createRoute 옵션

- 타입: [`RouteOptions`](./RouteOptionsType.md)
- 필수 여부: 필수
- 설명: 라우트 인스턴스를 구성하는 데 사용되는 옵션들


## createRoute 반환값

새로운 [`Route`](./RouteType.md) 인스턴스를 반환합니다.


## 예제

```tsx
import { createRoute } from '@tanstack/react-router'
import { rootRoute } from './__root'

const Route = createRoute({
  getParentRoute: () => rootRoute,  // 부모 라우트 설정
  path: '/',  // 경로 설정
  loader: () => {
    return 'Hello World'  // 데이터 로딩
  },
  component: IndexComponent,  // 컴포넌트 연결
})

function IndexComponent() {
  const data = Route.useLoaderData()  // 로더 데이터 사용
  return {data}  // 데이터 반환
}
```


