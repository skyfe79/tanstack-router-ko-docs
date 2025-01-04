# Table of Contents

- [ToOptions 타입](#tooptions-타입)

# ToOptions 타입

`ToOptions` 타입은 라우터 목적지를 설명하는 데 사용할 수 있는 여러 속성을 포함합니다.

```tsx
type ToOptions = {
  from?: ValidRoutePath | string  // 출발 경로
  to?: ValidRoutePath | string    // 도착 경로
  hash?: true | string | ((prev?: string) => string)  // 해시 값
  state?: true | HistoryState | ((prev: HistoryState) => HistoryState)  // 상태 값
} & SearchParamOptions &
  PathParamOptions

type SearchParamOptions = {
  search?: true | TToSearch | ((prev: TFromSearch) => TToSearch)  // 검색 파라미터
}

type PathParamOptions = {
  path?: true | Record | ((prev: TFromParams) => TToParams)  // 경로 파라미터
}
```


