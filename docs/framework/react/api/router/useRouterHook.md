# Table of Contents

- [useRouter 훅](#userouter-훅)
  - [useRouter 반환값](#userouter-반환값)
  - [예제](#예제)

# useRouter 훅

`useRouter` 메서드는 컨텍스트에서 현재 [`Router`](./RouterType.md) 인스턴스를 반환하는 훅입니다. 이 훅은 컴포넌트 내에서 라우터 인스턴스에 접근할 때 유용합니다.


## useRouter 반환값

- 현재 [`Router`](./RouterType.md) 인스턴스를 반환합니다.

> ⚠️⚠️⚠️ **`router.state`는 항상 최신 상태를 유지하지만, 반응형(REACTIVE)이 아닙니다. 컴포넌트에서 `router.state`를 사용하면, 라우터 상태가 변경되어도 컴포넌트가 리렌더링되지 않습니다. 반응형 라우터 상태를 얻으려면 [`useRouterState`](./useRouterStateHook.md) 훅을 사용하세요.**


## 예제

```tsx
import { useRouter } from '@tanstack/react-router'

function Component() {
  const router = useRouter()
  //    ^ Router

  // ...
}
```


