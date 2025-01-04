# 코드 분할(Code Splitting)

코드 분할과 지연 로딩은 애플리케이션의 번들 크기와 로드 성능을 개선하는 강력한 기술입니다.

- 초기 페이지 로드 시 필요한 코드 양을 줄입니다.
- 코드는 필요할 때만 요청에 따라 로드됩니다.
- 더 작은 크기의 청크(chunk)로 분할되어 브라우저에서 쉽게 캐싱할 수 있습니다.


## TanStack Router의 코드 분할 방식

TanStack Router는 코드를 두 가지 범주로 나눕니다:

- **중요 라우트 설정(Critical Route Configuration)** - 현재 라우트를 렌더링하고 데이터 로딩 프로세스를 가능한 한 빨리 시작하는 데 필요한 코드입니다.

  - 경로 파싱/직렬화
  - 검색 파라미터 유효성 검사
  - 로더, Before Load
  - 라우트 컨텍스트
  - 정적 데이터
  - 링크
  - 스크립트
  - 스타일
  - 아래에 나열되지 않은 기타 라우트 설정

- **비중요/지연 로딩 라우트 설정(Non-Critical/Lazy Route Configuration)** - 라우트 매칭에 필요하지 않고, 필요할 때 로드할 수 있는 코드입니다.
  - 라우트 컴포넌트
  - 에러 컴포넌트
  - Pending 컴포넌트
  - Not-found 컴포넌트

> 🧠 **로더는 왜 분할되지 않나요?**
>
> - 로더는 이미 비동기 경계이므로, 청크를 가져오는 비용과 로더 실행을 기다리는 비용을 두 번 지불해야 합니다.
> - 일반적으로 로더는 컴포넌트보다 번들 크기에 큰 영향을 미치지 않습니다.
> - 로더는 라우트에서 가장 중요한 미리 로드 가능한 리소스 중 하나입니다. 특히 링크 위에 마우스를 올리는 것과 같은 기본 미리 로드 의도를 사용할 때, 로더는 추가적인 비동기 오버헤드 없이 사용 가능해야 합니다.
>
>   로더 분할의 단점을 알고 있음에도 불구하고, 여전히 분할을 원한다면 [데이터 로더 분할](#data-loader-splitting) 섹션을 참고하세요.


## 코드 분할 접근 방식

TanStack Router는 여러 가지 코드 분할 방식을 지원합니다. 코드 기반 라우팅을 사용 중이라면 [코드 기반 분할](#code-based-splitting) 섹션으로 넘어가세요.

파일 기반 라우팅을 사용할 경우, 다음과 같은 코드 분할 방식을 활용할 수 있습니다:

- [`.lazy.tsx` 접미사 사용](#using-the-lazytsx-suffix)
- [가상 라우트 사용](#using-virtual-routes)
- [자동 코드 분할 사용 ✨](#using-automatic-code-splitting)


## 라우트 파일을 디렉토리로 캡슐화하기

TanStack Router의 파일 기반 라우팅 시스템은 플랫(flat)과 중첩(nested) 파일 구조를 모두 지원하도록 설계되었습니다. 따라서 추가 설정 없이도 라우트 파일을 단일 디렉토리로 캡슐화할 수 있습니다.

라우트 파일을 디렉토리로 캡슐화하려면, 라우트 파일을 동일한 이름의 디렉토리 안에 `.route` 파일로 이동시키면 됩니다.

예를 들어, `posts.tsx`라는 라우트 파일이 있다면, `posts`라는 새 디렉토리를 만들고 `posts.tsx` 파일을 그 안으로 이동시킨 후, 파일 이름을 `route.tsx`로 변경합니다.

**변경 전**

- `posts.tsx`
- `posts.lazy.tsx`

**변경 후**

- `posts`
  - `route.tsx`
  - `route.lazy.tsx`


## `.lazy.tsx` 접미사 사용하기

[파일 기반 라우팅](./route-trees.md) 방식을 사용한다면, 코드 분할은 **코드를 `.lazy.tsx` 접미사가 붙은 별도의 파일로 옮기고 `FileRoute` 클래스나 `createFileRoute` 함수 대신 `createLazyFileRoute` 함수를 사용하는 것만큼 간단**합니다.

현재 `createLazyFileRoute` 함수에서 지원하는 옵션은 다음과 같습니다:

| 내보내기 이름         | 설명                                                                 |
| ------------------- | --------------------------------------------------------------------- |
| `component`         | 라우트에 렌더링할 컴포넌트.                                           |
| `errorComponent`    | 라우트 로딩 중 오류가 발생했을 때 렌더링할 컴포넌트.                  |
| `pendingComponent`  | 라우트가 로딩 중일 때 렌더링할 컴포넌트.                              |
| `notFoundComponent` | 찾을 수 없는 오류가 발생했을 때 렌더링할 컴포넌트.                    |


### `.lazy.tsx` 규칙의 예외 사항

- `__root.tsx` 라우트 파일은 현재 라우트와 상관없이 항상 렌더링되기 때문에 코드 분할을 지원하지 않습니다.


### `.lazy.tsx`를 사용한 코드 분할 예제

`.lazy.tsx`를 사용하면 라우트를 두 개의 파일로 나누어 코드 분할을 활성화할 수 있습니다.

**이전 (단일 파일)**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'
import { fetchPosts } from './api'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
  component: Posts,
})

function Posts() {
  // ...
}
```

**이후 (두 파일로 분할)**

이 파일은 중요한 라우트 설정을 포함합니다:

```tsx
// src/routes/posts.tsx

import { createFileRoute } from '@tanstack/react-router'
import { fetchPosts } from './api'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
})
```

비중요한 라우트 설정은 `.lazy.tsx` 접미사가 붙은 파일로 이동합니다:

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```


## 가상 라우트 사용하기

라우트 파일에서 모든 내용을 분리하다 보면 파일이 비어 있는 상황이 발생할 수 있습니다. 이 경우, **라우트 파일을 완전히 삭제**하면 됩니다! 그러면 코드 분할 파일을 위한 앵커 역할을 하는 가상 라우트가 자동으로 생성됩니다. 이 가상 라우트는 생성된 라우트 트리 파일에 직접 위치하게 됩니다.

**이전 (가상 라우트)**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // 아무 내용 없음
})
```

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

**이후 (가상 라우트)**

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

짜잔! 🎉


## 자동 코드 분할 사용하기

`autoCodeSplitting` 기능을 사용하면, TanStack Router는 위에서 언급한 비중요 라우트 설정을 기반으로 라우트 파일을 자동으로 코드 분할합니다.

> [!IMPORTANT]
> 자동 코드 분할 기능은 [지원되는 번들러](./file-based-routing.md#installation) 중 하나를 사용하여 파일 기반 라우팅을 사용할 때만 사용할 수 있습니다.
> CLI(`@tanstack/router-cli`)만 사용하는 경우에는 이 기능이 **작동하지 않습니다**.

TanStack Router를 처음 사용하는 경우, 다음 단계를 건너뛰고 [자동 코드 분할 활성화](#enabling-automatic-code-splitting)로 이동할 수 있습니다. 이미 TanStack Router를 사용 중이라면, 자동 코드 분할을 활성화하기 위해 라우트 파일을 일부 수정해야 할 수 있습니다.


### 자동 코드 분할을 위한 라우트 파일 준비

자동 코드 분할을 사용할 때는 `.lazy.tsx` 접미사를 사용하지 않아야 합니다. 대신, `.lazy.tsx` 접미사로 분할된 라우트 파일은 하나의 파일로 다시 합쳐야 합니다.

**`.lazy` 접미사 사용 전**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
})
```

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  component: Posts,
})

function Posts() {
  // ...
}
```

**`.lazy` 접미사 제거 후**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  loader: fetchPosts,
  component: Posts,
})

function Posts() {
  // ...
}
```

또한, 가상 라우트를 사용하고 있었다면 해당 `.lazy.tsx` 접미사도 제거해야 합니다.

**가상 라우트 사용 전**

```tsx
// src/routes/posts.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts')({
  /* ... */
})
```

**가상 라우트 제거 후**

```tsx
// src/routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  /* ... */
})
```

이 작업을 마치면 라우트 파일이 자동 코드 분할을 사용할 준비가 됩니다.


### 자동 코드 분할 활성화

> [!IMPORTANT]
> 현재 자동 코드 분할을 사용할 경우, **모든 라우트 파일**이 코드 분할됩니다. 따라서 모든 라우트 파일을 코드 분할하고 싶은 경우에만 이 기능을 사용해야 합니다.

자동 코드 분할을 활성화하려면 `tsr.config.json` 파일에 다음을 추가하세요:

```jsonc
{
  // ...
  "autoCodeSplitting": true,
}
```

이제 TanStack Router가 모든 라우트 파일을 중요(critical) 및 비중요(non-critical) 라우트 설정에 따라 자동으로 코드 분할합니다.


## Code-Based Splitting





### `route.lazy()`와 `createLazyRoute`를 사용한 수동 코드 분할

파일 기반 라우팅 시스템을 사용하지 않더라도, `route.lazy()` 메서드와 `createLazyRoute` 함수를 사용해 코드를 수동으로 분할할 수 있습니다. 이를 위해 다음 단계를 따르면 됩니다.

먼저, `createLazyRoute` 함수를 사용해 지연 로딩(lazy loading) 라우트를 생성합니다.

```tsx
// src/posts.tsx
export const Route = createLazyRoute('/posts')({
  component: MyComponent,
})

function MyComponent() {
  return My Component
}
```

그런 다음, `app.tsx` 파일에서 라우트 정의에 `.lazy` 메서드를 호출하여, 비중요한 라우트 설정과 함께 지연 로딩/코드 분할된 라우트를 가져옵니다.

```tsx
// src/app.tsx
const postsRoute = createRoute({
  getParent: () => rootRoute,
  path: '/posts',
}).lazy(() => import('./posts.lazy').then((d) => d.Route))
```

이 방법을 사용하면, 필요한 경우에만 코드를 로드할 수 있어 애플리케이션의 초기 로딩 속도를 개선할 수 있습니다.


## 데이터 로더 분할

> ⚠️ 데이터 로더를 분할하면 서버에 두 번 왕복해야 합니다. 첫 번째는 로더 코드 번들을 불러오기 위한 것이고, 두 번째는 로더 코드를 실행하고 데이터를 가져오기 위한 것입니다. 로더가 번들 크기에 충분히 영향을 미치는지 확실하지 않다면 이 작업을 진행하지 마세요.

라우트의 `loader` 옵션을 사용해 데이터 로딩 로직을 코드 분할할 수 있습니다. 이 과정은 로더에 전달되는 인자의 타입 안전성을 유지하기 어렵게 만들지만, 제네릭 `LoaderContext` 타입을 사용해 대부분의 경우 해결할 수 있습니다:

```tsx
import { LoaderContext } from '@tanstack/react-router'

const route = createRoute({
  path: '/my-route',
  component: MyComponent,
  loader: (...args) => import('./loader').then((d) => d.loader(...args)),
})

// 다른 파일에서...
export const loader = async (context: LoaderContext) => {
  // ...
}
```

이 과정이 다소 번거로울 수 있기 때문에, TanStack Router는 `lazyRouteComponent`와 매우 유사한 `lazyFn` 유틸리티를 제공합니다. 이를 사용하면 과정을 단순화할 수 있습니다:

```tsx
import { lazyFn } from '@tanstack/react-router'

const route = createRoute({
  path: '/my-route',
  component: MyComponent,
  loader: lazyFn(() => import('./loader'), 'loader'),
})

// 다른 파일에서...
export const loader = async (context: LoaderContext) => {
  // ...
}
```


## `getRouteApi` 헬퍼를 사용해 다른 파일에서 라우트 API에 접근하기

여러분이 예상했듯이, 컴포넌트 코드를 라우트 파일과 별도의 파일에 작성하면 라우트 자체를 사용하기 어려울 수 있습니다. 이를 해결하기 위해 TanStack Router는 `getRouteApi` 함수를 제공합니다. 이 함수를 사용하면 라우트를 직접 임포트하지 않고도 타입 안전한 API에 접근할 수 있습니다.

- `my-route.tsx`

```tsx
import { createRoute } from '@tanstack/react-router'
import { MyComponent } from './MyComponent'

const route = createRoute({
  path: '/my-route',
  loader: () => ({
    foo: 'bar',
  }),
  component: MyComponent,
})
```

- `MyComponent.tsx`

```tsx
import { getRouteApi } from '@tanstack/react-router'

const route = getRouteApi('/my-route')

export function MyComponent() {
  const loaderData = route.useLoaderData()
  //    ^? { foo: string }

  return ...
}
```

`getRouteApi` 함수는 다음과 같은 타입 안전한 API에 접근할 때 유용합니다:

- `useLoaderData`
- `useLoaderDeps`
- `useMatch`
- `useParams`
- `useRouteContext`
- `useSearch`


