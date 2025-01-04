# Table of Contents

- [useMatchRoute 훅](#usematchroute-훅)
  - [useMatchRoute 반환값](#usematchroute-반환값)
  - [matchRoute 함수](#matchroute-함수)
    - [matchRoute 함수 옵션](#matchroute-함수-옵션)
    - [matchRoute 함수 반환값](#matchroute-함수-반환값)
  - [예제](#예제)

# useMatchRoute 훅

`useMatchRoute` 훅은 현재 위치나 보류 중인 위치에 대해 라우트를 매칭할 수 있는 `matchRoute` 함수를 반환하는 훅입니다.


## useMatchRoute 반환값

- `matchRoute` 함수를 반환합니다. 이 함수는 현재 위치나 보류 중인 위치에 대해 라우트를 매칭하는 데 사용할 수 있습니다.


## matchRoute 함수

`matchRoute` 함수는 현재 위치나 보류 중인 위치에 대해 라우트를 매칭하는 데 사용할 수 있는 함수입니다.


### matchRoute 함수 옵션

`matchRoute` 함수는 하나의 인자를 받습니다. 이 인자는 `options` 객체입니다.

- 타입: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)


### matchRoute 함수 반환값

- 매칭된 라우트의 파라미터를 반환합니다. 매칭된 라우트가 없을 경우 `false`를 반환합니다.


## 예제

```tsx
import { useMatchRoute } from '@tanstack/react-router'

// 현재 위치: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId' })
  //    ^ { postId: '123' }
}

// 현재 위치: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts' })
  //    ^ false
}

// 현재 위치: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts', fuzzy: true })
  //    ^ {}
}

// 현재 위치: /posts
// 대기 중인 위치: /posts/123
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId', pending: true })
  //    ^ { postId: '123' }
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({ to: '/posts/$postId/foo/$fooId' })
  //    ^ { postId: '123', fooId: '456' }
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789' },
  })
  //    ^ false
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '123', fooId: '456' },
  })
  //    ^ { postId: '123', fooId: '456' }
}

// 현재 위치: /posts/123/foo/456
function Component() {
  const matchRoute = useMatchRoute()
  const params = matchRoute({
    to: '/posts/$postId/foo/$fooId',
    params: { postId: '789', fooId: '456' },
  })
  //    ^ false
}
```


