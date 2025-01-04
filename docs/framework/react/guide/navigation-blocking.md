# Table of Contents

- [네비게이션 블로킹](#네비게이션-블로킹)
  - [네비게이션 블로킹은 어떻게 동작하나요?](#네비게이션-블로킹은-어떻게-동작하나요)
  - [네비게이션 블로킹 사용 방법](#네비게이션-블로킹-사용-방법)
  - [훅/논리 기반 블로킹](#훅논리-기반-블로킹)
  - [컴포넌트 기반 블로킹](#컴포넌트-기반-블로킹)
  - [커스텀 UI를 어떻게 표시할 수 있나요?](#커스텀-ui를-어떻게-표시할-수-있나요)
    - [리졸버를 활용한 훅/로직 기반 커스텀 UI](#리졸버를-활용한-훅로직-기반-커스텀-ui)
    - [리졸버 없이 Hook/논리 기반 커스텀 UI 구현](#리졸버-없이-hook논리-기반-커스텀-ui-구현)
    - [컴포넌트 기반 커스텀 UI](#컴포넌트-기반-커스텀-ui)

# 네비게이션 블로킹

네비게이션 블로킹은 사용자가 페이지를 이동하려는 것을 막는 방법입니다. 주로 다음과 같은 상황에서 사용됩니다:

- 저장되지 않은 변경 사항이 있을 때
- 폼 작성 중일 때
- 결제 진행 중일 때

이러한 경우, 사용자에게 페이지를 떠날지 확인하는 메시지나 커스텀 UI를 보여줘야 합니다.

- 사용자가 확인을 선택하면 네비게이션이 정상적으로 진행됩니다.
- 사용자가 취소를 선택하면 모든 대기 중인 네비게이션이 차단됩니다.


## 네비게이션 블로킹은 어떻게 동작하나요?

네비게이션 블로킹은 기본 히스토리 API에 하나 이상의 "블로커" 레이어를 추가합니다. 블로커가 존재하는 경우, 네비게이션은 다음 방식 중 하나로 일시 중지됩니다:

- **커스텀 UI**
  - 네비게이션이 라우터 레벨에서 제어할 수 있는 요소에 의해 트리거된 경우, 사용자에게 작업을 수행하거나 확인 UI를 표시할 수 있습니다. 각 블로커의 `blocker` 함수는 비동기적이고 순차적으로 실행됩니다. 블로커 함수가 `true`를 반환하거나 resolve되면, 네비게이션이 허용되고 나머지 블로커들도 동일하게 진행됩니다. 단 하나의 블로커라도 `false`를 반환하거나 resolve되면, 네비게이션이 취소되고 나머지 `blocker` 함수들은 무시됩니다.
  
- **`onbeforeunload` 이벤트**
  - 직접 제어할 수 없는 페이지 이벤트의 경우, 브라우저의 `onbeforeunload` 이벤트를 사용합니다. 사용자가 탭이나 윈도우를 닫거나, 페이지를 새로 고치거나, 페이지 리소스를 언로드하려고 시도하면, 브라우저의 기본 "정말로 나가시겠습니까?" 대화상자가 표시됩니다. 사용자가 확인을 선택하면 모든 블로커가 무시되고 페이지가 언로드됩니다. 사용자가 취소를 선택하면 언로드가 취소되고 페이지는 그대로 유지됩니다.


## 네비게이션 블로킹 사용 방법

네비게이션 블로킹을 사용하는 방법은 두 가지가 있습니다:

1. **훅/논리 기반 블로킹**
2. **컴포넌트 기반 블로킹**


## 훅/논리 기반 블로킹

폼이 수정된 상태에서 네비게이션을 막고 싶다면 `useBlocker` 훅을 사용할 수 있습니다.

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) return false

      const shouldLeave = confirm('정말로 나가시겠습니까?')
      return !shouldLeave
    },
  })

  // ...
}
```

`shouldBlockFn`은 `current`와 `next` 위치에 대한 타입 안전한 접근을 제공합니다.

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  // /foo에서 /bar/123?hello=world로 이동하는 것을 항상 막음
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      return (
        current.routeId === '/foo' &&
        next.fullPath === '/bar/$id' &&
        next.params.id === 123 &&
        next.search.hello === 'world'
      )
    },
    withResolver: true,
  })

  // ...
}
```

`useBlocker` 훅에 대한 더 자세한 정보는 [API 레퍼런스](../api/router/useBlockerHook.md)에서 확인할 수 있습니다.


## 컴포넌트 기반 블로킹

로직이나 훅 기반 블로킹 외에도, `Block` 컴포넌트를 사용해 비슷한 결과를 얻을 수 있습니다:

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block
      shouldBlockFn={() => {
        if (!formIsDirty) return false

        const shouldLeave = confirm('정말로 떠나시겠습니까?')
        return !shouldLeave
      }}
    />
  )

  // 또는

  return (
    <Block shouldBlock={!formIsDirty} withResolver>
      {({ status, proceed, reset }) => <>{/* ... */}</>}
    </Block>
  )
}
```

이 코드는 폼이 변경되었을 때 사용자가 페이지를 떠나려고 할 경우 확인 메시지를 표시합니다. `Block` 컴포넌트는 `shouldBlockFn` 함수를 통해 블로킹 여부를 결정하거나, `shouldBlock` 프로퍼티와 `withResolver`를 함께 사용해 더 세밀한 제어가 가능합니다.


## 커스텀 UI를 어떻게 표시할 수 있나요?

대부분의 경우, `shouldBlockFn` 함수에서 `withResolver: false`를 설정한 상태로 `window.confirm`을 사용하는 것으로 충분합니다. 이렇게 하면 사용자에게 네비게이션이 차단되었음을 명확히 알리고, 사용자의 응답에 따라 차단을 해제할 수 있습니다.

하지만 경우에 따라, 앱의 디자인과 더 잘 어울리고 덜 방해가 되는 커스텀 UI를 표시하고 싶을 수도 있습니다.

**참고:** `withResolver`가 `true`인 경우, `shouldBlockFn`의 반환 값은 차단을 해결하지 않습니다.


### 리졸버를 활용한 훅/로직 기반 커스텀 UI

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: () => formIsDirty,
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>정말로 페이지를 떠나시겠습니까?</p>
          <button onClick={proceed}>예</button>
          <button onClick={reset}>아니오</button>
        </div>
      )}
    </>
  )
}
```

이 코드는 `useBlocker` 훅을 사용하여 폼이 변경되었을 때 사용자가 페이지를 떠나려고 할 경우 확인 메시지를 보여주는 예제입니다. `formIsDirty` 상태가 `true`일 때, `useBlocker`는 페이지 이동을 차단하고 사용자에게 확인 메시지를 표시합니다. 사용자가 "예"를 선택하면 페이지 이동이 진행되고, "아니오"를 선택하면 이동이 취소됩니다.


### 리졸버 없이 Hook/논리 기반 커스텀 UI 구현

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) {
        return false
      }

      const shouldBlock = new Promise((resolve) => {
        // 여러분이 선택한 모달 관리자를 사용
        modals.open({
          title: '정말로 나가시겠습니까?',
          children: (
            <SaveBlocker
              confirm={() => {
                modals.closeAll()
                resolve(false)
              }}
              reject={() => {
                modals.closeAll()
                resolve(true)
              }}
            />
          ),
          onClose: () => resolve(true),
        })
      })
      return shouldBlock
    },
  })

  // ...
}
```

이 코드는 `useBlocker` 훅을 사용하여 폼이 변경되었을 때 사용자가 페이지를 떠나려고 할 경우 경고 모달을 표시하는 예제입니다. `formIsDirty` 상태가 `true`일 때만 모달이 나타나며, 사용자가 확인 또는 취소를 선택하면 그에 따라 페이지 이동을 허용하거나 차단합니다.


### 컴포넌트 기반 커스텀 UI

훅과 마찬가지로, `Block` 컴포넌트도 렌더 프롭스와 동일한 상태와 함수를 반환합니다:

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block when={formIsDirty} withResolver>
      {({ status, proceed, reset }) => (
        <>
          {/* ... */}
          {status === 'blocked' && (
            <div>
              <p>정말로 나가시겠습니까?</p>
              <button onClick={proceed}>예</button>
              <button onClick={reset}>아니오</button>
            </div>
          )}
        </>
      )}
    </Block>
  )
}
```

이 예제에서 `Block` 컴포넌트는 `formIsDirty` 상태에 따라 사용자에게 확인 메시지를 표시합니다. `status`가 'blocked'일 경우, 사용자는 예 또는 아니오 버튼을 통해 진행 여부를 결정할 수 있습니다.


