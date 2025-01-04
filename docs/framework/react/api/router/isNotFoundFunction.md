# Table of Contents

- [isNotFound 함수](#isnotfound-함수)
  - [isNotFound 옵션](#isnotfound-옵션)
    - [`input` 옵션](#input-옵션)
  - [isNotFound 반환값](#isnotfound-반환값)
  - [예제](#예제)

# isNotFound 함수

`isNotFound` 함수는 특정 객체가 [`NotFoundError`](./NotFoundErrorType.md) 객체인지 확인하는 데 사용됩니다.


## isNotFound 옵션

`isNotFound` 함수는 하나의 인자를 받습니다. 이 인자는 `input`입니다.


### `input` 옵션

- 타입: `unknown`
- 필수 여부: 필수
- [`NotFoundError`](./NotFoundErrorType.md)인지 확인할 객체입니다.


## isNotFound 반환값

- 타입: `boolean`
- 객체가 [`NotFoundError`](./NotFoundErrorType.md)인 경우 `true`를 반환합니다.
- 객체가 [`NotFoundError`](./NotFoundErrorType.md)가 아닌 경우 `false`를 반환합니다.


## 예제

```tsx
import { isNotFound } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isNotFound(obj)) {
    // ...
  }
}
```


