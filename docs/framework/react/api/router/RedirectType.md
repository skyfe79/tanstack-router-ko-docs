# 리다이렉트 타입

`Redirect` 타입은 TanStack Router에서 리다이렉트 동작을 나타내는 데 사용됩니다.

```tsx
export type Redirect = {
  statusCode?: number
  throw?: any
  headers?: HeadersInit
} & NavigateOptions
```

- [`NavigateOptions`](./NavigateOptionsType.md)


## 리다이렉트 속성

`Redirect` 객체는 다음과 같은 속성을 포함합니다:


### `statusCode` 프로퍼티

- 타입: `number`
- 선택 사항
- 리다이렉트 시 사용할 HTTP 상태 코드


### `throw` 속성

- 타입: `any`
- 선택 사항
- 이 속성을 제공하면 리다이렉트 객체를 반환하는 대신 던지게 됩니다. 함수 내에서 `throw`를 사용하면 반환 타입이 `never`가 되는 경우가 있는데, 이때 `redirect({ throw: true })`를 사용하여 리다이렉트 객체를 반환하지 않고 던질 수 있습니다.


### `headers` 속성

- 타입: `HeadersInit`
- 선택 사항
- 리다이렉트 시 사용할 HTTP 헤더를 지정합니다.


