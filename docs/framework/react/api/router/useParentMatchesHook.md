# Table of Contents

- [useParentMatches 훅](#useparentmatches-훅)
  - [useParentMatches 옵션](#useparentmatches-옵션)
    - [`opts.select` 옵션](#optsselect-옵션)
    - [`opts.structuralSharing` 옵션](#optsstructuralsharing-옵션)
  - [useParentMatches 반환값](#useparentmatches-반환값)
  - [예제](#예제)

# useParentMatches 훅

`useParentMatches` 훅은 현재 컨텍스트에서 매치된 라우트의 바로 위 부모부터 루트까지의 모든 부모 [`RouteMatch`](./RouteMatchType.md) 객체를 반환합니다. **이 훅은 현재 매치를 포함하지 않으며, 현재 매치는 `useMatch` 훅을 사용해 얻을 수 있습니다.**

> [!IMPORTANT]
> 라우터에 보류 중인 매치가 있고, 해당 매치가 보류 상태의 컴포넌트 폴백을 보여주고 있다면, `router.state.matches` 대신 `router.state.pendingMatches`가 사용됩니다.


## useParentMatches 옵션

`useParentMatches` 훅은 선택적으로 `options` 객체를 인자로 받습니다.


### `opts.select` 옵션

- 선택 사항
- `(matches: RouteMatch[]) => TSelected`
- 이 함수가 제공되면, 라우트 매칭 결과를 인자로 받아 호출됩니다. 반환된 값은 `useParentMatches`에서 반환되며, 이 값은 부모 컴포넌트를 리렌더링할지 여부를 결정하기 위해 얕은 비교(shallow equality)를 통해 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useParentMatches 반환값

- `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
- `select` 함수가 제공되지 않은 경우, [`RouteMatch`](./RouteMatchType.md) 객체의 배열을 반환합니다.


## 예제

```tsx
import { useParentMatches } from '@tanstack/react-router'

function Component() {
  const parentMatches = useParentMatches()
  //    ^ [RouteMatch, RouteMatch, ...]
}
```

위 예제는 `@tanstack/react-router` 라이브러리에서 제공하는 `useParentMatches` 훅을 사용하는 방법을 보여줍니다. 이 훅은 현재 컴포넌트의 상위 라우트 매치 정보를 배열 형태로 반환합니다. 반환된 `parentMatches` 배열에는 각 상위 라우트의 매치 정보가 순서대로 포함됩니다. 이를 통해 여러분은 현재 위치한 라우트의 상위 계층 정보를 쉽게 확인할 수 있습니다.


