# Table of Contents

- [Router 클래스](#router-클래스)
  - [`Router` 생성자](#router-생성자)
    - [생성자 옵션](#생성자-옵션)
    - [생성자 반환값](#생성자-반환값)
  - [예제](#예제)

# Router 클래스

> [!CAUTION]
> 이 클래스는 더 이상 사용되지 않으며, TanStack Router의 다음 메이저 버전에서 제거될 예정입니다.  
> 대신 [`createRouter`](./createRouterFunction.md) 함수를 사용해 주세요.

`Router` 클래스는 새로운 라우터 인스턴스를 생성하는 데 사용됩니다.


## `Router` 생성자

`Router` 생성자는 하나의 인자를 받습니다. 이 인자는 라우터 인스턴스를 구성하는 데 사용되는 `options`입니다.


### 생성자 옵션

- 타입: [`RouterOptions`](./RouterOptionsType.md)
- 필수 여부: 필수
- 설명: 라우터 인스턴스를 구성하는 데 사용되는 옵션입니다.


### 생성자 반환값

- [`Router`](./RouterType.md)의 인스턴스를 반환합니다.


## 예제

```tsx
import { Router, RouterProvider } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = new Router({
  routeTree,
  defaultPreload: 'intent',
})

export default function App() {
  return 
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리를 사용하여 라우터를 설정하는 방법을 보여줍니다. `Router`와 `RouterProvider`를 임포트하고, `routeTree`를 정의한 후 라우터를 생성합니다. `defaultPreload` 옵션을 'intent'로 설정하여 사용자가 링크를 클릭하기 전에 미리 데이터를 가져오도록 합니다. 마지막으로 `App` 컴포넌트에서 라우터를 사용합니다.


