# useParams 훅

`useParams` 메서드는 가장 가까운 매치와 그 상위 매치들에서 파싱된 모든 경로 파라미터를 반환합니다.


## useParams 옵션

`useParams` 훅은 선택적으로 `options` 객체를 인자로 받을 수 있습니다.


### `opts.strict` 옵션

- 타입: `boolean`
- 선택 사항 - 기본값: `true`
- `false`로 설정하면, `opts.from` 옵션이 무시되고 모든 파라미터의 공통 타입을 반영하기 위해 타입이 `Partial<AllParams>`로 완화됩니다.


### `opts.select` 옵션

- **선택 사항**
- `(params: AllParams) => TSelected` 타입의 함수
- 이 함수를 제공하면, `params` 객체를 인자로 받아 호출됩니다. 반환된 값은 `useParams` 훅에서 반환되며, 이 값은 **얕은 비교(shallow equality)**를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useParams 반환값

- 매치된 경로와 부모 경로의 파라미터를 담은 객체를 반환합니다. 만약 `select` 함수가 제공된 경우, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useParams } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  // 방법 1: useParams 훅 사용
  const params = useParams({ from: '/posts/$postId' })

  // 방법 2: routeApi를 통해 파라미터 접근
  const routeParams = routeApi.useParams()

  // 방법 3: 특정 파라미터만 선택적으로 추출
  const postId = useParams({
    from: '/posts/$postId',
    select: (params) => params.postId,
  })

  // 방법 4: 엄격한 검사 없이 파라미터 접근
  const looseParams = useParams({ strict: false })

  // ...
}
```

이 예제에서는 `useParams` 훅을 다양한 방식으로 사용하는 방법을 보여줍니다. 각 방법은 라우트 파라미터를 가져오는 데 유용하며, 필요에 따라 적절한 방식을 선택할 수 있습니다.


