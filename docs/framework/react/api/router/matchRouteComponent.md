# MatchRoute 컴포넌트

`useMatchRoute` 훅의 컴포넌트 버전입니다. `useMatchRoute`와 동일한 옵션을 받으며, 조건부 렌더링을 돕기 위한 추가적인 props도 지원합니다.


## MatchRoute 컴포넌트의 props

`MatchRoute` 컴포넌트는 조건부 렌더링을 돕기 위한 추가적인 props와 함께 `useMatchRoute` 훅과 동일한 옵션을 받습니다.


### `...props` 속성

- 타입: [`UseMatchRouteOptions`](./UseMatchRouteOptionsType.md)


### `children` prop

- 선택 사항
- `React.ReactNode`
  - 라우트가 매치될 경우 렌더링될 컴포넌트
- `((params: TParams | false) => React.ReactNode)`
  - 매치된 라우트의 파라미터를 받거나, 매치되지 않았을 경우 `false`를 인자로 받는 함수. 이 기능은 항상 렌더링해야 하지만, 라우트 매치 여부에 따라 다른 props를 렌더링해야 하는 컴포넌트에 유용하다.


## MatchRoute 반환값

`children` prop 또는 `children` 함수의 반환값 중 하나를 반환합니다.


## 예제

```tsx
import { MatchRoute } from '@tanstack/react-router'

function Component() {
  return (
    <MatchRoute>
      {(match) => (
        // 매치된 라우트에 대한 로직을 여기에 작성
      )}
    </MatchRoute>
  )
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리에서 `MatchRoute` 컴포넌트를 가져와 사용합니다. `MatchRoute` 컴포넌트는 현재 라우트가 매치되었는지 여부를 확인하고, 매치된 경우에만 내부의 콜백 함수를 실행합니다. 콜백 함수 내부에는 매치된 라우트에 대한 로직을 작성할 수 있습니다.


