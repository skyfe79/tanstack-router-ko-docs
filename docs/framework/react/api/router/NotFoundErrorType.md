# Table of Contents

- [NotFoundError](#notfounderror)
  - [NotFoundError 속성](#notfounderror-속성)
    - [`data` 속성](#data-속성)
    - [`global` 속성](#global-속성)
    - [`route` 속성](#route-속성)
    - [`throw` 속성](#throw-속성)

# NotFoundError

`NotFoundError` 타입은 TanStack Router에서 찾을 수 없는 오류를 나타내기 위해 사용됩니다.

```tsx
export type NotFoundError = {
  global?: boolean
  data?: any
  throw?: boolean
  routeId?: string
}
```

- `global`: 이 오류가 전역적으로 적용되는지 여부를 나타냅니다.
- `data`: 오류와 관련된 추가 데이터를 포함할 수 있습니다.
- `throw`: 이 오류를 던져야 하는지 여부를 결정합니다.
- `routeId`: 오류가 발생한 라우트의 ID를 지정합니다.


## NotFoundError 속성

`NotFoundError` 객체는 다음과 같은 속성을 포함합니다:


### `data` 속성

- 타입: `any`
- 선택 사항
- not-found 오류가 처리될 때 `notFoundComponent`에 전달되는 커스텀 데이터


### `global` 속성

- 타입: `boolean`
- 선택 사항 - `기본값: false`
- true로 설정하면, `not-found` 오류가 발생한 라우트에서 상위로 전파되지 않고, 루트 라우트의 `notFoundComponent`에서 처리됩니다. 이는 루트 라우트를 임포트하고 `RootRoute.notFound()`를 호출하는 것과 동일한 동작을 합니다.


### `route` 속성

- 타입: `string`
- 선택 사항
- 404 오류를 처리하려고 시도할 라우트의 ID입니다. 해당 라우트에 `notFoundComponent`가 없는 경우, 오류는 상위 라우트로 전파됩니다(필요한 경우 루트 라우트에서 처리됨). 기본적으로 TanStack Router는 오류를 발생시킨 라우트에서 404 오류를 처리하려고 시도합니다.


### `throw` 속성

- 타입: `boolean`
- 선택 사항 - `기본값: false`
- 이 속성을 제공하면, 찾을 수 없는 객체를 반환하는 대신 예외를 발생시킵니다. 함수 내에서 예외를 발생시키면 반환 타입이 `never`가 되는 경우에 유용합니다. 이 경우, `notFound({ throw: true })`를 사용하여 찾을 수 없는 객체를 반환하지 않고 예외를 발생시킬 수 있습니다.


