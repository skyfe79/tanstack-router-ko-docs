# Table of Contents

- [RouteMatch 타입](#routematch-타입)

# RouteMatch 타입

`RouteMatch` 타입은 TanStack Router에서 라우트 매치를 나타냅니다.

```tsx
interface RouteMatch {
  id: string // 고유 식별자
  routeId: string // 라우트 ID
  pathname: string // 경로명
  params: Route['allParams'] // 라우트 파라미터
  status: 'pending' | 'success' | 'error' // 상태
  isFetching: boolean // 데이터 가져오기 중 여부
  showPending: boolean // 로딩 상태 표시 여부
  error: unknown // 에러 정보
  paramsError: unknown // 파라미터 에러
  searchError: unknown // 검색 에러
  updatedAt: number // 마지막 업데이트 시간
  loadPromise?: Promise // 로딩 Promise
  loaderData?: Route['loaderData'] // 로더 데이터
  context: Route['allContext'] // 컨텍스트 정보
  search: Route['fullSearchSchema'] // 검색 정보
  fetchedAt: number // 데이터 가져온 시간
  abortController: AbortController // 중단 컨트롤러
  cause: 'enter' | 'stay' // 매치 원인
}
```


