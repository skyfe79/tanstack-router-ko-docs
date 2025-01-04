# isRedirect 함수

`isRedirect` 함수는 특정 객체가 리다이렉트 객체인지 확인하는 데 사용됩니다.


## isRedirect 옵션

`isRedirect` 함수는 하나의 인자 `input`을 받습니다.


#### `input`

- 타입: `unknown`
- 필수 여부: 필수
- 리다이렉트 객체인지 확인할 객체


## isRedirect 반환값

- 타입: `boolean`
- 객체가 리다이렉트 객체인 경우 `true` 반환
- 객체가 리다이렉트 객체가 아닌 경우 `false` 반환


## 예제

```tsx
import { isRedirect } from '@tanstack/react-router'

function somewhere(obj: unknown) {
  if (isRedirect(obj)) {
    // ...
  }
}
```


