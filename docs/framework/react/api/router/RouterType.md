# Table of Contents

- [라우터 타입](#라우터-타입)
  - [`Router`의 속성과 메서드](#router의-속성과-메서드)
    - [`.update` 메서드](#update-메서드)
    - [`state` 속성](#state-속성)
    - [`.subscribe` 메서드](#subscribe-메서드)
    - [`.matchRoutes` 메서드](#matchroutes-메서드)
    - [`.cancelMatch` 메서드](#cancelmatch-메서드)
    - [`.cancelMatches` 메서드](#cancelmatches-메서드)
    - [`.buildLocation` 메서드](#buildlocation-메서드)
    - [`.commitLocation` 메서드](#commitlocation-메서드)
    - [`.navigate` 메서드](#navigate-메서드)
    - [`.invalidate` 메서드](#invalidate-메서드)
    - [`.clearCache` 메서드](#clearcache-메서드)
    - [`.load` 메서드](#load-메서드)
    - [`.preloadRoute` 메서드](#preloadroute-메서드)
    - [`.loadRouteChunk` 메서드](#loadroutechunk-메서드)
    - [`.matchRoute` 메서드](#matchroute-메서드)
    - [`.dehydrate` 메서드](#dehydrate-메서드)
    - [`.hydrate` 메서드](#hydrate-메서드)

# 라우터 타입

`Router` 타입은 라우터 인스턴스를 설명하는 데 사용됩니다.


## `Router`의 속성과 메서드

`Router` 인스턴스는 다음과 같은 속성과 메서드를 가지고 있습니다:


### `.update` 메서드

- 타입: `(newOptions: RouterOptions) => void`
- 라우터 인스턴스를 새로운 옵션으로 업데이트합니다.


### `state` 속성

- 타입: [`RouterState`](./RouterStateType.md)
- 라우터의 현재 상태를 나타냅니다.

> ⚠️⚠️⚠️ **`router.state`는 항상 최신 상태를 유지하지만, 반응형(REACTIVE)이 아닙니다. 컴포넌트에서 `router.state`를 사용하면 라우터 상태가 변경되어도 컴포넌트가 리렌더링되지 않습니다. 반응형 라우터 상태를 얻으려면 [`useRouterState`](./useRouterStateHook.md) 훅을 사용하세요.**


### `.subscribe` 메서드

- 타입: `(eventType: TType, fn: ListenerFn<RouterEvents[TType]>) => (event: RouterEvent) => void`
- [`RouterEvent`](./RouterEventsType.md)를 구독합니다.
- 이벤트 구독을 해제할 수 있는 함수를 반환합니다.
- 반환된 함수에 제공된 콜백은 발생한 이벤트와 함께 호출됩니다.

```typescript
// 예제 코드
const unsubscribe = router.subscribe('NAVIGATION_START', (event) => {
  console.log('Navigation started:', event);
});

// 구독 해제
unsubscribe();
```


### `.matchRoutes` 메서드

- **타입**: `(pathname: string, locationSearch: Record<string, any>, opts?: { throwOnError?: boolean; }) => RouteMatch[]`
- **기능**: 주어진 경로명(`pathname`)과 검색 파라미터(`locationSearch`)를 라우터의 라우트 트리와 비교하여 일치하는 라우트 매치 배열을 반환합니다.
- **옵션**: 
  - `opts.throwOnError`가 `true`로 설정된 경우, 매칭 과정에서 발생한 모든 오류가 라우트 매치의 `error` 속성에 반환되는 것 외에도 예외로 던져집니다.

```typescript
// 예제 코드
const matches = matchRoutes(pathname, locationSearch, { throwOnError: true });
```


### `.cancelMatch` 메서드

- 타입: `(matchId: string) => void`
- 현재 대기 중인 라우트 매치를 취소합니다. `match.abortController.abort()`를 호출하여 취소를 수행합니다.


### `.cancelMatches` 메서드

- 타입: `() => void`
- 현재 보류 중인 모든 라우트 매치를 취소합니다. 각 매치에 대해 `match.abortController.abort()`를 호출하여 처리합니다.


### `.buildLocation` 메서드

새로운 파싱된 위치 객체를 생성합니다. 이 객체는 나중에 새로운 위치로 네비게이션할 때 사용할 수 있습니다.

- 타입: `(opts: BuildNextOptions) => ParsedLocation`
- 속성
  - `from`
    - 타입: `string`
    - 선택 사항
    - 네비게이션을 시작할 경로입니다. 제공되지 않으면 현재 경로가 사용됩니다.
  - `to`
    - 타입: `string | number | null`
    - 선택 사항
    - 네비게이션할 경로입니다. `null`이면 현재 경로가 사용됩니다.
  - `params`
    - 타입: `true | Updater<unknown>`
    - 선택 사항
    - `true`이면 현재 파라미터가 사용됩니다. 함수가 제공되면 현재 파라미터를 인자로 받아 반환된 값이 사용됩니다.
  - `search`
    - 타입: `true | Updater<unknown>`
    - 선택 사항
    - `true`이면 현재 검색 파라미터가 사용됩니다. 함수가 제공되면 현재 검색 파라미터를 인자로 받아 반환된 값이 사용됩니다.
  - `hash`
    - 타입: `true | Updater<string>`
    - 선택 사항
    - `true`이면 현재 해시가 사용됩니다. 함수가 제공되면 현재 해시를 인자로 받아 반환된 값이 사용됩니다.
  - `state`
    - 타입: `true | NonNullableUpdater<HistoryState>`
    - 선택 사항
    - `true`이면 현재 상태가 사용됩니다. 함수가 제공되면 현재 상태를 인자로 받아 반환된 값이 사용됩니다.
  - `mask`
    - 타입: `object`
    - 선택 사항
    - `BuildNextOptions`와 동일한 속성을 포함하며, 추가로 `unmaskOnReload` 속성이 있습니다.
    - `unmaskOnReload`
      - 타입: `boolean`
      - 선택 사항
      - `true`이면 페이지가 다시 로드될 때 라우트 마스크가 제거됩니다. 이 설정은 `Navigate` 옵션에서 `unmaskOnReload`를 설정하여 네비게이션마다 재정의할 수 있습니다.


### `.commitLocation` 메서드

브라우저 히스토리에 새로운 위치 객체를 커밋합니다.

- 타입
  ```tsx
  type commitLocation = (
    location: ParsedLocation & {
      replace?: boolean
      resetScroll?: boolean
      hashScrollIntoView?: boolean | ScrollIntoViewOptions
      ignoreBlocker?: boolean
    },
  ) => Promise
  ```
- 속성
  - `location`
    - 타입: [`ParsedLocation`](./ParsedLocationType.md)
    - 필수
    - 브라우저 히스토리에 커밋할 위치입니다.
  - `replace`
    - 타입: `boolean`
    - 선택 사항
    - 기본값: `false`
    - `true`로 설정하면, `history.push` 대신 `history.replace`를 사용하여 브라우저 히스토리에 위치를 커밋합니다.
  - `resetScroll`
    - 타입: `boolean`
    - 선택 사항
    - 기본값: `true`
    - 위치가 브라우저 히스토리에 커밋된 후 스크롤 위치를 0,0으로 초기화합니다.
    - `false`로 설정하면, 스크롤 위치가 초기화되지 않습니다.
  - `hashScrollIntoView`
    - 타입: `boolean | ScrollIntoViewOptions`
    - 선택 사항
    - 기본값: `true`
    - 위치가 브라우저 히스토리에 커밋된 후 해시와 일치하는 ID를 가진 엘리먼트가 화면에 스크롤됩니다.
    - `false`로 설정하면, 해당 엘리먼트가 스크롤되지 않습니다.
    - 객체를 제공하면, `scrollIntoView` 메서드에 옵션으로 전달됩니다.
    - `ScrollIntoViewOptions`에 대한 자세한 내용은 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)을 참고하세요.
  - `ignoreBlocker`
    - 타입: `boolean`
    - 선택 사항
    - 기본값: `false`
    - `true`로 설정하면, 네비게이션을 방해할 수 있는 블로커를 무시합니다.


### `.navigate` 메서드

새로운 위치로 이동합니다.

- 타입
  ```tsx
  type navigate = (options: NavigateOptions) => Promise
  ```


### `.invalidate` 메서드

라우트 매치를 무효화하여 `beforeLoad`와 `load` 함수를 다시 호출하도록 강제합니다.

- 타입: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean, sync?: boolean}) => Promise<void>`
- 이 메서드는 로더 데이터가 오래되거나 최신 상태가 아닐 때 유용합니다. 예를 들어, 게시물 목록을 표시하는 라우트가 있고, API에서 게시물 목록을 가져오는 로더 함수가 있다면, 새로운 게시물이 생성될 때마다 해당 라우트의 매치를 무효화하여 게시물 목록이 항상 최신 상태를 유지하도록 할 수 있습니다.
- `filter`가 제공되지 않으면 모든 매치가 무효화됩니다.
- `filter`가 제공되면, `filter`가 `true`를 반환하는 매치만 무효화됩니다.
- `sync`가 `true`로 설정되면, 이 함수가 반환하는 Promise는 모든 로더가 완료된 후에만 resolve됩니다.
- 또한, 라우터의 `CatchBoundary`를 명령적으로 `reset`하여 로더를 다시 트리거하려는 경우에도 라우터를 무효화할 수 있습니다.


### `.clearCache` 메서드

캐시된 라우트 매치를 제거합니다.

- 타입: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean}) => void`
- `filter`를 제공하지 않으면 모든 캐시된 매치가 제거됩니다.
- `filter`를 제공하면 `filter`가 `true`를 반환하는 매치만 제거됩니다.

```typescript
// 예제: 특정 조건에 맞는 캐시된 라우트 매치 제거
router.clearCache({
  filter: (match) => match.path === '/example'
});
```


### `.load` 메서드

현재 매칭된 라우트를 모두 로드하고, 모든 데이터가 준비되어 렌더링할 수 있는 상태가 되면 해결(resolve)됩니다.

> ⚠️⚠️⚠️ **`router.load()`는 `route.staleTime`을 존중하며, 아직 유효한 라우트 매칭을 강제로 다시 로드하지 않습니다. 라우트 매칭을 강제로 다시 로드해야 한다면, 대신 `router.invalidate()`를 사용하세요.**

- 타입: `(opts?: {sync?: boolean}) => Promise<void>`
- `sync`가 `true`인 경우, 이 함수가 반환하는 Promise는 모든 로더가 완료된 후에만 해결됩니다.
- 이 메서드는 주로 SSR(서버 사이드 렌더링)을 할 때 사용됩니다. 클라이언트에게 애플리케이션을 스트리밍하거나 렌더링하기 전에 현재 라우트의 중요한 데이터가 모두 로드되었는지 확인하기 위해 호출합니다.

```javascript
// 예제: SSR에서 .load() 사용
router.load({ sync: true }).then(() => {
  // 모든 데이터가 로드된 후 렌더링 진행
  renderApp();
});
```


### `.preloadRoute` 메서드

제공된 `NavigateOptions`와 일치하는 모든 매치를 미리 로드합니다.

> ⚠️⚠️⚠️ **미리 로드된 라우트 매치는 라우터 상태에 장기간 저장되지 않습니다. 다음 네비게이션 시도가 있을 때까지만 저장됩니다.**

- 타입: `(opts?: NavigateOptions) => Promise<RouteMatch[]>`
- 속성
  - `opts`
    - 타입: `NavigateOptions`
    - 선택 사항이며, 기본값은 현재 위치입니다.
    - 미리 로드할 라우트 매치를 결정하는 데 사용되는 옵션입니다.
- 반환값
  - 미리 로드된 모든 라우트 매치 배열로 해결되는 Promise입니다.


### `.loadRouteChunk` 메서드

라우트의 JS 청크를 불러옵니다.

- 타입: `(route: AnyRoute) => Promise<void>`

```typescript
// 예제 코드
const loadRouteChunk = async (route: AnyRoute): Promise<void> => {
    // 라우트의 JS 청크를 비동기적으로 불러오는 로직
    await import(`./routes/${route}.js`);
};
```

이 메서드는 주어진 라우트에 해당하는 JavaScript 청크를 비동기적으로 불러오는 역할을 합니다. `Promise<void>`를 반환하므로, 청크 로딩이 완료되면 프로미스가 해결됩니다.


### `.matchRoute` 메서드

주어진 경로명(pathname)과 검색 파라미터(search params)를 라우터의 라우트 트리와 비교하여 일치하는 라우트 매치의 파라미터를 반환합니다. 일치하는 라우트가 없으면 `false`를 반환합니다.

- 타입: `(dest: ToOptions, matchOpts?: MatchRouteOptions) => RouteMatch | false`
- 속성
  - `dest`
    - 타입: `ToOptions`
    - 필수
    - 비교할 대상 경로입니다.
  - `matchOpts`
    - 타입: `MatchRouteOptions`
    - 선택 사항
    - 경로를 비교할 때 사용할 옵션입니다.
- 반환값
  - 일치하는 라우트가 있으면 라우트 매치 객체를 반환합니다.
  - 일치하는 라우트가 없으면 `false`를 반환합니다.


### `.dehydrate` 메서드

라우터의 핵심 상태를 직렬화 가능한 객체로 변환합니다. 이 객체는 초기 요청 시 클라이언트로 전송할 수 있습니다.

- 타입: `() => DehydratedRouter`
- 반환값
  - 라우터의 핵심 상태를 포함한 직렬화 가능한 객체


### `.hydrate` 메서드

서버에서 초기 요청 시 전송된 직렬화 가능한 객체로부터 라우터의 중요한 상태를 복원합니다.

- 타입: `(dehydrated: DehydratedRouter) => void`
- 속성
  - `dehydrated`
    - 타입: `DehydratedRouter`
    - 필수 여부: 필수
    - 설명: 서버에서 전송된 라우터의 상태를 담은 객체입니다.


