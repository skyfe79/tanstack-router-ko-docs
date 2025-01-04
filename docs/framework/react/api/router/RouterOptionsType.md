# RouterOptions

`RouterOptions` 타입은 라우터 인스턴스를 구성하는 데 사용할 수 있는 모든 옵션을 포함합니다.


## RouterOptions 속성

`RouterOptions` 타입은 다음과 같은 속성과 메서드를 가진 객체를 받습니다:


### `routeTree` 속성

- 타입: `AnyRoute`
- 필수 여부: 필수
- 설명: 라우터 인스턴스를 구성하는 데 사용될 라우트 트리입니다.


### `history` 속성

- 타입: `RouterHistory`
- 선택 사항
- 브라우저 기록을 관리하는 데 사용될 `history` 객체입니다. 이 속성이 제공되지 않으면, 새로운 `createBrowserHistory` 인스턴스가 생성되어 사용됩니다.


### `stringifySearch` 메서드

- 타입: `(search: Record<string, any>) => string`
- 선택 사항
- 링크를 생성할 때 검색 파라미터를 문자열로 변환하는 데 사용되는 함수입니다.
- 기본값은 `defaultStringifySearch`입니다.


### `parseSearch` 메서드

- 타입: `(search: string) => Record<string, any>`
- 선택 사항
- 현재 위치를 파싱할 때 검색 파라미터를 파싱하는 데 사용되는 함수
- 기본값: `defaultParseSearch`

```typescript
// 예제: 커스텀 parseSearch 함수 구현
const customParseSearch = (search: string) => {
  const params = new URLSearchParams(search);
  const result: Record<string, any> = {};

  params.forEach((value, key) => {
    result[key] = value;
  });

  return result;
};
```


### `search.strict` 속성

- 타입: `boolean`
- 선택 사항
- 기본값: `false`
- 알려지지 않은 검색 파라미터(즉, `validateSearch`에서 반환되지 않은 파라미터)를 어떻게 처리할지 설정합니다.
- `false`인 경우, 알려지지 않은 검색 파라미터를 유지합니다.
- `true`인 경우, 알려지지 않은 검색 파라미터를 제거합니다.


### `defaultPreload` 속성

- 타입: `undefined | false | 'intent' | 'viewport' | 'render'`
- 선택 사항
- 기본값: `false`
- `false`일 경우, 기본적으로 어떤 방식으로도 라우트를 미리 가져오지 않습니다.
- `'intent'`일 경우, 사용자가 링크 위에 마우스를 올리거나 `<Link>`에서 `touchstart` 이벤트가 감지되면 기본적으로 라우트를 미리 가져옵니다.
- `'viewport'`일 경우, 브라우저의 뷰포트 안에 라우트가 들어오면 기본적으로 미리 가져옵니다.
- `'render'`일 경우, 라우트가 DOM에 렌더링되는 즉시 기본적으로 미리 가져옵니다.


### `defaultPreloadDelay` 속성

- 타입: `number`
- 선택 사항
- 기본값: `50`
- 라우트가 미리 가져오기(preload)되기 전에 마우스를 올리거나 터치해야 하는 지연 시간(밀리초 단위)입니다.


### `defaultComponent` 속성

- 타입: `RouteComponent`
- 선택 사항
- 기본값: `Outlet`
- 설명: 라우트에 컴포넌트가 제공되지 않을 때 사용할 기본 컴포넌트를 지정합니다.


### `defaultErrorComponent` 속성

- 타입: `RouteComponent`
- 선택 사항
- 기본값: `ErrorComponent`
- 에러 컴포넌트가 제공되지 않을 때 라우트가 사용할 기본 `errorComponent`입니다.


### `defaultNotFoundComponent` 속성

- 타입: `NotFoundRouteComponent`
- 선택 사항
- 기본값: `NotFound`
- 설명: 라우트에 `notFoundComponent`가 제공되지 않을 경우 사용할 기본 `notFoundComponent`를 지정합니다.


### `defaultPendingComponent` 속성

- 타입: `RouteComponent`
- 선택 사항
- `pendingComponent`가 제공되지 않을 때 라우트가 사용할 기본 `pendingComponent`를 지정합니다.


### `defaultPendingMs` 속성

- 타입: `number`
- 선택 사항
- 기본값: `1000`
- `pendingMs`가 제공되지 않을 때 라우트가 사용할 기본 `pendingMs` 값입니다.


### `defaultPendingMinMs` 속성

- **타입**: `number`
- **선택 사항**
- **기본값**: `500`
- **설명**: `pendingMinMs` 값이 제공되지 않을 때, 라우트가 사용할 기본 `pendingMinMs` 값을 지정합니다.


### `defaultStaleTime` 속성

- 타입: `number`
- 선택 사항
- 기본값: `0`
- `staleTime`이 제공되지 않을 경우, 라우트가 사용할 기본 `staleTime` 값을 설정합니다.


### `defaultPreloadStaleTime` 속성

- 타입: `number`
- 선택 사항
- 기본값: `30_000` ms (30초)
- 라우트에 `preloadStaleTime`이 제공되지 않을 때 사용할 기본 `preloadStaleTime` 값입니다.


### `defaultPreloadGcTime` 속성

- **타입**: `number`
- **선택 사항**
- **기본값**: `routerOptions.defaultGcTime` (기본값은 30분)
- **설명**: `preloadGcTime`이 제공되지 않을 때 라우트가 사용할 기본 `preloadGcTime` 값입니다.


### `defaultGcTime` 속성

- **타입**: `number`
- **선택 사항**
- **기본값**: 30분
- **설명**: `gcTime`이 제공되지 않을 때 라우트가 사용할 기본 `gcTime` 값입니다.


### `defaultOnCatch` 속성

- 타입: `(error: Error, errorInfo: ErrorInfo) => void`
- 선택 사항
- 라우터의 `ErrorBoundary`에서 잡힌 오류를 처리하기 위한 기본 `onCatch` 핸들러


### `defaultViewTransition` 속성

- 타입: `boolean | ViewTransitionOptions`
- 선택 사항
- `true`로 설정하면, 라우트 네비게이션은 `document.startViewTransition()`을 사용하여 호출됩니다.
- [`ViewTransitionOptions`](./ViewTransitionOptionsType.md)를 전달하면, 라우트 네비게이션은 `document.startViewTransition({update, types})`를 사용하여 호출됩니다. 이때 `types`는 `ViewTransitionOptions["types"]`로 전달된 문자열 배열이 됩니다. 만약 브라우저가 `viewTransition` 타입을 지원하지 않는다면, 네비게이션은 일반적인 `document.startTransition()`으로 대체되며, 이는 `true`를 전달한 경우와 동일하게 동작합니다.
- 브라우저가 이 API를 지원하지 않으면, 이 옵션은 무시됩니다.
- 이 함수가 어떻게 동작하는지에 대한 자세한 내용은 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)을 참고하세요.
- `viewTransition` 타입에 대한 추가 정보는 [Google](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)을 확인하세요.


### `defaultHashScrollIntoView` 속성

- 타입: `boolean | ScrollIntoViewOptions`
- 선택 사항
- 기본값: `true`로 설정되어 있어, 히스토리에 위치가 커밋된 후 해시와 일치하는 ID를 가진 엘리먼트가 화면에 스크롤되어 보이게 됩니다.
- `false`로 설정하면, 히스토리에 위치가 커밋된 후 해시와 일치하는 ID를 가진 엘리먼트가 화면에 스크롤되지 않습니다.
- 객체를 제공하면, 이 객체는 `scrollIntoView` 메서드에 옵션으로 전달됩니다.
- `ScrollIntoViewOptions`에 대한 자세한 내용은 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)을 참고하세요.


### `caseSensitive` 속성

- **타입**: `boolean`
- **선택 사항**
- **기본값**: `false`
- **설명**: `true`로 설정하면 모든 라우트가 대소문자를 구분하여 매칭됩니다.


### `basepath` 속성

- 타입: `string`
- 선택 사항
- 기본값: `/`
- 전체 라우터의 기본 경로를 설정합니다. 라우터 인스턴스를 하위 경로에 마운트할 때 유용합니다.


### `context` 속성

- 타입: `any`
- 선택 사항 또는 필수: 루트 라우트가 [`createRootRouteWithContext()`](./createRootRouteWithContextFunction.md)로 생성된 경우 필수
- 설명: 라우트 트리의 모든 라우트에 제공될 루트 컨텍스트. 이 속성을 사용하면 각 라우트에 개별적으로 컨텍스트를 제공하지 않고도 전체 라우트 트리에 컨텍스트를 제공할 수 있습니다.


### `dehydrate` 메서드

- 타입: `() => TDehydrated`
- 선택 사항
- 라우터가 **탈수 상태(dehydrated state)**로 전환될 때 호출되는 함수입니다. 이 함수의 반환 값은 직렬화되어 라우터의 탈수 상태에 저장됩니다.


### `hydrate` 메서드

- 타입: `(dehydrated: TDehydrated) => void`
- 선택 사항
- 라우터가 하이드레이션(hydration)될 때 호출되는 함수입니다. 이 함수의 반환 값은 직렬화되어 라우터의 하이드레이션 상태에 저장됩니다.


### `routeMasks` 속성

- 타입: `RouteMask[]`
- 선택 사항
- 라우트 트리에서 라우트를 마스킹하는 데 사용되는 라우트 마스크 배열입니다. 라우트 마스킹은 특정 라우트를 구성된 경로와 다른 경로에 표시하는 것을 의미합니다. 예를 들어, 모달 팝업을 공유할 때 모달의 컨텍스트 대신 모달의 내용이 표시되도록 설정할 수 있습니다.


### `unmaskOnReload` 속성

- 타입: `boolean`
- 선택 사항
- 기본값: `false`
- `true`로 설정하면, 페이지가 다시 로드될 때 기본적으로 라우트 마스크가 제거됩니다. 이 설정은 마스크 단위로 `unmaskOnReload` 옵션을 설정하거나, 네비게이션 단위로 `Navigate` 옵션에서 `unmaskOnReload`를 설정하여 재정의할 수 있습니다.


### `Wrap` 속성

- 타입: `React.Component`
- 선택 사항
- 전체 라우터를 감싸는 데 사용되는 컴포넌트입니다. 이 속성은 전체 라우터에 컨텍스트를 제공할 때 유용합니다. 프로바이더와 같은 DOM을 렌더링하지 않는 컴포넌트만 사용해야 하며, 그 외의 경우에는 하이드레이션 오류가 발생할 수 있습니다.

**예제**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  Wrap: ({ children }) => {
    return {children}
  },
})
```


### `InnerWrap` 속성

- 타입: `React.Component`
- 선택 사항
- 라우터 내부 콘텐츠를 감싸는 데 사용되는 컴포넌트입니다. 이 속성은 라우터 컨텍스트와 훅에 접근해야 하는 경우, 내부 콘텐츠에 컨텍스트를 제공하는 데 유용합니다. 프로바이더와 같은 **비 DOM 렌더링 컴포넌트**만 사용해야 하며, 그 외의 경우에는 하이드레이션 오류가 발생할 수 있습니다.

**예제**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  InnerWrap: ({ children }) => {
    const routerState = useRouterState()

    return (
      <SomeProvider>
        {children}
      </SomeProvider>
    )
  },
})
```


### `notFoundMode` 속성

- 타입: `'root' | 'fuzzy'`
- 선택 사항
- 기본값: `'fuzzy'`
- 이 속성은 TanStack Router가 현재 위치와 일치하는 라우트를 찾지 못했을 때의 동작 방식을 제어합니다. 자세한 내용은 [Not Found Errors 가이드](../../guide/not-found-errors.md)를 참고하세요.


### `notFoundRoute` 속성

- **더 이상 사용되지 않음**
- 타입: `NotFoundRoute`
- 선택 사항
- 라우트 트리의 모든 브랜치에 대한 기본 404 라우트로 사용됩니다. 이 설정은 각 브랜치의 루트 라우트에서 `NotFoundRoute` 옵션을 통해 개별적으로 재정의할 수 있습니다.


### `errorSerializer` 속성

- 타입: [`RouterErrorSerializer`]
- 선택 사항
- 서버와 클라이언트 간에 오류를 어떻게 직렬화(serialize)하고 역직렬화(deserialize)할지 결정하는 직렬화 객체입니다.


#### `errorSerializer.serialize` 메서드

- 타입: `(err: unknown) => TSerializedError`
- 이 메서드는 라우터의 **dehydrated 상태**에 저장될 때, 에러가 어떻게 직렬화되는지를 정의하기 위해 호출됩니다.


#### `errorSerializer.deserialize` 메서드

- 타입: `(err: TSerializedError) => unknown`
- 이 메서드는 라우터의 **디하이드레이션(dehydration)** 상태에서 오류를 어떻게 역직렬화할지 정의하는 데 사용됩니다.


### `transformer` 속성

- 타입: `RouterTransformer`
- 선택 사항
- SSR(서버 사이드 렌더링) 중 서버와 클라이언트 간 데이터를 전송할 때 사용할 변환기입니다.
- 기본값은 몇 가지 기본 타입을 지원하는 매우 가벼운 변환기입니다. 자세한 내용은 [SSR 가이드](../../guide/ssr.md)를 참고하세요.


#### `transformer.stringify` 메서드

- 타입: `(obj: unknown) => string`
- 이 메서드는 클라이언트로 전송할 데이터를 문자열로 변환할 때 호출됩니다.


#### `transformer.parse` 메서드

- 타입: `(str: string) => unknown`
- 이 메서드는 서버에서 인코딩된 문자열을 파싱할 때 호출됩니다.


### `trailingSlash` 속성

- 타입: `'always' | 'never' | 'preserve'`
- 선택 사항
- 기본값: `never`
- URL 끝에 있는 슬래시(/)를 어떻게 처리할지 설정합니다.  
  - `'always'`: 슬래시가 없으면 추가합니다.  
  - `'never'`: 슬래시가 있으면 제거합니다.  
  - `'preserve'`: 슬래시를 그대로 유지합니다.


### `pathParamsAllowedCharacters` 속성

- 타입: `Array<';' | ':' | '@' | '&' | '=' | '+' | '$' | ','>`
- 선택 사항
- `encodeURIComponent` 함수에 의해 일반적으로 이스케이프되는 URI 문자 중 경로 파라미터에서 허용할 문자를 설정합니다.


### `defaultStructuralSharing` 속성

- **타입**: `boolean`
- **선택 사항**
- **기본값**: `false`
- **설명**: 세밀한 선택자에 대해 기본적으로 구조적 공유(structural sharing)를 활성화할지 여부를 설정합니다.
- **참고**: 더 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 확인하세요.


