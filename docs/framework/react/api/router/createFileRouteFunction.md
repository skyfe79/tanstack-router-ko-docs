# Table of Contents

- [createFileRoute 함수](#createfileroute-함수)
  - [createFileRoute 옵션](#createfileroute-옵션)
    - [`path` 옵션](#path-옵션)
  - [createFileRoute 반환값](#createfileroute-반환값)
  - [예제](#예제)

# createFileRoute 함수

`createFileRoute` 함수는 파일 기반 라우트 인스턴스를 생성하는 팩토리 함수입니다. 이렇게 생성된 라우트 인스턴스는 `tsr generate`와 `tsr watch` 커맨드를 사용하여 자동으로 라우트 트리를 생성하는 데 활용할 수 있습니다.


## createFileRoute 옵션

`createFileRoute` 함수는 라우트를 생성할 파일의 경로를 나타내는 `string` 타입의 인자를 하나 받습니다.


### `path` 옵션

- 타입: `string` 리터럴
- 필수 항목이지만, **`tsr generate`와 `tsr watch` 커맨드에 의해 자동으로 삽입되고 업데이트됨**
- 라우트가 생성될 파일의 전체 경로를 나타냄


## createFileRoute 반환값

`createFileRoute`는 [`RouteOptions`](./RouteOptionsType.md) 타입의 단일 인자를 받는 새로운 함수를 반환합니다. 이 인자는 파일 기반 [`Route`](./RouteType.md) 인스턴스를 구성하는 데 사용됩니다.

> ⚠️ 주의: `tsr generate`와 `tsr watch`가 제대로 동작하려면, 파일에서 `Route` 식별자를 사용해 라우트 인스턴스를 반드시 내보내야 합니다.


## 예제

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
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

이 예제는 `@tanstack/react-router` 라이브러리를 사용하여 루트 경로(`/`)에 대한 라우트를 생성하고, 해당 라우트의 컴포넌트를 정의하는 방법을 보여줍니다. 

1. `createFileRoute` 함수를 사용하여 루트 경로(`/`)에 대한 라우트를 생성합니다.
2. `loader` 함수는 라우트가 로드될 때 실행되며, 여기서는 간단히 `'Hello World'` 문자열을 반환합니다.
3. `IndexComponent`는 라우트의 컴포넌트로, `Route.useLoaderData()`를 통해 `loader`에서 반환된 데이터를 가져와 화면에 표시합니다.

이 코드는 라우트 기반의 데이터 로딩과 컴포넌트 렌더링을 간단하게 구현한 예제입니다.


