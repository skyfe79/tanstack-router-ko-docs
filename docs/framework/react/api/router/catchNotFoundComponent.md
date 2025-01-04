# Table of Contents

- [CatchNotFound 컴포넌트](#catchnotfound-컴포넌트)
  - [CatchNotFound 컴포넌트의 props](#catchnotfound-컴포넌트의-props)
    - [`props.children` prop](#propschildren-prop)
    - [`props.fallback` 속성](#propsfallback-속성)
    - [`props.onCatch` 프로퍼티](#propsoncatch-프로퍼티)
  - [CatchNotFound 반환값](#catchnotfound-반환값)
  - [예제](#예제)

# CatchNotFound 컴포넌트

`CatchNotFound` 컴포넌트는 자식 컴포넌트에서 발생한 **not-found 오류**를 잡아내는 역할을 합니다. 이 컴포넌트는 오류가 발생했을 때 **대체 컴포넌트(fallback component)**를 렌더링하고, 선택적으로 `onCatch` 콜백을 호출합니다. 또한, **pathname**이 변경되면 상태를 초기화합니다.


## CatchNotFound 컴포넌트의 props

`CatchNotFound` 컴포넌트는 다음과 같은 props를 받습니다:


### `props.children` prop

- 타입: `React.ReactNode`
- 필수 여부: 필수
- 설명: 에러가 없을 때 렌더링할 컴포넌트의 자식 엘리먼트

이 `props.children`은 컴포넌트가 정상적으로 동작할 때 보여줄 내용을 정의하는 데 사용됩니다. 에러가 발생하지 않으면 이 자식 엘리먼트들이 화면에 렌더링됩니다.


### `props.fallback` 속성

- 타입: `(error: NotFoundError) => React.ReactElement`
- 선택 사항
- 오류가 발생했을 때 렌더링할 컴포넌트를 지정합니다.


### `props.onCatch` 프로퍼티

- 타입: `(error: any) => void`
- 선택 사항
- 컴포넌트의 자식에서 발생한 에러를 인자로 받아 실행되는 콜백 함수


## CatchNotFound 반환값

- 오류가 없을 경우 컴포넌트의 자식 요소를 반환합니다.
- 오류가 발생할 경우 `fallback`을 반환합니다.


## 예제

```tsx
import { CatchNotFound } from '@tanstack/react-router'

function Component() {
  return (
    <CatchNotFound
      fallback={(error) => `Not found error! ${JSON.stringify(error)}`}
    >
      {/* 컴포넌트 내용 */}
    </CatchNotFound>
  )
}
```

이 예제에서는 `CatchNotFound` 컴포넌트를 사용하여 라우트에서 발생하는 404 에러를 처리합니다. `fallback` 속성에 에러 메시지를 표시하는 함수를 전달하여, 해당 에러가 발생했을 때 사용자에게 적절한 피드백을 제공합니다.


