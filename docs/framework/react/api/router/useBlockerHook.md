# useBlocker 훅

`useBlocker` 메서드는 특정 조건이 충족될 때 [네비게이션을 차단](../../guide/navigation-blocking.md)하는 훅입니다.

> ⚠️ 아래의 새로운 `useBlocker` API는 현재 `실험적(experimental)` 기능입니다.


## useBlocker 옵션

`useBlocker` 훅은 하나의 필수 인자로 옵션 객체를 받습니다:

```javascript
useBlocker({
  // 옵션 객체
});
```


### `options.shouldBlockFn` 옵션

- **필수 여부**: 필수
- **타입**: `ShouldBlockFn`
- 이 함수는 `boolean` 또는 `Promise<boolean>`을 반환해야 하며, 이 값은 현재 네비게이션을 차단할지 여부를 결정합니다.
- 이 함수는 `ShouldBlockFnArgs` 타입의 인자를 받으며, 이 인자는 현재 라우트와 다음 라우트, 그리고 수행된 액션에 대한 정보를 제공합니다.
- 이 함수는 라우터에게 네비게이션을 차단할지 여부를 알려주는 역할을 합니다. `true`를 반환하면 네비게이션을 차단하고, `false`를 반환하면 네비게이션을 허용합니다.

```ts
interface ShouldBlockFnLocation {
  routeId: TRouteId
  fullPath: TFullPath
  pathname: string
  params: TAllParams
  search: TFullSearchSchema
}

type ShouldBlockFnArgs = {
  current: ShouldBlockFnLocation
  next: ShouldBlockFnLocation
  action: HistoryAction
}
```


### `options.disabled` 옵션

- 선택 사항 - 기본값: `false`
- 타입: `boolean`
- 블로커를 완전히 비활성화할지 여부를 지정합니다.


### `options.enableBeforeUnload` 옵션

- 선택 사항 - 기본값: `true`
- 타입: `boolean | (() => boolean)`
- 브라우저의 `beforeUnload` 이벤트를 항상 또는 특정 조건에서만 차단할지 여부를 설정합니다.


### `options.withResolver` 옵션

- 선택 사항 - 기본값: `false`
- 타입: `boolean`
- 훅이 반환한 리졸버(resolver)를 사용할지, 아니면 여러분의 `shouldBlockFn` 함수 자체가 블로킹을 해결할지 지정합니다.


### `options.blockerFn` 옵션 (⚠️ 사용 중단됨)

- 선택 사항
- 타입: `BlockerFn`
- 네비게이션을 허용할지 여부를 나타내는 `boolean` 또는 `Promise<boolean>`을 반환하는 함수입니다.


### `options.condition` 옵션 (⚠️ 사용 중단됨)

- 선택 사항 - 기본값: `true`
- 타입: `boolean`
- 이 조건이 `true`일 때 네비게이션 시도가 차단됩니다.


## useBlocker 반환값

네비게이션을 수동으로 차단하거나 해제할 수 있는 컨트롤 객체를 반환합니다.

- `status` - `'blocked'` 또는 `'idle'` 중 하나의 문자열 리터럴
- `next` - 상태가 `blocked`일 때, 다음 위치에 대한 정보를 담은 타입이 좁혀진 객체
- `current` - 상태가 `blocked`일 때, 현재 위치에 대한 정보를 담은 타입이 좁혀진 객체
- `action` - 상태가 `blocked`일 때, 네비게이션을 트리거한 액션을 나타내는 `HistoryAction` 문자열
- `proceed` - 상태가 `blocked`일 때, 네비게이션을 계속 진행할 수 있도록 하는 함수
- `reset` - 상태가 `blocked`일 때, 네비게이션을 취소하고 `status`를 `'idle'`로 재설정하는 함수

또는

`withResolver`가 `false`일 때는 `void`를 반환합니다.


## 예제

`useBlocker` 훅의 두 가지 일반적인 사용 사례는 다음과 같습니다:


### 기본 사용법

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => formIsDirty,
  })

  // ...
}
```

위 예제는 `useBlocker` 훅을 사용하여 폼이 더러워졌을 때(`formIsDirty`가 `true`일 때) 네비게이션을 차단하는 방법을 보여줍니다. `shouldBlockFn` 함수는 네비게이션을 차단할지 여부를 결정하는 조건을 반환합니다. 이 경우, 폼이 더러워졌다면 네비게이션을 차단하게 됩니다.


### 커스텀 UI

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status, next } = useBlocker({
    shouldBlockFn: () => formIsDirty,
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>다음 페이지로 이동하려고 합니다: {next.pathname}</p>
          <p>정말로 떠나시겠습니까?</p>
          <button onClick={proceed}>예</button>
          <button onClick={reset}>아니오</button>
        </div>
      )}
    </>
  )
}
```

이 코드는 폼이 수정된 상태에서 페이지를 떠나려고 할 때 사용자에게 확인 메시지를 보여주는 커스텀 UI를 구현한 예제입니다. `useBlocker` 훅을 사용하여 폼이 더러워진 상태(`formIsDirty`)를 감지하고, 페이지 이동을 차단합니다. 사용자가 이동을 원할 경우 `proceed`를, 취소할 경우 `reset`을 호출하여 상태를 초기화합니다.


### 조건부 차단

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ next }) => {
      return !next.pathname.includes('step/')
    },
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>정말로 떠나시겠습니까?</p>
          <button onClick={proceed}>예</button>
          <button onClick={reset}>아니오</button>
        </div>
      )}
    </>
  )
}
```

이 코드는 `useBlocker` 훅을 사용하여 특정 조건에서 네비게이션을 차단하는 예제입니다. `shouldBlockFn` 함수는 다음 경로(`next.pathname`)가 'step/'을 포함하지 않을 때 차단 여부를 결정합니다. `status`가 'blocked' 상태일 때, 사용자에게 확인 메시지를 표시하고, 사용자가 '예'를 선택하면 `proceed` 함수를 호출하여 차단을 해제합니다. '아니오'를 선택하면 `reset` 함수를 호출하여 차단 상태를 초기화합니다.


### 리졸버 없이 사용하기

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: ({ next }) => {
      // 다음 경로가 'step/'을 포함하는지 확인
      if (next.pathname.includes('step/')) {
        return false
      }

      // 사용자에게 확인 메시지를 표시
      const shouldLeave = confirm('정말로 떠나시겠습니까?')
      return !shouldLeave
    },
  })

  // ...
}
```

이 코드는 `useBlocker` 훅을 사용하여 사용자가 폼을 떠나려고 할 때 확인 메시지를 표시합니다. `shouldBlockFn` 함수는 다음 경로가 특정 조건을 만족하는지 확인하고, 조건에 따라 사용자에게 확인 메시지를 보여줍니다.


### 타입 좁히기 (Type Narrowing)

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  // editor-1에서 /foo/123?hello=world로 이동하는 것을 차단
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      if (
        current.routeId === '/editor-1' &&
        next.fullPath === '/foo/$id' &&
        next.params.id === '123' &&
        next.search.hello === 'world'
      ) {
        return true
      }
      return false
    },
    enableBeforeUnload: false,
    withResolver: true,
  })

  // ...
}
```

이 코드는 `useBlocker` 훅을 사용하여 특정 조건에서 네비게이션을 차단하는 예제입니다. `shouldBlockFn` 함수는 현재 라우트와 다음 라우트를 비교하여 조건에 맞는 경우 `true`를 반환합니다. 이를 통해 특정 경로로의 이동을 차단할 수 있습니다.


