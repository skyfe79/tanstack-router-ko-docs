# createRouter 함수

`createRouter` 함수는 [`RouterOptions`](./RouterOptionsType.md) 객체를 인자로 받아 새로운 [`Router`](./RouterClass.md) 인스턴스를 생성합니다.


## createRouter 옵션

- 타입: [`RouterOptions`](./RouterOptionsType.md)
- 필수 여부: 필수
- 라우터 인스턴스를 구성하는 데 사용되는 옵션입니다.


## createRouter 반환값

- [`Router`](./RouterType.md)의 인스턴스를 반환합니다.


## 예제

```tsx
import { createRouter, RouterProvider } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = createRouter({
  routeTree,
  defaultPreload: 'intent',
})

export default function App() {
  return 
}
```


