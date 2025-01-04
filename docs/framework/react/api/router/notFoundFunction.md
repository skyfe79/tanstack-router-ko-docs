# Table of Contents

- [notFound 함수](#notfound-함수)
  - [notFound 옵션](#notfound-옵션)
  - [notFound 반환 동작](#notfound-반환-동작)
  - [예제](#예제)

# notFound 함수

`notFound` 함수는 새로운 `NotFoundError` 객체를 반환합니다. 이 객체는 라우트의 `beforeLoad`나 `loader` 콜백과 같은 곳에서 반환되거나 던져질 수 있으며, `notFoundComponent`를 트리거하는 데 사용됩니다.


## notFound 옵션

`notFound` 함수는 선택적으로 하나의 인자를 받습니다. 이 인자는 not-found 오류 객체를 생성하기 위한 `options`입니다.

- 타입: [`Partial<NotFoundError>`](./NotFoundErrorType.md)
- 선택 사항


## notFound 반환 동작

- `options` 객체의 `throw` 속성이 `true`인 경우, 함수 호출 내부에서 `NotFoundError` 객체가 발생합니다.
- `options` 객체의 `throw` 속성이 `false` 또는 `undefined`인 경우, `NotFoundError` 객체가 반환됩니다.


## 예제

```tsx
import { notFound, createFileRoute } from '@tanstack/react-router'

const Route = new createFileRoute('/posts/$postId')({
  // not-found 객체를 던지는 경우
  loader: ({ context: { post } }) => {
    if (!post) {
      throw notFound()
    }
  },
  // 전체 페이지에 not-found를 표시하고 싶은 경우
  loader: ({ context: { team } }) => {
    if (!team) {
      throw notFound({ global: true })
    }
  },
  // ... 기타 라우트 옵션
})
```


