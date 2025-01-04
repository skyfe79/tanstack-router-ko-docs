# Table of Contents

- [Navigation](#navigation)
  - [모든 것은 상대적이다](#모든-것은-상대적이다)
  - [공유 네비게이션 API](#공유-네비게이션-api)
    - [`ToOptions` 인터페이스](#tooptions-인터페이스)
    - [`NavigateOptions` 인터페이스](#navigateoptions-인터페이스)
    - [`LinkOptions` 인터페이스](#linkoptions-인터페이스)
  - [네비게이션 API](#네비게이션-api)
  - [`<Link>` 컴포넌트](#link-컴포넌트)
    - [절대 경로 링크](#절대-경로-링크)
    - [동적 링크](#동적-링크)
    - [상대 링크](#상대-링크)
    - [검색 파라미터 링크](#검색-파라미터-링크)
    - [검색 파라미터 타입 안전성](#검색-파라미터-타입-안전성)
    - [해시 링크](#해시-링크)
    - [활성 및 비활성 Props](#활성-및-비활성-props)
    - [`data-status` 속성](#data-status-속성)
    - [활성 옵션](#활성-옵션)
    - [`isActive`를 자식 컴포넌트에 전달하기](#isactive를-자식-컴포넌트에-전달하기)
    - [링크 미리 가져오기(Link Preloading)](#링크-미리-가져오기link-preloading)
    - [링크 미리 가져오기 타임아웃](#링크-미리-가져오기-타임아웃)
  - [`useNavigate`](#usenavigate)
    - [`navigate` 옵션](#navigate-옵션)
  - [`Navigate` 컴포넌트](#navigate-컴포넌트)
  - [`router.navigate`](#routernavigate)
  - [`useMatchRoute`와 `<MatchRoute>`](#usematchroute와-matchroute)

# Navigation





## 모든 것은 상대적이다

믿기 어려울지 모르지만, 앱 내의 모든 네비게이션은 **상대적**입니다. 명시적인 상대 경로 구문(`../../somewhere`)을 사용하지 않더라도 마찬가지입니다. 링크를 클릭하거나 명령형 네비게이션 호출을 할 때마다 항상 **출발지** 경로와 **목적지** 경로가 존재합니다. 이는 여러분이 한 라우트에서 다른 라우트로 이동한다는 의미입니다.

TanStack Router는 모든 네비게이션에서 이 상대적 네비게이션 개념을 염두에 두고 설계되었습니다. 따라서 API에서 두 가지 속성을 자주 보게 될 것입니다:

- `from` - 출발지 라우트 ID
- `to` - 목적지 라우트 ID

> ⚠️ `from` 라우트 ID가 제공되지 않으면, 라우터는 루트 `/` 라우트에서 네비게이션을 시작한다고 가정하고 절대 경로만 자동 완성합니다. 결국, 어디로 가려면 현재 위치를 알아야 하니까요 😉.


## 공유 네비게이션 API

TanStack Router의 모든 네비게이션과 라우트 매칭 API는 동일한 코어 인터페이스를 사용합니다. 각 API마다 약간의 차이는 있지만, 한 번 배우면 라이브러리 전체에서 동일한 문법과 개념을 활용할 수 있습니다.


### `ToOptions` 인터페이스

이 인터페이스는 모든 네비게이션 및 라우트 매칭 API에서 사용되는 핵심 `ToOptions`입니다.

```ts
type ToOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths | string = string,
  TTo extends string = '',
> = {
  // `from`은 선택적인 라우트 ID 또는 경로입니다. 이 값을 제공하지 않으면 절대 경로만 자동 완성 및 타입 안전성이 보장됩니다. 일반적으로 현재 렌더링 중인 라우트의 `route.fullPath`를 편의상 제공합니다. 출발 라우트를 모르는 경우, 이 값을 비워두고 절대 경로나 안전하지 않은 상대 경로를 사용하세요.
  from: string
  // `to`는 절대 라우트 경로 또는 `from` 옵션에서 유효한 라우트 경로까지의 상대 경로가 될 수 있습니다. ⚠️ `to` 옵션에 경로 파라미터, 해시 또는 검색 파라미터를 직접 삽입하지 마세요. 대신 `params`, `search`, `hash` 옵션을 사용하세요.
  to: string
  // `params`는 `to` 옵션에 삽입할 경로 파라미터 객체이거나, 이전 파라미터를 받아 새로운 파라미터를 반환하는 함수입니다. 이는 최종 URL에 동적 파라미터를 삽입하는 유일한 방법입니다. `from`과 `to` 라우트에 따라 경로 파라미터를 전부, 일부 또는 전혀 제공하지 않아도 됩니다. 필요한 파라미터가 있다면 TypeScript가 알려줍니다.
  params:
    | Record
    | ((prevParams: Record) => Record)
  // `search`는 쿼리 파라미터 객체이거나, 이전 검색 파라미터를 받아 새로운 검색 파라미터를 반환하는 함수입니다. `from`과 `to` 라우트에 따라 쿼리 파라미터를 전부, 일부 또는 전혀 제공하지 않아도 됩니다. 필요한 검색 파라미터가 있다면 TypeScript가 알려줍니다.
  search:
    | Record
    | ((prevSearch: Record) => Record)
  // `hash`는 문자열이거나, 이전 해시를 받아 새로운 해시를 반환하는 함수입니다.
  hash?: string | ((prevHash: string) => string)
  // `state`는 상태 객체이거나, 이전 상태를 받아 새로운 상태를 반환하는 함수입니다. 상태는 history API에 저장되며, URL 검색 파라미터에 영구적으로 저장하지 않고 라우트 간 데이터를 전달하는 데 유용합니다.
  state?:
    | Record
    | ((prevState: Record) => Record)
}
```

> 🧠 모든 라우트 객체는 `to` 속성을 가지고 있으며, 이는 네비게이션 또는 라우트 매칭 API에서 `to`로 사용될 수 있습니다. 가능한 경우, 이 기능을 사용하면 일반 문자열 대신 타입 안전한 라우트 참조를 사용할 수 있습니다.

```tsx
import { Route as aboutRoute } from './routes/about.tsx'

function Comp() {
  return About
}
```


### `NavigateOptions` 인터페이스

이것은 `ToOptions`를 확장한 핵심 `NavigateOptions` 인터페이스입니다. 실제로 네비게이션을 수행하는 모든 API는 이 인터페이스를 사용합니다:

```ts
export type NavigateOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths | string = string,
  TTo extends string = '',
> = ToOptions & {
  // `replace`는 네비게이션이 현재 히스토리 항목을 대체할지, 아니면 새로운 항목을 추가할지를 결정하는 불리언 값입니다.
  replace?: boolean
}
```


### `LinkOptions` 인터페이스

실제 `<a>` 태그가 사용되는 곳에서는 `NavigateOptions`를 확장한 `LinkOptions` 인터페이스를 사용할 수 있습니다:

```tsx
export type LinkOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths | string = string,
  TTo extends string = '',
> = NavigateOptions & {
  // 표준 앵커 태그의 target 속성
  target?: HTMLAnchorElement['target']
  // 기본값은 `{ exact: false, includeHash: false }`
  activeOptions?: {
    exact?: boolean
    includeHash?: boolean
    includeSearch?: boolean
    explicitUndefined?: boolean
  }
  // 설정하면, 링크된 라우트를 호버 시 미리 로드하고, 사용자가 해당 라우트로 이동할 가능성을 고려하여 지정된 시간(밀리초) 동안 캐시합니다.
  preload?: false | 'intent'
  // intent 미리 로드를 지정된 시간(밀리초)만큼 지연시킵니다. 이 지연 시간 전에 intent가 종료되면 미리 로드가 취소됩니다.
  preloadDelay?: number
  // true로 설정하면, href 속성 없이 링크를 렌더링합니다.
  disabled?: boolean
}
```


## 네비게이션 API

상대적 네비게이션과 다양한 인터페이스를 고려했으니, 이제 여러분이 사용할 수 있는 다양한 네비게이션 API에 대해 알아보겠습니다.

- `<Link>` 컴포넌트
  - 유효한 `href`를 가진 실제 `<a>` 태그를 생성합니다. 클릭하거나 cmd/ctrl + 클릭으로 새 탭에서 열 수 있습니다.
- `useNavigate()` 훅
  - 가능한 경우 네비게이션에는 `<Link>` 컴포넌트를 사용해야 하지만, 때로는 사이드 이펙트의 결과로 명령적으로 네비게이션을 해야 할 때가 있습니다. `useNavigate`는 즉각적인 클라이언트 측 네비게이션을 수행할 수 있는 함수를 반환합니다.
- `<Navigate>` 컴포넌트
  - 아무것도 렌더링하지 않고 즉각적인 클라이언트 측 네비게이션을 수행합니다.
- `Router.navigate()` 메서드
  - 이는 TanStack Router에서 가장 강력한 네비게이션 API입니다. `useNavigate`와 유사하게 명령적으로 네비게이션을 수행하지만, 라우터에 접근할 수 있는 모든 곳에서 사용할 수 있습니다.

⚠️ 이 API들은 서버 측 리다이렉트를 대체할 수 없습니다. 애플리케이션을 마운트하기 전에 사용자를 한 라우트에서 다른 라우트로 즉시 리다이렉트해야 한다면, 클라이언트 측 네비게이션 대신 서버 측 리다이렉트를 사용하세요.


## `<Link>` 컴포넌트

`<Link>` 컴포넌트는 앱 내에서 네비게이션을 할 때 가장 일반적으로 사용되는 방법입니다. 이 컴포넌트는 실제 `<a>` 태그를 렌더링하며, 유효한 `href` 속성을 가지고 있어 클릭하거나 cmd/ctrl + 클릭으로 새 탭에서 열 수 있습니다. 또한 `target`과 같은 일반적인 `<a>` 태그의 속성도 지원하여 새 창에서 링크를 열 수 있습니다.

[`LinkOptions`](#linkoptions-interface) 인터페이스 외에도, `<Link>` 컴포넌트는 다음과 같은 props를 지원합니다:

```tsx
export type LinkProps<
  TFrom extends RoutePaths | string = string,
  TTo extends string = '',
> = LinkOptions & {
  // 이 링크의 `active` 상태에 대한 추가 props를 반환하는 함수입니다. 이 props는 링크에 전달된 다른 props를 덮어씁니다 (`style`은 병합되고, `className`은 연결됨).
  activeProps?:
    | React.AnchorHTMLAttributes
    | (() => React.AnchorHTMLAttributes)
  // 이 링크의 `inactive` 상태에 대한 추가 props를 반환하는 함수입니다. 이 props는 링크에 전달된 다른 props를 덮어씁니다 (`style`은 병합되고, `className`은 연결됨).
  inactiveProps?:
    | React.AnchorHTMLAttributes
    | (() => React.AnchorHTMLAttributes)
}
```


### 절대 경로 링크

간단한 정적 링크를 만들어 보겠습니다!

```tsx
import { Link } from '@tanstack/react-router'

const link = About
```


### 동적 링크

동적 링크는 동적 세그먼트를 포함하는 링크입니다. 예를 들어, 블로그 게시물로 연결되는 링크는 다음과 같이 작성할 수 있습니다.

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
  >
    블로그 게시물
  </Link>
)
```

일반적으로 동적 세그먼트의 파라미터는 `string` 타입의 값이지만, 라우트 옵션에서 파싱한 다른 타입으로도 사용할 수 있습니다. 어느 쪽이든, 컴파일 시점에 올바른 타입을 전달했는지 확인하기 위해 타입 검사가 이루어집니다.


### 상대 링크

기본적으로 모든 링크는 `from` 라우트 경로가 제공되지 않는 한 절대 경로로 처리됩니다. 이는 위의 링크가 현재 어떤 라우트에 있든 항상 `/about` 라우트로 이동한다는 의미입니다.

현재 라우트를 기준으로 상대 링크를 만들고 싶다면, `from` 라우트 경로를 제공할 수 있습니다:

```tsx
const postIdRoute = createRoute({
  path: '/blog/post/$postId',
})

const link = (
  
    Categories
  
)
```

위에서 볼 수 있듯이, `route.fullPath`를 `from` 라우트 경로로 제공하는 것이 일반적입니다. 이는 `route.fullPath`가 애플리케이션을 리팩토링할 때 업데이트되는 참조이기 때문입니다. 그러나 라우트를 직접 가져올 수 없는 경우도 있는데, 이때는 라우트 경로를 문자열로 직접 제공해도 괜찮습니다. 여전히 일반적인 방식으로 타입 검사가 이루어집니다!


### 검색 파라미터 링크

검색 파라미터는 라우트에 추가적인 컨텍스트를 제공하는 좋은 방법입니다. 예를 들어, 검색 페이지에 검색 쿼리를 제공하고 싶을 수 있습니다:

```tsx
const link = (
  <Link
    to="/search"
    search={{
      query: 'tanstack',
    }}
  >
    검색
  </Link>
)
```

또한, 기존 라우트의 다른 정보를 제공하지 않고 단일 검색 파라미터만 업데이트하고 싶을 때도 있습니다. 예를 들어, 검색 결과의 페이지 번호를 업데이트하고 싶을 수 있습니다:

```tsx
const link = (
  <Link
    to="."
    search={(prev) => ({
      ...prev,
      page: prev.page + 1,
    })}
  >
    다음 페이지
  </Link>
)
```


### 검색 파라미터 타입 안전성

검색 파라미터는 매우 동적인 상태 관리 메커니즘이기 때문에, 올바른 타입을 전달하는 것이 중요합니다. 나중 섹션에서 검색 파라미터의 타입 안전성을 보장하고 검증하는 방법을 비롯한 다양한 기능을 자세히 살펴볼 예정입니다!


### 해시 링크

해시 링크는 페이지의 특정 섹션으로 연결하는 유용한 방법입니다. 예를 들어, 블로그 포스트의 특정 섹션으로 링크를 걸고 싶을 때 사용할 수 있습니다:

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    hash="section-1"
  >
    Section 1
  </Link>
);
```

위 코드에서 `hash="section-1"`은 페이지 내의 `section-1`이라는 ID를 가진 요소로 이동하는 링크를 생성합니다. 이렇게 하면 독자가 페이지의 특정 부분으로 바로 이동할 수 있습니다.


### 활성 및 비활성 Props

`Link` 컴포넌트는 `activeProps`와 `inactiveProps`라는 두 가지 추가 props를 지원합니다. 이 props는 링크의 **활성(active)** 및 **비활성(inactive)** 상태에 대한 추가 props를 반환하는 함수입니다. 여기에 전달된 스타일과 클래스를 제외한 모든 props는 `Link`에 전달된 원래 props를 덮어씁니다. 전달된 스타일이나 클래스는 병합됩니다.

예제를 살펴보겠습니다:

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    activeProps={{
      style: {
        fontWeight: 'bold', // 활성 상태에서 글꼴 두께를 굵게 설정
      },
    }}
  >
    Section 1
  </Link>
);
```

이 예제에서 `activeProps`는 링크가 활성 상태일 때 글꼴 두께를 굵게 설정합니다.


### `data-status` 속성

`Link` 컴포넌트는 `activeProps`와 `inactiveProps` 속성 외에도, 링크가 활성 상태일 때 렌더링된 엘리먼트에 `data-status` 속성을 추가합니다. 이 속성은 링크의 현재 상태에 따라 `active` 또는 `undefined` 값을 가집니다. 이 기능은 속성 대신 데이터 속성을 사용해 링크를 스타일링하고 싶을 때 유용하게 활용할 수 있습니다.


### 활성 옵션

`Link` 컴포넌트는 링크가 활성 상태인지 여부를 결정하는 몇 가지 옵션을 제공하는 `activeOptions` 속성을 가지고 있습니다. 다음 인터페이스는 이러한 옵션을 설명합니다:

```tsx
export interface ActiveOptions {
  // true로 설정하면, 현재 라우트가 `to` 라우트 경로와 정확히 일치할 때만 링크가 활성화됩니다 (자식 라우트 제외)
  // 기본값은 `false`
  exact?: boolean
  // true로 설정하면, 현재 URL의 해시가 `hash` prop과 일치할 때만 링크가 활성화됩니다
  // 기본값은 `false`
  includeHash?: boolean // 기본값은 false
  // true로 설정하면, 현재 URL의 검색 파라미터가 `search` prop을 포함적으로 일치할 때만 링크가 활성화됩니다
  // 기본값은 `true`
  includeSearch?: boolean
  // 이 옵션은 `includeSearch` 동작을 수정합니다.
  // true로 설정하면, `search`에서 명시적으로 `undefined`로 설정된 속성은 현재 URL 검색 파라미터에 존재하지 않아야 링크가 활성화됩니다.
  // 기본값은 `false`
  explicitUndefined?: boolean
}
```

기본적으로, 이 옵션은 결과 **pathname**이 현재 라우트의 접두사인지 확인합니다. 검색 파라미터가 제공된 경우, 현재 위치의 검색 파라미터와 **포함적으로** 일치하는지 확인합니다. 해시는 기본적으로 확인하지 않습니다.

예를 들어, `/blog/post/my-first-blog-post` 라우트에 있는 경우, 다음 링크들은 활성화됩니다:

```tsx
const link1 = (
  
    Blog Post
  
)
const link2 = Blog Post
const link3 = Blog Post
```

그러나 다음 링크는 활성화되지 않습니다:

```tsx
const link4 = (
  
    Blog Post
  
)
```

일부 링크는 정확히 일치할 때만 활성화되도록 하는 것이 일반적입니다. 홈페이지로 연결되는 링크가 좋은 예입니다. 이러한 경우에는 `exact: true` 옵션을 전달할 수 있습니다:

```tsx
const link = (
  
    Home
  
)
```

이렇게 하면 자식 라우트에 있을 때 링크가 활성화되지 않도록 보장됩니다.

추가로 알아두어야 할 몇 가지 옵션:

- 해시를 매칭에 포함시키고 싶다면 `includeHash: true` 옵션을 전달할 수 있습니다.
- 검색 파라미터를 매칭에 포함시키지 않으려면 `includeSearch: false` 옵션을 전달할 수 있습니다.


### `isActive`를 자식 컴포넌트에 전달하기

`Link` 컴포넌트는 자식 요소로 함수를 받을 수 있습니다. 이를 통해 `isActive` 속성을 자식 컴포넌트에 전달할 수 있습니다. 예를 들어, 부모 링크가 활성 상태인지에 따라 자식 컴포넌트의 스타일을 조정할 수 있습니다:

```tsx
const link = (
  <Link>
    {({ isActive }) => {
      return (
        <>
          My Blog Post
          {isActive && <span> (현재 페이지)</span>}
        </>
      )
    }}
  </Link>
)
```

이 예제에서는 `isActive`가 `true`일 때만 " (현재 페이지)" 텍스트가 표시됩니다. 이를 통해 사용자가 현재 어떤 페이지에 있는지 시각적으로 알려줄 수 있습니다.


### 링크 미리 가져오기(Link Preloading)

`Link` 컴포넌트는 의도된 동작(현재는 호버링 또는 터치 시작)에 따라 라우트를 자동으로 미리 가져오는 기능을 지원합니다. 이 설정은 라우터 옵션에서 기본값으로 구성하거나, `Link` 컴포넌트에 `preload='intent'` 프로퍼티를 전달하여 설정할 수 있습니다. 아래는 예제입니다:

```tsx
const link = (
  <Link to="/blog-post" preload="intent">
    Blog Post
  </Link>
);
```

미리 가져오기가 활성화되고 비동기 라우트 의존성이 상대적으로 빠르게 처리된다면, 이 간단한 트릭으로 애플리케이션의 체감 성능을 크게 향상시킬 수 있습니다.

더 나은 점은 `@tanstack/query`와 같은 캐시 우선 라이브러리를 사용할 경우, 미리 가져온 라우트가 유지되며 사용자가 나중에 해당 라우트로 이동할 때 stale-while-revalidate 경험을 제공할 준비가 되어 있다는 것입니다.


### 링크 미리 가져오기 타임아웃

미리 가져오기 기능과 함께, 사용자가 링크 위에 얼마나 오래 머물러야 미리 가져오기가 시작될지 결정하는 **타임아웃 설정**이 가능합니다. 기본값은 50밀리초이지만, `Link` 컴포넌트에 `preloadTimeout` 속성을 전달하여 원하는 시간(밀리초 단위)으로 변경할 수 있습니다.

```tsx
const link = (
  <Link to="/blog-post" preloadTimeout={100}>
    Blog Post
  </Link>
);
```


## `useNavigate`

> ⚠️ `Link` 컴포넌트는 `href`, cmd/ctrl + 클릭 기능, 활성/비활성 상태 처리 등 내장된 기능을 제공합니다. 따라서 사용자가 상호작용할 수 있는 요소(예: 링크, 버튼)에는 `useNavigate` 대신 `Link` 컴포넌트를 사용하는 것이 권장됩니다. 하지만 사이드 이펙트로 인한 네비게이션(예: 비동기 작업 성공 후 페이지 이동)을 처리할 때는 `useNavigate`가 필요할 수 있습니다.

`useNavigate` 훅은 명령적으로 네비게이션을 수행할 수 있는 `navigate` 함수를 반환합니다. 이는 사이드 이펙트(예: 비동기 작업 성공 후)에서 라우트로 이동할 때 유용합니다. 다음은 예제입니다:

```tsx
function Component() {
  const navigate = useNavigate({ from: '/posts/$postId' })

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()

    const response = await fetch('/posts', {
      method: 'POST',
      body: JSON.stringify({ title: 'My First Post' }),
    })

    const { id: postId } = await response.json()

    if (response.ok) {
      navigate({ to: '/posts/$postId', params: { postId } })
    }
  }
}
```

> 🧠 위 예제에서 볼 수 있듯이, `from` 옵션을 사용해 훅 호출 시 시작 라우트를 지정할 수 있습니다. 이 옵션은 `navigate` 함수를 호출할 때마다 전달할 수도 있지만, 여기서 한 번 지정하면 오류 가능성을 줄이고 타이핑을 줄일 수 있어 권장됩니다!


### `navigate` 옵션

`useNavigate`가 반환하는 `navigate` 함수는 [`NavigateOptions` 인터페이스](#navigateoptions-interface)를 인자로 받습니다.


## `Navigate` 컴포넌트

가끔 컴포넌트가 마운트될 때 즉시 네비게이션을 해야 하는 경우가 있습니다. 처음에는 `useNavigate`와 즉각적인 사이드 이펙트(예: React.useEffect)를 사용하려고 할 수 있지만, 이는 불필요합니다. 대신 `Navigate` 컴포넌트를 렌더링하여 동일한 결과를 얻을 수 있습니다.

```tsx
function Component() {
  return <Navigate to="/target-route" replace />;
}
```

`Navigate` 컴포넌트는 컴포넌트가 마운트될 때 즉시 특정 라우트로 이동하는 방법으로 생각하면 됩니다. 클라이언트 전용 리다이렉트를 처리하는 데 매우 유용합니다. 하지만 이는 서버에서 서버 인식 리다이렉트를 책임감 있게 처리하는 대체 수단이 **절대 아님**을 명심하세요.


## `router.navigate`

`router.navigate` 메서드는 `useNavigate`가 반환하는 `navigate` 함수와 동일하며, 동일한 [`NavigateOptions` 인터페이스](#navigateoptions-interface)를 인자로 받습니다. `useNavigate` 훅과 달리, 이 메서드는 `router` 인스턴스가 사용 가능한 모든 곳에서 사용할 수 있습니다. 따라서 프레임워크 외부를 포함해 애플리케이션의 어디서든 명령형으로 네비게이션을 수행할 때 유용합니다.


## `useMatchRoute`와 `<MatchRoute>`

`useMatchRoute` 훅과 `<MatchRoute>` 컴포넌트는 동일한 기능을 제공하지만, 훅이 조금 더 유연합니다. 둘 다 표준 네비게이션 `ToOptions` 인터페이스를 옵션 또는 props로 받아들이고, 해당 라우트가 현재 매치되면 `true/false`를 반환합니다. 또한 라우트가 현재 대기 중인 경우(예: 라우트가 해당 라우트로 전환 중인 경우) `true`를 반환하는 편리한 `pending` 옵션도 있습니다. 이는 사용자가 네비게이션 중인 위치 주변에 낙관적 UI를 표시하는 데 매우 유용할 수 있습니다:

```tsx
function Component() {
  return (
    <MatchRoute to="/users" pending>
      {({ match, pending }) => (
        <div>
          Users
          {pending && <span>Loading...</span>}
        </div>
      )}
    </MatchRoute>
  )
}
```

컴포넌트 버전인 `<MatchRoute>`는 라우트가 매치될 때 무언가를 렌더링하기 위해 자식으로 함수를 사용할 수도 있습니다:

```tsx
function Component() {
  return (
    <MatchRoute to="/users">
      {(match) => {
        return match ? <div>Users</div> : null
      }}
    </MatchRoute>
  )
}
```

훅 버전인 `useMatchRoute`는 라우트가 매치되었는지 프로그래밍 방식으로 확인할 수 있는 함수를 반환합니다:

```tsx
function Component() {
  const matchRoute = useMatchRoute()

  useEffect(() => {
    if (matchRoute({ to: '/users', pending: true })) {
      console.info('/users 라우트가 매치되었고 대기 중입니다')
    }
  })

  return <div>Users</div>
}
```

---

휴! 정말 많은 네비게이션을 다뤘네요! 이제 여러분의 애플리케이션을 자유롭게 탐색할 준비가 되셨길 바랍니다. 다음으로 넘어가 봅시다!


