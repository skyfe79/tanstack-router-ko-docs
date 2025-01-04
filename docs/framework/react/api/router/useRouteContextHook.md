# useRouteContext 훅

`useRouteContext` 메서드는 현재 라우트의 컨텍스트를 반환하는 훅입니다. 이 훅은 컴포넌트에서 현재 라우트 컨텍스트에 접근할 때 유용합니다.


## useRouteContext 옵션

`useRouteContext` 훅은 `options` 객체를 인자로 받습니다.


### `opts.from` 옵션

- 타입: `string`
- 필수 여부: 필수
- 설명: 라우트 컨텍스트를 매칭할 RouteID를 지정합니다.


### `opts.select` 옵션

- 타입: `(context: RouteContext) => TSelected`
- 선택 사항
- 이 함수를 제공하면, 라우트 컨텍스트 객체와 함께 호출됩니다. 반환된 값은 `useRouteContext`에서 반환됩니다.

```typescript
// 예제 코드
const selectedValue = useRouteContext({
  select: (context) => context.someValue,
});
```

이 옵션을 사용하면, 라우트 컨텍스트에서 특정 값을 선택적으로 추출할 수 있습니다.


## useRouteContext 반환값

- 현재 라우트의 컨텍스트를 반환합니다. 만약 `select` 함수가 제공된 경우, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useRouteContext } from '@tanstack/react-router'

function Component() {
  const context = useRouteContext({ from: '/posts/$postId' })
  //    ^ RouteContext 타입

  // 또는

  const selected = useRouteContext({
    from: '/posts/$postId',
    select: (context) => context.postId,
  })
  //    ^ string 타입

  // ...
}
```

- `useRouteContext` 훅을 사용하여 라우트 컨텍스트에 접근할 수 있습니다.
- 첫 번째 예제에서는 전체 컨텍스트 객체를 가져옵니다. 이때 반환되는 타입은 `RouteContext`입니다.
- 두 번째 예제에서는 `select` 함수를 사용해 컨텍스트에서 특정 값(`postId`)만 선택적으로 추출합니다. 이 경우 반환 타입은 `string`이 됩니다.


