# RouterState 타입

`RouterState` 타입은 라우터의 내부 상태를 나타냅니다. 라우터의 내부 상태는 라우터의 특정 내부 정보에 접근해야 할 때 유용합니다. 예를 들어, 현재 대기 중인 매칭 정보나 라우터가 로딩 상태인지 여부 등을 확인할 수 있습니다.

```tsx
type RouterState = {
  status: 'pending' | 'idle' // 현재 라우터 상태 (대기 중 또는 유휴 상태)
  isLoading: boolean // 로딩 중인지 여부
  isTransitioning: boolean // 트랜지션 중인지 여부
  matches: Array // 현재 매칭된 라우트 정보
  pendingMatches: Array // 대기 중인 매칭 정보
  location: ParsedLocation // 현재 위치 정보
  resolvedLocation: ParsedLocation // 해결된 위치 정보
}
```


## RouterState 속성

`RouterState` 타입은 라우터 상태에서 사용할 수 있는 모든 속성을 포함합니다.


### `status` 속성

- 타입: `'pending' | 'idle'`
- 라우터의 현재 상태를 나타냅니다. 라우터가 `pending` 상태라면, 현재 라우트를 로딩 중이거나 새로운 라우트로 전환 중임을 의미합니다.


### `isLoading` 속성

- 타입: `boolean`
- 라우터가 현재 라우트를 로딩 중이거나, 라우트 로딩이 완료되기를 기다리고 있다면 `true`가 됩니다.


### `isTransitioning` 속성

- 타입: `boolean`
- 라우터가 현재 새로운 라우트로 전환 중인 경우 `true`를 반환합니다.


### `matches` 프로퍼티

- 타입: [`Array<RouteMatch>`](./RouteMatchType.md)
- 현재 활성화되어 있고 해결된 모든 라우트 매치를 담은 배열입니다.


### `pendingMatches` 속성

- 타입: [`Array<RouteMatch>`](./RouteMatchType.md)
- 현재 보류 중인 모든 라우트 매치를 담은 배열입니다.


### `location` 프로퍼티

- 타입: [`ParsedLocation`](./ParsedLocationType.md)
- 라우터가 브라우저 히스토리에서 파싱한 최신 위치 정보입니다. 이 위치는 아직 해결되거나 로드되지 않았을 수 있습니다.


### `resolvedLocation` 속성

- 타입: [`ParsedLocation`](./ParsedLocationType.md)
- 라우터가 해석하고 로드한 위치를 나타냅니다.


