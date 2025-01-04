# 인증된 라우트

웹 애플리케이션에서 인증은 매우 일반적인 요구사항입니다. 이 가이드에서는 TanStack Router를 사용하여 보호된 라우트를 구축하는 방법과, 사용자가 해당 라우트에 접근하려고 할 때 로그인 페이지로 리디렉션하는 방법을 살펴보겠습니다.


## `route.beforeLoad` 옵션

`route.beforeLoad` 옵션은 라우트가 로드되기 전에 호출될 함수를 지정할 수 있게 해줍니다. 이 함수는 `route.loader` 함수와 동일한 인자들을 받습니다. 사용자가 인증되었는지 확인하고, 인증되지 않았다면 로그인 페이지로 리다이렉트하는 데 적합한 위치입니다.

`beforeLoad` 함수는 다음과 같은 순서로 다른 라우트 로딩 함수들과 함께 실행됩니다:

- 라우트 매칭 (상위에서 하위로)
  - `route.params.parse`
  - `route.validateSearch`
- 라우트 로딩 (미리 로딩 포함)
  - **`route.beforeLoad`**
  - `route.onError`
- 라우트 로딩 (병렬)
  - `route.component.preload?`
  - `route.load`

**중요한 점은, 라우트의 `beforeLoad` 함수는 해당 라우트의 모든 자식 라우트의 `beforeLoad` 함수보다 먼저 호출된다는 것입니다.** 이는 라우트와 그 자식 라우트들을 위한 미들웨어 함수 역할을 합니다.

**`beforeLoad` 함수에서 에러를 던지면, 그 자식 라우트들은 로드를 시도하지 않습니다.**


## 리다이렉트

필수는 아니지만, 일부 인증 흐름에서는 로그인 페이지로 리다이렉트해야 할 수 있습니다. 이를 위해 `beforeLoad`에서 `redirect()`를 **throw**할 수 있습니다:

```tsx
// src/routes/_authenticated.tsx
export const Route = createFileRoute('/_authenticated')({
  beforeLoad: async ({ location }) => {
    if (!isAuthenticated()) {
      throw redirect({
        to: '/login',
        search: {
          // 로그인 후 리다이렉트를 위해 현재 위치 사용
          // (`router.state.resolvedLocation`은 실제 현재 위치보다
          // 뒤쳐질 수 있으므로 사용하지 않음)
          redirect: location.href,
        },
      })
    }
  },
})
```

> [!TIP]
> `redirect()` 함수는 `navigate` 함수와 동일한 옵션을 모두 받을 수 있으므로, 새로운 히스토리 항목을 추가하는 대신 현재 항목을 교체하려면 `replace: true`와 같은 옵션을 전달할 수 있습니다.

사용자를 인증한 후에는 일반적으로 사용자가 접근하려고 했던 페이지로 다시 리다이렉트하는 것이 일반적입니다. 이를 위해 원래 리다이렉트에서 추가한 `redirect` 검색 파라미터를 활용할 수 있습니다. 전체 URL을 교체할 것이므로 `router.navigate`보다 `router.history.push`가 더 적합합니다:

```tsx
router.history.push(search.redirect)
```


## 리다이렉트 없는 인증 방식

일부 애플리케이션은 사용자를 로그인 페이지로 리다이렉트하지 않고, 동일한 페이지에 머물게 한 상태에서 로그인 폼을 보여줍니다. 이때 로그인 폼은 메인 콘텐츠를 대체하거나 모달로 표시됩니다. TanStack Router를 사용하면 이 방식을 쉽게 구현할 수 있습니다. 일반적으로 자식 라우트를 렌더링하는 `<Outlet />`을 렌더링하지 않도록 처리하면 됩니다.

```tsx
// src/routes/_authenticated.tsx
export const Route = createFileRoute('/_authenticated')({
  component: () => {
    if (!isAuthenticated()) {
      return 
    }

    return 
  },
})
```

이 방식은 사용자를 동일한 페이지에 유지하면서도 로그인 폼을 렌더링할 수 있습니다. 사용자가 인증되면 `<Outlet />`을 렌더링하여 자식 라우트를 표시할 수 있습니다.


## React context/hooks를 사용한 인증

React context와 hooks를 사용하여 인증 흐름을 구현한다면, `router.context` 옵션을 통해 인증 상태를 TanStack Router에 전달해야 합니다.

> [!중요]
> React hooks는 React 컴포넌트 외부에서 사용할 수 없습니다. React 컴포넌트 외부에서 hook을 사용해야 한다면, `<RouterProvider />`를 감싸는 컴포넌트에서 hook의 반환 값을 추출한 후, 그 값을 TanStack Router에 전달해야 합니다.

`router.context` 옵션에 대한 자세한 내용은 [Router Context](./router-context.md) 섹션에서 다룹니다.

아래는 React context와 hooks를 사용하여 TanStack Router에서 인증된 라우트를 보호하는 예제입니다. 전체 설정은 [Authenticated Routes 예제](../../examples/authenticated-routes)에서 확인할 수 있습니다.

- `src/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

interface MyRouterContext {
  // useAuth hook의 반환 타입 또는 AuthContext의 값
  auth: AuthState
}

export const Route = createRootRouteWithContext()({
  component: () => ,
})
```

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

export const router = createRouter({
  routeTree,
  context: {
    // 초기에는 auth가 undefined
    // React 컴포넌트 내부에서 auth 상태를 전달할 예정
    auth: undefined!,
  },
})
```

- `src/App.tsx`

```tsx
import { RouterProvider } from '@tanstack/react-router'

import { AuthProvider, useAuth } from './auth'

import { router } from './router'

function InnerApp() {
  const auth = useAuth()
  return 
}

function App() {
  return (
    
      
    
  )
}
```

인증된 라우트에서는 `beforeLoad` 함수를 사용하여 인증 상태를 확인하고, 사용자가 로그인하지 않았다면 **Login 라우트**로 `redirect()`를 **던질** 수 있습니다.

- `src/routes/dashboard.route.tsx`

```tsx
import { createFileRoute, redirect } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  beforeLoad: ({ context, location }) => {
    if (!context.auth.isAuthenticated) {
      throw redirect({
        to: '/login',
        search: {
          redirect: location.href,
        },
      })
    }
  },
})
```

**선택적으로**, 리다이렉트를 호출하는 대신 [Non-Redirected Authentication](#non-redirected-authentication) 방식을 사용하여 로그인 폼을 표시할 수도 있습니다.

이 방식은 특정 레이아웃 아래의 모든 라우트를 보호하기 위해 Layout 또는 Parent Routes와 함께 사용할 수도 있습니다.


