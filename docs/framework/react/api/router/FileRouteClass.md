# FileRoute 클래스

> [!경고]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 주요 버전에서 제거될 예정입니다.
> 대신 [`createFileRoute`](./createFileRouteFunction.md) 함수를 사용해 주세요.

`FileRoute` 클래스는 파일 기반 라우트 인스턴스를 생성하는 팩토리입니다. 이 라우트 인스턴스는 `tsr generate` 및 `tsr watch` 커맨드를 사용하여 자동으로 라우트 트리를 생성하는 데 활용할 수 있습니다.


## `FileRoute` 생성자

`FileRoute` 생성자는 하나의 인자를 받습니다. 이 인자는 라우트가 생성될 파일의 `path`입니다.


### 생성자 옵션

- 타입: `string` 리터럴
- 필수 항목이지만, **`tsr generate`와 `tsr watch` 커맨드에 의해 자동으로 삽입되고 업데이트됩니다**.
- 라우트가 생성될 파일의 전체 경로를 나타냅니다.


### 생성자 반환값

- `FileRoute` 클래스의 인스턴스를 반환합니다. 이 인스턴스는 라우트를 생성하는 데 사용할 수 있습니다.


## `FileRoute` 메서드

`FileRoute` 클래스는 다음과 같은 메서드를 구현합니다:


### `.createRoute` 메서드

`createRoute` 메서드는 파일 라우트 인스턴스를 설정하는 데 사용할 수 있는 메서드입니다. 이 메서드는 파일 라우트 인스턴스를 설정하는 데 사용할 `options`라는 단일 인자를 받습니다.


#### .createRoute 옵션

- 타입: `Omit<RouteOptions, 'getParentRoute' | 'path' | 'id'>`
- [`RouteOptions`](./RouteOptionsType.md) 참조
- 선택 사항
- `Route` 클래스에서 사용할 수 있는 옵션과 동일하지만, 파일 기반 라우팅에서는 필요하지 않은 `getParentRoute`, `path`, `id` 옵션은 제외됩니다.


#### .createRoute 반환값

`.createRoute`는 라우트 트리에 삽입할 수 있는 [`Route`](./RouteType.md) 인스턴스를 반환합니다. 이 인스턴스를 사용해 라우트를 구성할 수 있습니다.

> ⚠️ 주의: `tsr generate`와 `tsr watch`가 정상적으로 동작하려면, 파일에서 `Route` 식별자를 사용해 라우트 인스턴스를 반드시 내보내야 합니다.


### 예제

```tsx
import { FileRoute } from '@tanstack/react-router'

export const Route = new FileRoute('/').createRoute({
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return {data}
}
```


