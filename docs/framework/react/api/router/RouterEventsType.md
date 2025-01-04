# Table of Contents

- [RouterEvents 타입](#routerevents-타입)
  - [라우터 이벤트 속성](#라우터-이벤트-속성)
    - [`type` 속성](#type-속성)
    - [`fromLocation` 속성](#fromlocation-속성)
    - [`toLocation` 속성](#tolocation-속성)
    - [`pathChanged` 속성](#pathchanged-속성)
    - [`hrefChanged` 속성](#hrefchanged-속성)
  - [예제](#예제)

# RouterEvents 타입

`RouterEvents` 타입은 라우터가 발생시킬 수 있는 모든 이벤트를 포함합니다. 이 타입의 각 최상위 키는 라우터가 발생시킬 수 있는 이벤트의 이름을 나타냅니다. 키의 값은 해당 이벤트의 페이로드입니다.

```tsx
type RouterEvents = {
  onBeforeNavigate: {
    type: 'onBeforeNavigate'
    fromLocation: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onBeforeLoad: {
    type: 'onBeforeLoad'
    fromLocation: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onLoad: {
    type: 'onLoad'
    fromLocation: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onResolved: {
    type: 'onResolved'
    fromLocation: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onBeforeRouteMount: {
    type: 'onBeforeRouteMount'
    fromLocation: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
}
```


## 라우터 이벤트 속성

이벤트가 발생하면, 이벤트 페이로드에 다음과 같은 속성들이 포함됩니다.


### `type` 속성

- 타입: `onBeforeNavigate | onBeforeLoad | onLoad | onBeforeRouteMount | onResolved`
- 이벤트의 타입을 나타냅니다.
- 리스너 함수에서 이벤트를 구분할 때 유용합니다.


### `fromLocation` 속성

- 타입: [`ParsedLocation`](./ParsedLocationType.md)
- 라우터가 전환되기 전의 위치를 나타냅니다.


### `toLocation` 속성

- 타입: [`ParsedLocation`](./ParsedLocationType.md)
- 라우터가 전환하려는 대상 위치를 나타냅니다.


### `pathChanged` 속성

- 타입: `boolean`
- `fromLocation`과 `toLocation` 사이의 경로가 변경된 경우 `true`로 설정됩니다.


### `hrefChanged` 속성

- 타입: `boolean`
- `fromLocation`과 `toLocation` 사이에 `href`가 변경된 경우 `true`로 설정됩니다.


## 예제

```tsx
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = createRouter({ routeTree })

const unsub = router.subscribe('onResolved', (evt) => {
  // ...
})
```

이 예제에서는 `@tanstack/react-router` 라이브러리를 사용하여 라우터를 생성하고, `routeTree`를 기반으로 라우터를 초기화합니다. 그런 다음 `router.subscribe` 메서드를 사용하여 라우트가 해결될 때 실행될 콜백 함수를 등록합니다. 이 콜백 함수는 `onResolved` 이벤트가 발생할 때마다 실행됩니다.


