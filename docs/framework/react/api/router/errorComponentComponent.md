# Table of Contents

- [ErrorComponent 컴포넌트](#errorcomponent-컴포넌트)
  - [ErrorComponent props](#errorcomponent-props)
    - [`props.error` 속성](#propserror-속성)
    - [`props.reset` 프로퍼티](#propsreset-프로퍼티)
  - [ErrorComponent 반환값](#errorcomponent-반환값)

# ErrorComponent 컴포넌트

`ErrorComponent`는 오류 메시지를 렌더링하고, 선택적으로 오류의 상세 메시지를 표시하는 컴포넌트입니다.


## ErrorComponent props

`ErrorComponent` 컴포넌트는 다음과 같은 props를 받습니다:


### `props.error` 속성

- 타입: `any`
- 컴포넌트의 자식 요소에서 발생한 오류를 나타냅니다.


### `props.reset` 프로퍼티

- 타입: `() => void`
- 에러 상태를 프로그래밍 방식으로 초기화하는 함수


## ErrorComponent 반환값

- 에러 메시지가 존재할 경우, 해당 메시지를 포맷팅하여 반환한다.
- "Show Error" 버튼을 클릭하면 에러 메시지를 토글할 수 있다.
- 개발 환경에서는 기본적으로 에러 메시지가 표시된다.


