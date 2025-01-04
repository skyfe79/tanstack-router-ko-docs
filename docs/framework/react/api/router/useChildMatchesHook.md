# Table of Contents

- [useChildMatches 훅](#usechildmatches-훅)
  - [useChildMatches 옵션](#usechildmatches-옵션)
    - [`opts.select` 옵션](#optsselect-옵션)
    - [`opts.structuralSharing` 옵션](#optsstructuralsharing-옵션)
  - [useChildMatches 반환값](#usechildmatches-반환값)
  - [예제](#예제)

# useChildMatches 훅

`useChildMatches` 훅은 가장 가까운 매치부터 리프(leaf) 매치까지의 모든 자식 [`RouteMatch`](./RouteMatchType.md) 객체를 반환합니다. **현재 매치는 포함되지 않으며, 현재 매치는 `useMatch` 훅을 사용해 얻을 수 있습니다.**

> [!IMPORTANT]
> 라우터에 대기 중인 매치가 있고, 해당 매치가 보류 중인 컴포넌트 폴백을 보여주고 있다면, `router.state.matches` 대신 `router.state.pendingMatches`가 사용됩니다.


## useChildMatches 옵션

`useChildMatches` 훅은 하나의 `선택적` 인자를 받습니다. 이 인자는 `options` 객체입니다.


### `opts.select` 옵션

- 선택 사항
- `(matches: RouteMatch[]) => TSelected`
- 이 함수를 제공하면, 라우트 매칭 결과를 인자로 받아 호출됩니다. 반환된 값은 `useChildMatches` 훅의 결과로 반환됩니다. 또한 이 값은 **얕은 비교(shallow equality)**를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useChildMatches 반환값

- `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
- `select` 함수가 제공되지 않은 경우, [`RouteMatch`](./RouteMatchType.md) 객체의 배열을 반환합니다.


## 예제

```tsx
import { useChildMatches } from '@tanstack/react-router'

function Component() {
  const childMatches = useChildMatches()
  // ...
}
```


