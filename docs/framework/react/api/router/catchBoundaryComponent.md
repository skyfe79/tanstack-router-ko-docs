# Table of Contents

- [CatchBoundary 컴포넌트](#catchboundary-컴포넌트)
  - [CatchBoundary 컴포넌트의 props](#catchboundary-컴포넌트의-props)
    - [`props.getResetKey` 프로퍼티](#propsgetresetkey-프로퍼티)
    - [`props.children` prop](#propschildren-prop)
    - [`props.errorComponent` 속성](#propserrorcomponent-속성)
    - [`props.onCatch` 프로퍼티](#propsoncatch-프로퍼티)
  - [CatchBoundary 반환값](#catchboundary-반환값)
  - [예제](#예제)

# CatchBoundary 컴포넌트

`CatchBoundary` 컴포넌트는 자식 컴포넌트에서 발생한 에러를 잡아내고, 에러 컴포넌트를 렌더링하며, 선택적으로 `onCatch` 콜백을 호출하는 역할을 합니다. 또한 `getResetKey` 함수를 받아서 키가 변경될 때 컴포넌트의 상태를 선언적으로 초기화할 수 있습니다.


## CatchBoundary 컴포넌트의 props

`CatchBoundary` 컴포넌트는 다음과 같은 props를 받습니다:


### `props.getResetKey` 프로퍼티

- 타입: `() => string`
- 필수 여부: 필수
- 설명: 컴포넌트의 상태를 초기화할 때 사용할 문자열을 반환하는 함수입니다. 이 키가 변경되면 컴포넌트의 상태가 리셋됩니다.


### `props.children` prop

- 타입: `React.ReactNode`
- 필수 여부: 필수
- 설명: 에러가 없을 때 렌더링할 컴포넌트의 자식 엘리먼트

```jsx
function MyComponent({ children }) {
  return (
    <div>
      {children}
    </div>
  );
}
```

위 예제에서 `children`은 `MyComponent` 내부에 렌더링될 자식 엘리먼트를 의미합니다. 이 prop은 필수적으로 제공되어야 하며, `React.ReactNode` 타입을 가집니다.


### `props.errorComponent` 속성

- 타입: `React.ReactNode`
- 선택 사항 - [`기본값: ErrorComponent`](./errorComponentComponent.md)
- 에러가 발생했을 때 렌더링할 컴포넌트를 지정합니다.


### `props.onCatch` 프로퍼티

- 타입: `(error: any) => void`
- 선택 사항
- 컴포넌트의 자식에서 발생한 에러를 인자로 받아 호출되는 콜백 함수입니다.


## CatchBoundary 반환값

- 오류가 없을 경우 컴포넌트의 자식 요소를 반환합니다.
- 오류가 발생하면 `errorComponent`를 반환합니다.


## 예제

```tsx
import { CatchBoundary } from '@tanstack/react-router'

function Component() {
  return (
    <CatchBoundary
      getResetKey={() => 'reset'}
      onCatch={(error) => console.error(error)}
    >
      My Component
    </CatchBoundary>
  )
}
```

이 예제에서는 `CatchBoundary` 컴포넌트를 사용하여 에러를 처리하는 방법을 보여줍니다. `getResetKey` 함수는 컴포넌트를 리셋할 때 사용할 키를 반환하고, `onCatch` 함수는 에러가 발생했을 때 실행됩니다. 이렇게 하면 컴포넌트 내에서 발생한 에러를 효과적으로 처리할 수 있습니다.


