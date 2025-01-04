# Table of Contents

- [createLazyFileRoute 함수](#createlazyfileroute-함수)
  - [createLazyFileRoute 옵션](#createlazyfileroute-옵션)
    - [`path`](#path)
    - [createLazyFileRoute 반환값](#createlazyfileroute-반환값)
    - [예제](#예제)

# createLazyFileRoute 함수

`createLazyFileRoute` 함수는 파일 기반의 라우트 인스턴스를 생성하는 데 사용됩니다. 이 라우트 인스턴스는 매칭될 때 지연 로딩(lazily loaded)되며, **비중요 속성**만 설정할 수 있습니다. 여기서 비중요 속성은 `component`, `pendingComponent`, `errorComponent`, `notFoundComponent`와 같은 속성을 의미합니다.

```typescript
const route = createLazyFileRoute('/example')({
  component: ExampleComponent,
  pendingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  notFoundComponent: NotFoundPage,
});
```

이 함수는 코드 분할(code splitting)을 통해 애플리케이션의 초기 로딩 속도를 최적화하는 데 유용합니다. 라우트와 관련된 컴포넌트들은 실제로 해당 라우트에 접근할 때만 로드됩니다.


## createLazyFileRoute 옵션

`createLazyFileRoute` 함수는 하나의 인자를 받습니다. 이 인자는 `string` 타입으로, 라우트가 생성될 파일의 `경로(path)`를 나타냅니다.


### `path`

- **타입**: `string`
- **필수 여부**: 필수이지만, `tsr generate`와 `tsr watch` 커맨드에 의해 자동으로 삽입되고 업데이트됨
- **설명**: 라우트가 생성될 파일의 전체 경로를 나타냅니다.


### createLazyFileRoute 반환값

`createLazyFileRoute`는 [`RouteOptions`](./RouteOptionsType.md) 타입의 부분 집합을 인자로 받는 새로운 함수를 반환합니다. 이 인자는 파일 기반 [`Route`](./RouteType.md) 인스턴스를 구성하는 데 사용됩니다.

- 타입:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 주의: `tsr generate`와 `tsr watch`가 정상적으로 동작하려면, 파일 내에서 `Route` 식별자를 사용해 라우트 인스턴스를 반드시 내보내야 합니다.


### 예제

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/')({
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return {data}
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리를 사용하여 라우트를 생성하고, 해당 라우트의 컴포넌트를 정의합니다. `createLazyFileRoute` 함수를 통해 루트 경로(`/`)에 대한 라우트를 생성하고, `IndexComponent`를 해당 라우트의 컴포넌트로 지정합니다. `IndexComponent` 내부에서는 `Route.useLoaderData()`를 사용하여 데이터를 가져오고, 이를 반환합니다.


