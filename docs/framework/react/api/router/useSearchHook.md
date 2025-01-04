# Table of Contents

- [useSearch 훅](#usesearch-훅)
  - [useSearch 옵션](#usesearch-옵션)
    - [`opts.from` 옵션](#optsfrom-옵션)
    - [`opts.select` 옵션](#optsselect-옵션)
    - [`opts.structuralSharing` 옵션](#optsstructuralsharing-옵션)
    - [`opts.strict` 옵션](#optsstrict-옵션)
  - [useSearch 반환값](#usesearch-반환값)
  - [예제](#예제)

# useSearch 훅

`useSearch` 메서드는 현재 위치의 검색 쿼리 파라미터를 객체로 반환하는 훅입니다. 이 훅은 컴포넌트에서 현재 검색 문자열과 쿼리 파라미터에 접근할 때 유용합니다.


## useSearch 옵션

`useSearch` 훅은 `options` 객체를 인자로 받습니다.


### `opts.from` 옵션

- 타입: `string`
- 필수 여부: 필수
- 설명: 검색 쿼리 파라미터를 가져올 라우트 ID를 지정합니다.


### `opts.select` 옵션

- 타입: `(search: SelectedSearchSchema) => TSelected`
- 선택 사항
- 이 함수를 제공하면, 검색 객체와 함께 호출되며 반환 값은 `useSearch`에서 반환됩니다.

```typescript
// 예제 코드
const selectedData = useSearch({
  select: (search) => {
    // 검색 객체에서 필요한 데이터를 선택하여 반환
    return search.results.map(result => result.data);
  }
});
```

이 옵션을 사용하면, 검색 결과에서 특정 데이터만 선택적으로 추출할 수 있습니다. `select` 함수는 검색 객체를 인자로 받아 원하는 형태로 가공한 후 반환합니다. 이렇게 반환된 값은 `useSearch` 훅의 결과로 사용됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select`가 반환하는 값에 대해 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


### `opts.strict` 옵션

- 타입: `boolean`
- 선택 사항 - `기본값: true`
- `false`로 설정하면 `opts.from` 옵션이 무시되고, 모든 검색 쿼리 파라미터의 공통 타입을 반영하기 위해 타입이 `Partial<FullSearchSchema>`로 완화됩니다.


## useSearch 반환값

- `opts.from`이 제공된 경우, 현재 위치의 검색 쿼리 파라미터 객체를 반환합니다. 만약 `select` 함수가 제공되었다면, `TSelected` 타입의 값을 반환합니다.
- `opts.strict`가 `false`인 경우, 현재 위치의 검색 쿼리 파라미터 객체를 반환합니다. 만약 `select` 함수가 제공되었다면, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useSearch } from '@tanstack/react-router'

function Component() {
  const search = useSearch({ from: '/posts/$postId' })
  //    ^ FullSearchSchema

  // 또는

  const selected = useSearch({
    from: '/posts/$postId',
    select: (search) => search.postView,
  })
  //    ^ string

  // 또는

  const looseSearch = useSearch({ strict: false })
  //    ^ Partial

  // ...
}
```


