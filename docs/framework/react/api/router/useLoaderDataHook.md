# useLoaderData 훅

`useLoaderData` 훅은 컴포넌트 트리에서 가장 가까운 [`RouteMatch`](./RouteMatchType.md)의 로더 데이터를 반환합니다.


## useLoaderData 옵션

`useLoaderData` 훅은 `options` 객체를 인자로 받습니다.


### `opts.from` 옵션

- 타입: `string`
- 가장 가까운 상위 매치의 라우트 ID
- 선택 사항이지만, 완전한 타입 안전성을 위해 권장됨
- `opts.strict`가 `true`인 경우, 이 옵션이 제공되지 않으면 TypeScript가 경고를 표시함
- `opts.strict`가 `false`인 경우, TypeScript는 반환된 로더 데이터에 대해 느슨한 타입을 제공함


### `opts.strict` 옵션

- 타입: `boolean`
- 선택 사항 - `기본값: true`
- `false`로 설정하면 `opts.from` 옵션이 무시되고, 모든 가능한 로더 데이터의 공통 타입을 반영하도록 타입이 느슨해집니다.


### `opts.select` 옵션

- 선택 사항
- `(loaderData: TLoaderData) => TSelected`
- 이 함수를 제공하면, 로더 데이터와 함께 호출됩니다. 반환된 값은 `useLoaderData`에서 반환됩니다. 또한 이 값은 **얕은 비교(shallow equality)**를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 **구조적 공유(structural sharing)**를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useLoaderData 반환값

- `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
- `select` 함수가 제공되지 않은 경우, `opts.strict`가 `false`일 때는 로더 데이터의 느슨한 버전을, 그렇지 않으면 로더 데이터 자체를 반환합니다.


## 예제

```tsx
import { useLoaderData } from '@tanstack/react-router'

function Component() {
  const loaderData = useLoaderData({ from: '/posts/$postId' })
  //     ^? { postId: string, body: string, ... }
  // ...
}
```

위 예제는 `@tanstack/react-router` 라이브러리에서 제공하는 `useLoaderData` 훅을 사용하는 방법을 보여줍니다. 이 훅은 특정 라우트에서 로드된 데이터를 가져오는 데 사용됩니다. `from` 옵션을 통해 데이터를 가져올 라우트를 지정할 수 있습니다. 반환된 `loaderData` 객체는 해당 라우트에서 정의된 데이터 구조를 따릅니다. 예를 들어, `postId`와 `body` 같은 필드를 포함할 수 있습니다.


