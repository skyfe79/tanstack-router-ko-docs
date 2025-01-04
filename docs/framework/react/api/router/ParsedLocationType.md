# ParsedLocation 타입

`ParsedLocation` 타입은 TanStack Router에서 파싱된 위치를 나타냅니다. 이 타입은 현재 위치에 대한 다양한 유용한 정보를 포함하고 있으며, 경로명(pathname), 검색 파라미터(search params), 해시(hash), 위치 상태(location state), 그리고 라우트 마스킹 정보(route masking information) 등을 담고 있습니다.

```tsx
interface ParsedLocation {
  href: string // 전체 URL
  pathname: string // 경로명
  search: TFullSearchSchema // 검색 파라미터
  searchStr: string // 검색 파라미터 문자열
  state: HistoryState // 위치 상태
  hash: string // 해시
  maskedLocation?: ParsedLocation // 마스킹된 위치 정보
  unmaskOnReload?: boolean // 새로고침 시 마스크 해제 여부
}
```


