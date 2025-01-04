# Table of Contents

- [AsyncRouteComponent 타입](#asyncroutecomponent-타입)

# AsyncRouteComponent 타입

`AsyncRouteComponent` 타입은 코드 분할된 라우트 컴포넌트를 설명하는 데 사용됩니다. 이 타입은 `component.preload()` 메서드를 통해 미리 로드할 수 있는 기능을 제공합니다.

```tsx
type AsyncRouteComponent = SyncRouteComponent & {
  preload?: () => Promise
}
```

- `SyncRouteComponent`: 동기식 라우트 컴포넌트를 나타냅니다.
- `preload`: 컴포넌트를 미리 로드하는 비동기 함수입니다. 이 함수는 `Promise`를 반환합니다.


