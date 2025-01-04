# useMatch 훅

`useMatch` 훅은 컴포넌트 트리에서 [`RouteMatch`](./RouteMatchType.md)를 반환합니다. 이 원시 라우트 매치 객체는 라우터에서 라우트 매치에 대한 모든 정보를 포함하며, `useParams`, `useLoaderData`, `useRouteContext`, `useSearch`와 같은 다른 훅들도 내부적으로 이 정보를 활용합니다.


## useMatch 옵션

`useMatch` 훅은 하나의 인자로 `options` 객체를 받습니다.


### `opts.from` 옵션

- 타입: `string`
- 매치된 라우트의 ID
- 선택 사항이지만, 완전한 타입 안전성을 위해 권장됨
- `opts.strict`가 `true`인 경우, `from`은 필수이며, 이 옵션이 제공되지 않으면 TypeScript에서 경고가 발생함
- `opts.strict`가 `false`인 경우, `from`은 설정하면 안 되며, TypeScript는 반환된 [`RouteMatch`](./RouteMatchType.md)에 대해 느슨한 타입을 제공함


### `opts.strict` 옵션

- 타입: `boolean`
- 선택 사항
- 기본값: `true`
- `false`로 설정하면, `opts.from`을 설정할 수 없으며, 모든 매치의 공통 타입을 반영하기 위해 타입이 `Partial<RouteMatch>`로 완화됩니다.


### `opts.select` 옵션

- **선택 사항**
- `(match: RouteMatch) => TSelected` 타입의 함수
- 이 함수를 제공하면, 라우트 매치(`RouteMatch`)를 인자로 받아 호출됩니다. 반환된 값은 `useMatch` 훅에서 반환되며, 이 값은 부모 컴포넌트가 리렌더링해야 하는지 여부를 결정하기 위해 얕은 비교(shallow equality)를 통해 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select` 함수가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


### `opts.shouldThrow` 옵션

- 타입: `boolean`
- 선택 사항
- 기본값: `true`
- `false`로 설정하면, 현재 렌더링된 매치에서 일치하는 항목을 찾지 못했을 때 `useMatch`가 예외를 던지지 않습니다. 이 경우 `undefined`를 반환합니다.


## useMatch 반환값

- `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
- `select` 함수가 제공되지 않은 경우, [`RouteMatch`](./RouteMatchType.md) 객체를 반환합니다. 단, `opts.strict`가 `false`인 경우에는 완화된 버전의 `RouteMatch` 객체를 반환합니다.


## Examples





### 라우트 매치 접근하기

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts/$postId' })
  //     ^? RouteMatch에 대한 엄격한 매치
  // ...
}
```

- `useMatch` 훅을 사용하여 특정 라우트의 매치 정보에 접근할 수 있습니다.
- `from` 속성에 라우트 경로를 지정하면 해당 라우트와 일치하는지 확인합니다.
- `match` 변수는 라우트 매치 정보를 담고 있으며, 이를 통해 라우트 파라미터 등에 접근할 수 있습니다.


### 루트 라우트의 매치 정보 접근하기

```tsx
import {
  useMatch,
  rootRouteId, // <<<< 이 토큰을 사용하세요!
} from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: rootRouteId })
  //     ^? RouteMatch에 대한 엄격한 매치
  // ...
}
```

- `rootRouteId` 토큰을 사용하여 루트 라우트의 매치 정보에 접근할 수 있습니다.
- `useMatch` 훅을 통해 루트 라우트의 매치 정보를 가져올 수 있으며, 이는 `RouteMatch` 타입으로 엄격하게 정의됩니다.


### 특정 라우트가 현재 렌더링 중인지 확인하기

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts', shouldThrow: false })
  //     ^? RouteMatch | undefined
  if (match !== undefined) {
    // ...
  }
}
```

위 코드는 `@tanstack/react-router` 라이브러리의 `useMatch` 훅을 사용하여 특정 라우트가 현재 렌더링 중인지 확인하는 방법을 보여줍니다. 

- `useMatch` 훅은 `from` 속성에 지정된 라우트 경로(`/posts`)가 현재 활성화되어 있는지 확인합니다.
- `shouldThrow: false` 옵션은 라우트가 일치하지 않을 때 예외를 던지지 않고 `undefined`를 반환하도록 설정합니다.
- `match` 변수는 라우트가 일치하면 `RouteMatch` 타입의 값을, 그렇지 않으면 `undefined`를 반환합니다.
- `if (match !== undefined)` 조건문을 통해 라우트가 일치하는 경우에만 특정 로직을 실행할 수 있습니다.

이 방법을 사용하면 특정 라우트가 활성화되었을 때만 실행되어야 하는 코드를 쉽게 구현할 수 있습니다.


