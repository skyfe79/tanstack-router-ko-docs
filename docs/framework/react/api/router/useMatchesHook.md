# useMatches 훅

`useMatches` 훅은 **React 컴포넌트 트리에서 호출 위치와 상관없이** 라우터의 모든 [`RouteMatch`](./RouteMatchType.md) 객체를 반환합니다.

> [!TIP]
> 부모 또는 자식 매치만 필요한 경우, [`useParentMatches`](./useParentMatchesHook.md) 또는 [`useChildMatches`](./useChildMatchesHook.md)를 사용할 수 있습니다. 필요에 따라 적절한 훅을 선택하세요.


## useMatches 옵션

`useMatches` 훅은 하나의 `선택적(optional)` 인자를 받습니다. 이 인자는 `options` 객체입니다.


### `opts.select` 옵션

- 선택 사항
- `(matches: RouteMatch[]) => TSelected`
- 이 함수를 제공하면, 라우트 매칭 결과(`matches`)를 인자로 받아 호출됩니다. 반환값은 `useMatches` 훅의 결과로 반환됩니다. 이 값은 또한 **얕은 비교(shallow equality)**를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useMatches 반환값

- `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
- `select` 함수가 제공되지 않은 경우, [`RouteMatch`](./RouteMatchType.md) 객체의 배열을 반환합니다.


## 예제

```tsx
import { useMatches } from '@tanstack/react-router'

function Component() {
  const matches = useMatches()
  //     ^? [RouteMatch, RouteMatch, ...]
  // ...
}
```


