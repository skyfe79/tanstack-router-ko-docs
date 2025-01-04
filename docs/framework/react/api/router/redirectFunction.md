# 리다이렉트 함수

`redirect` 함수는 새로운 `Redirect` 객체를 반환합니다. 이 객체는 라우트의 `beforeLoad`나 `loader` 콜백 같은 곳에서 반환하거나 던져져서 새로운 위치로 리다이렉트를 트리거할 수 있습니다.


## 리다이렉트 옵션

`redirect` 함수는 리다이렉트 동작을 결정하는 `options`라는 단일 인자를 받습니다.

- 타입: [`Redirect`](./RedirectType.md)
- 필수 여부: 필수


## 리다이렉트 반환

- `options` 객체에서 `throw` 속성이 `true`로 설정된 경우, `Redirect` 객체는 함수 호출 내부에서 예외로 던져진다.
- `options` 객체에서 `throw` 속성이 `false` 또는 `undefined`로 설정된 경우, `Redirect` 객체는 반환된다.


## 예제

```tsx
import { redirect } from '@tanstack/react-router'

const route = createRoute({
  // redirect 객체를 던지는 방식
  loader: () => {
    if (!user) {
      throw redirect({
        to: '/login',
      })
    }
  },
  // `redirect`가 스스로 던지도록 강제하는 방식
  loader: () => {
    if (!user) {
      redirect({
        to: '/login',
        throw: true,
      })
    }
  },
  // ... 기타 라우트 옵션
})
```


