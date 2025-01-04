# useRouterState 훅

`useRouterState` 메서드는 라우터의 현재 내부 상태를 반환하는 훅입니다. 이 훅은 컴포넌트에서 라우터의 현재 상태에 접근할 때 유용합니다.

> [!TIP]
> 현재 위치나 현재 매치에 접근하려면, 먼저 [`useLocation`](./useLocationHook.md)과 [`useMatches`](./useMatchesHook.md) 훅을 사용해 보세요. 이 훅들은 라우터 상태에 직접 접근하는 것보다 더 편리하고 사용하기 쉽게 설계되었습니다.


## useRouterState 옵션

`useRouterState` 훅은 선택적으로 `options` 객체를 인자로 받습니다.


### `opts.select` 옵션

- 타입: `(state: RouterState) => TSelected`
- 선택 사항
- 이 함수가 제공되면, [`RouterState`](./RouterStateType.md) 객체와 함께 호출되며, 반환된 값은 `useRouterState`에서 반환됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useRouterState 반환값

- 현재 [`RouterState`](./RouterStateType.md) 객체를 반환합니다. 만약 `select` 함수가 제공된 경우, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useRouterState } from '@tanstack/react-router'

function Component() {
  const state = useRouterState()
  //    ^ RouterState 타입

  // 또는

  const selected = useRouterState({
    select: (state) => state.location,
  })
  //    ^ ParsedLocation 타입

  // ...
}
```

- `useRouterState` 훅을 사용하여 라우터 상태를 가져올 수 있습니다.
- 첫 번째 예제에서는 전체 라우터 상태를 가져옵니다. 이때 `state` 변수는 `RouterState` 타입을 가집니다.
- 두 번째 예제에서는 `select` 함수를 사용해 특정 상태 값만 선택적으로 가져옵니다. 여기서는 `location` 정보만 추출하며, `selected` 변수는 `ParsedLocation` 타입을 가집니다.


