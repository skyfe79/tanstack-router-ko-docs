# RouteOptions 타입

`RouteOptions` 타입은 라우트를 생성할 때 사용할 수 있는 옵션들을 정의합니다.


## RouteOptions 속성

`RouteOptions` 타입은 다음과 같은 속성을 가진 객체를 받습니다:


### `getParentRoute` 메서드

- 타입: `() => TParentRoute`
- 필수 여부: 필수
- 이 함수는 생성 중인 라우트의 부모 라우트를 반환합니다. 자식 라우트 설정에 완전한 타입 안전성을 제공하고, 라우트 트리가 올바르게 구성되도록 보장하기 위해 필수적으로 필요합니다.


### `path` 속성

- 타입: `string`
- 필수: 라우트를 레이아웃 라우트로 구성하기 위해 `id`를 제공하지 않는 한 필수입니다.
- 라우트를 매칭하는 데 사용될 경로 세그먼트입니다.


### `id` 속성

- 타입: `string`
- 선택 사항이지만, `path`가 제공되지 않은 경우 필수
- 라우트를 레이아웃 라우트로 구성할 때 사용하는 고유 식별자입니다. 이 속성이 제공되면, 라우트는 위치 경로명(location pathname)과 일치하지 않으며, 해당 라우트의 하위 라우트는 부모 라우트에 병합되어 매칭됩니다.


### `component` 속성

- 타입: `RouteComponent` 또는 `LazyRouteComponent`
- 선택 사항 - 기본값: `<Outlet />`
- 라우트가 매칭될 때 렌더링할 콘텐츠를 지정합니다.


### `errorComponent` 속성

- 타입: `RouteComponent` 또는 `LazyRouteComponent`
- 선택 사항 - 기본값: `routerOptions.defaultErrorComponent`
- 라우트에서 오류가 발생했을 때 렌더링할 내용을 지정합니다.


### `pendingComponent` 속성

- 타입: `RouteComponent` 또는 `LazyRouteComponent`
- 선택 사항 - 기본값: `routerOptions.defaultPendingComponent`
- 라우트가 대기 상태(pending)에 있고, `pendingMs` 임계값에 도달했을 때 렌더링할 내용을 지정합니다.


### `notFoundComponent` 속성

- 타입: `NotFoundRouteComponent` 또는 `LazyRouteComponent`
- 선택 사항 - 기본값: `routerOptions.defaultNotFoundComponent`
- 라우트를 찾을 수 없을 때 렌더링할 내용을 지정합니다.


### `validateSearch` 메서드

- 타입: `(rawSearchParams: unknown) => TSearchSchema`
- 선택 사항
- 이 함수는 현재 위치의 원시 검색 파라미터를 받아 유효한 파싱된 검색 파라미터를 반환합니다. 이 함수가 호출되면 해당 라우트가 매칭되고, 검색 파라미터가 전달됩니다. 만약 이 함수에서 오류가 발생하면, 라우트는 오류 상태가 되고, 렌더링 중에 오류가 발생합니다. 오류가 발생하지 않으면, 반환된 값이 라우트의 검색 파라미터로 사용되며, 반환 타입은 라우터의 나머지 부분에서 추론됩니다.
- 선택적으로, 파라미터 타입에 `SearchSchemaInput` 타입을 태그로 추가할 수 있습니다. 예를 들어, `(searchParams: TSearchSchemaInput & SearchSchemaInput) => TSearchSchema`와 같이 사용할 수 있습니다. 이 태그가 있으면, `TSearchSchemaInput`이 `<Link />`와 `navigate()`의 `search` 속성에 대한 타입으로 사용됩니다. 이때 `TSearchSchema` 대신 `TSearchSchemaInput`이 사용됩니다. `TSearchSchemaInput`과 `TSearchSchema`의 차이는 예를 들어, 선택적 검색 파라미터를 표현하는 데 유용할 수 있습니다.


### `search.middlewares` 속성

- 타입: `(({search: TSearchSchema, next: (newSearch: TSearchSchema) => TSearchSchema}) => TSearchSchema)[]`
- 선택 사항
- 검색 미들웨어는 라우트나 그 하위 라우트에 대한 새로운 링크를 생성할 때 검색 파라미터를 변환하는 함수입니다.
- 검색 미들웨어는 현재 검색 파라미터를 받습니다. 첫 번째 미들웨어라면 현재 검색 파라미터를, 그렇지 않다면 이전 미들웨어가 `next`를 호출하여 전달한 파라미터를 받습니다.


### `parseParams` 메서드 (⚠️ 사용 중단됨)

- 타입: `(rawParams: Record<string, string>) => TParams`
- 선택 사항
- 이 함수는 현재 라우트가 매치될 때 호출되며, 현재 위치에서의 원시 파라미터(rawParams)를 전달받아 유효한 파싱된 파라미터를 반환합니다. 이 함수에서 오류가 발생하면, 라우트는 오류 상태로 전환되고, 렌더링 중에 해당 오류가 발생합니다. 오류가 발생하지 않으면, 반환된 값이 라우트의 파라미터로 사용되며, 반환 타입은 라우터의 나머지 부분에서 추론됩니다.


### `stringifyParams` 메서드 (⚠️ 사용 중단됨)

- 타입: `(params: TParams) => Record<string, string>`
- `parseParams`가 제공된 경우 필수
- 이 라우트의 파싱된 파라미터가 위치 정보를 구성하는 데 사용될 때 호출되는 함수입니다. 이 함수는 `Record<string, string>` 형태의 유효한 객체를 반환해야 합니다.


### `params.parse` 메서드

- 타입: `(rawParams: Record<string, string>) => TParams`
- 선택 사항
- 이 함수는 현재 라우트가 매치될 때 호출되며, 현재 위치에서의 원시 파라미터(raw params)를 받아 유효한 파싱된 파라미터를 반환합니다. 이 함수가 예외를 던지면, 라우트는 에러 상태가 되고, 렌더링 중에 해당 에러가 발생합니다. 예외를 던지지 않으면, 반환된 값이 라우트의 파라미터로 사용되며, 반환 타입은 라우터의 나머지 부분에서 추론됩니다.


### `params.stringify` 메서드

- 타입: `(params: TParams) => Record<string, string>`
- 이 라우트의 파싱된 파라미터를 사용하여 위치를 생성할 때 호출되는 함수입니다. 이 함수는 `Record<string, string>` 형태의 유효한 객체를 반환해야 합니다.

```typescript
// 예제 코드
const stringifyParams = (params: TParams): Record<string, string> => {
  return {
    id: params.id.toString(),
    name: params.name,
  };
};
```


### `beforeLoad` 메서드

- 타입:

```tsx
type beforeLoad = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TParentContext
    location: ParsedLocation
    navigate: NavigateFn // @deprecated
    buildLocation: BuildLocationFn
    cause: 'enter' | 'stay'
  },
) => Promise | TRouteContext | void
```

- 선택 사항
- [`ParsedLocation`](./ParsedLocationType.md)
- 이 비동기 함수는 라우트가 로드되기 전에 호출됩니다. 여기서 오류가 발생하면 라우트의 로더가 호출되지 않고 라우트도 렌더링되지 않습니다. 네비게이션 중에 오류가 발생하면 네비게이션이 취소되고 오류는 `onError` 함수로 전달됩니다. 미리 가져오기(preload) 이벤트 중에 오류가 발생하면 콘솔에 로그가 기록되고 미리 가져오기가 실패합니다.
- 이 함수가 Promise를 반환하면 라우트는 대기 상태가 되고, Promise가 해결될 때까지 렌더링이 일시 중단됩니다. 이 라우트의 `pendingMs` 임계값에 도달하면 `pendingComponent`가 Promise가 해결될 때까지 표시됩니다. Promise가 거부되면 라우트는 오류 상태가 되고, 렌더링 중에 오류가 발생합니다.
- 이 함수가 `TRouteContext` 객체를 반환하면, 해당 객체는 라우트의 컨텍스트에 병합되어 `loader` 및 관련된 다른 라우트 컴포넌트/메서드에서 사용할 수 있습니다.
- 이 함수는 일반적으로 사용자가 인증되었는지 확인하고, 인증되지 않은 경우 로그인 페이지로 리디렉션하는 데 사용됩니다. 이를 위해 이 함수에서 `redirect` 객체를 반환하거나 던질 수 있습니다.

> 🚧 `opts.navigate`는 더 이상 사용되지 않으며 다음 주요 릴리스에서 제거될 예정입니다. 대신 `throw redirect({ to: '/somewhere' })`를 사용하세요. `redirect` 함수에 대한 자세한 내용은 [여기](./redirectFunction.md)에서 확인할 수 있습니다.


### `loader` 메서드

- 타입:

```tsx
type loader = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TAllContext
    location: ParsedLocation
    navigate: NavigateFn // @deprecated
    buildLocation: BuildLocationFn
    cause: 'enter' | 'stay'
  },
) => Promise | TLoaderData | void
```

- 선택 사항
- [`ParsedLocation`](./ParsedLocationType.md) 참조
- 이 비동기 함수는 라우트가 매칭될 때 호출되며, 라우트의 매치 객체를 인자로 받습니다. 여기서 에러가 발생하면 라우트는 에러 상태로 전환되고, 렌더링 중에 해당 에러가 발생합니다. 네비게이션 중에 에러가 발생하면 네비게이션이 취소되고, 에러는 `onError` 함수로 전달됩니다. 미리 가져오기(preload) 이벤트 중에 에러가 발생하면 콘솔에 로그가 기록되고, 미리 가져오기가 실패합니다.
- 이 함수가 Promise를 반환하면, 라우트는 대기 상태로 전환되고, Promise가 해결될 때까지 렌더링이 일시 중단됩니다. 라우트의 `pendingMs` 임계값에 도달하면, `pendingComponent`가 Promise가 해결될 때까지 표시됩니다. Promise가 거부되면, 라우트는 에러 상태로 전환되고, 렌더링 중에 해당 에러가 발생합니다.
- 이 함수가 `TLoaderData` 객체를 반환하면, 해당 객체는 라우트 매치가 활성 상태인 동안 라우트 매치에 저장됩니다. 이 객체는 다른 `<Outlet />`이 렌더링되기 전에 라우트 매치의 자식 컴포넌트에서 `useLoaderData` 훅을 사용해 접근할 수 있습니다.

> 🚧 `opts.navigate`는 더 이상 사용되지 않으며(deprecated), 다음 주요 릴리스에서 제거될 예정입니다. 대신 `throw redirect({ to: '/somewhere' })`를 사용하세요. `redirect` 함수에 대한 자세한 내용은 [여기](./redirectFunction.md)에서 확인할 수 있습니다.


### `loaderDeps` 메서드

- 타입:

```tsx
type loaderDeps = (opts: { search: TFullSearchSchema }) => Record
```

- 선택 사항
- 이 함수는 라우트가 매칭되기 전에 호출되며, 라우트 매칭에 추가적인 고유 식별자를 제공하고 매칭이 다시 로드되어야 할 때의 의존성 추적기 역할을 합니다. 이 함수는 네비게이션 간에 라우트 매칭을 고유하게 식별할 수 있는 직렬화 가능한 값을 반환해야 합니다.
- 기본적으로 경로 매개변수(path params)는 이미 라우트 매칭을 고유하게 식별하는 데 사용되므로, 여기서 반환할 필요가 없습니다.
- 라우트 매칭이 검색 매개변수(search params)에 의존하여 고유하게 식별되는 경우, `loader`의 `deps` 인자에서 사용할 수 있도록 여기서 반환해야 합니다.


### `staleTime` 속성

- **타입**: `number`
- **선택 사항**
- **기본값**: `routerOptions.defaultStaleTime` (기본값은 `0`)
- **설명**: 라우트 매치의 로더 데이터가 신선한 상태로 간주되는 시간(밀리초 단위)을 지정합니다. 이 시간 내에 동일한 라우트 매치가 다시 발생하면, 해당 로더 데이터는 다시 불러오지 않습니다.


### `preloadStaleTime` 속성

- 타입: `number`
- 선택 사항
- 기본값: `routerOptions.defaultPreloadStaleTime` (기본값은 `30_000` ms, 즉 30초)
- **설명**:  
  라우트 매치의 로더 데이터가 미리 가져오기(preloading) 시점에서 얼마 동안 신선한 상태로 유지될지 설정하는 시간(밀리초 단위)입니다. 이 시간 내에 동일한 라우트 매치를 다시 미리 가져오는 경우, 로더 데이터는 다시 불러오지 않습니다. 만약 이 시간 내에 네비게이션을 통해 라우트 매치가 로드되면, 일반적인 `staleTime`이 대신 사용됩니다.


### `gcTime` 속성

- 타입: `number`
- 선택 사항
- 기본값: `routerOptions.defaultGcTime` (기본값은 30분)
- 설명: 라우트 매치의 로더 데이터가 미리 가져오기(preload) 후 또는 더 이상 사용되지 않을 때 메모리에 유지되는 시간을 밀리초 단위로 지정합니다.


### `shouldReload` 속성

- 타입: `boolean | ((args: LoaderArgs) => boolean)`
- 선택 사항
- `false`를 반환하거나 `false`로 설정하면, 이후 매칭에서 라우트 매치의 로더 데이터가 다시 로드되지 않습니다.
- `true`를 반환하거나 `true`로 설정하면, 이후 매칭에서 라우트 매치의 로더 데이터가 다시 로드됩니다.
- `undefined`를 반환하거나 `undefined`로 설정하면, 라우트 매치의 로더 데이터는 기본적인 stale-while-revalidate 동작을 따릅니다.


### `caseSensitive` 속성

- 타입: `boolean`
- 선택 사항
- `true`로 설정하면, 이 라우트는 대소문자를 구분하여 매칭됩니다.


### `wrapInSuspense` 속성

- **타입**: `boolean`
- **선택 사항**
- `true`로 설정하면, 제공된 컴포넌트를 검사하여 Suspense 경계를 적용할 이유가 발견되지 않더라도, 이 라우트는 강제로 Suspense 경계로 감싸집니다.


### `pendingMs` 속성

- 타입: `number`
- 선택 사항
- 기본값: `routerOptions.defaultPendingMs` (기본값은 `1000`)
- 설명: 라우트가 `pendingComponent`를 보여주기 전에 대기해야 하는 시간(밀리초)을 설정합니다.


### `pendingMinMs` 속성

- 타입: `number`
- 선택 사항
- 기본값: `routerOptions.defaultPendingMinMs` (기본값은 `500`)
- 설명: 보류 중인 컴포넌트가 화면에 표시될 경우, 최소 표시 시간을 밀리초 단위로 설정합니다. 이 설정은 보류 중인 컴포넌트가 순간적으로 깜빡이는 현상을 방지하는 데 유용합니다.

```typescript
// 예제: pendingMinMs 속성 사용
const routerOptions = {
  defaultPendingMinMs: 1000 // 1초로 설정
};

const router = new Router({
  pendingMinMs: 800 // 800ms로 설정
});
```


### `preloadMaxAge` 속성

- 타입: `number`
- 선택 사항
- 기본값: `30_000` ms (30초)
- 라우트의 미리 가져온 데이터가 캐시될 최대 시간(밀리초 단위)을 지정합니다. 이 시간 내에 라우트가 매칭되지 않으면, 해당 라우트의 로더 데이터는 삭제됩니다.


### `preSearchFilters` 속성 (⚠️ 더 이상 사용되지 않음, `search.middlewares`를 대신 사용하세요)

- 타입: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 선택 사항
- 이 라우트나 그 자손 라우트에 대한 새로운 링크를 생성할 때 호출될 함수들의 배열입니다.
- 각 함수는 현재 검색 파라미터를 인자로 받고, 새로운 검색 파라미터 객체를 반환해야 합니다. 이 객체는 링크를 생성하는 데 사용됩니다.
- `pre` 접두사가 붙은 이유는, 사용자가 `navigate`나 `Link` 등에 전달한 함수가 검색 파라미터를 수정하기 전에 호출되기 때문입니다.


### `postSearchFilters` 속성 (⚠️ 더 이상 사용되지 않음, `search.middlewares`를 대신 사용하세요)

- 타입: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 선택 사항
- 이 라우트 또는 그 하위 라우트에 대한 새로운 링크를 생성할 때 호출될 함수들의 배열입니다.
- 각 함수는 현재의 검색 파라미터를 인자로 받고, 링크를 생성하는 데 사용될 새로운 검색 파라미터 객체를 반환해야 합니다.
- `post` 접두사가 붙은 이유는, 이 함수가 `navigate`/`Link` 등에 전달된 사용자 제공 함수가 검색 파라미터를 수정한 후에 호출되기 때문입니다.


### `onError` 속성

- 타입: `(error: any) => void`
- 선택 사항
- 네비게이션 또는 미리 가져오기(preload) 이벤트 중에 오류가 발생했을 때 호출되는 함수입니다.
- 이 함수가 [`redirect`](./redirectFunction.md)를 던지면, 라우터는 즉시 리다이렉트를 처리하고 적용합니다.


### `onEnter` 속성

- 타입: `(match: RouteMatch) => void`
- 선택 사항
- 이전 위치에서 매칭되지 않았던 라우트가 매칭되고 로드된 후에 호출될 함수입니다.


### `onStay` 속성

- 타입: `(match: RouteMatch) => void`
- 선택 사항
- 이전 위치에서 매칭된 후, 라우트가 매칭되고 로드될 때 호출되는 함수입니다.


### `onLeave` 속성

- 타입: `(match: RouteMatch) => void`
- 선택 사항
- 이전 위치에서 매칭되었던 라우트가 더 이상 매칭되지 않을 때 호출되는 함수입니다.


### `onCatch` 속성

- 타입: `(error: Error, errorInfo: ErrorInfo) => void`
- 선택 사항 - 기본값: `routerOptions.defaultOnCatch`
- 라우트에서 오류가 발생했을 때 호출되는 함수입니다. 이 함수는 오류가 잡히면 실행됩니다.


