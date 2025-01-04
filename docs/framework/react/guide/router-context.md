# Table of Contents

- [라우터 컨텍스트](#라우터-컨텍스트)
  - [타입이 지정된 라우터 컨텍스트](#타입이-지정된-라우터-컨텍스트)
  - [초기 라우터 컨텍스트 전달하기](#초기-라우터-컨텍스트-전달하기)
    - [라우터 컨텍스트 무효화하기](#라우터-컨텍스트-무효화하기)
  - [라우터 컨텍스트 사용하기](#라우터-컨텍스트-사용하기)
    - [외부 데이터 가져오기 라이브러리는 어떨까요?](#외부-데이터-가져오기-라이브러리는-어떨까요)
  - [React Context/Hooks 사용하기](#react-contexthooks-사용하기)
  - [라우터 컨텍스트 수정하기](#라우터-컨텍스트-수정하기)
  - [누적된 라우트 컨텍스트 처리](#누적된-라우트-컨텍스트-처리)

# 라우터 컨텍스트

TanStack Router의 라우터 컨텍스트는 의존성 주입을 비롯해 다양한 용도로 활용할 수 있는 매우 강력한 도구입니다. 이름 그대로 라우터 컨텍스트는 라우터를 통해 전달되며, 매칭되는 각 라우트로 내려갑니다. 계층 구조의 각 라우트에서 컨텍스트를 수정하거나 추가할 수 있습니다. 라우터 컨텍스트를 실제로 사용할 수 있는 몇 가지 방법을 소개합니다:

- **의존성 주입**
  - 라우트와 모든 하위 라우트에서 직접 가져오거나 생성하지 않고도 사용할 수 있는 의존성(예: 로더 함수, 데이터 페칭 클라이언트, 뮤테이션 서비스)을 제공할 수 있습니다.
  
- **브레드크럼브**
  - 각 라우트의 주요 컨텍스트 객체는 하위로 내려가면서 병합되지만, 각 라우트의 고유한 컨텍스트도 저장됩니다. 이를 통해 각 라우트의 컨텍스트에 브레드크럼브나 메서드를 추가할 수 있습니다.
  
- **동적 메타 태그 관리**
  - 각 라우트의 컨텍스트에 메타 태그를 추가하고, 사용자가 사이트를 탐색할 때 페이지의 메타 태그를 동적으로 업데이트하는 메타 태그 관리자를 사용할 수 있습니다.

이것들은 라우터 컨텍스트를 사용하는 몇 가지 제안된 방법일 뿐입니다. 여러분이 원하는 어떤 용도로든 사용할 수 있습니다!


## 타입이 지정된 라우터 컨텍스트

다른 모든 것과 마찬가지로, 루트 라우터 컨텍스트도 엄격하게 타입이 지정됩니다. 이 타입은 라우트 매칭 트리를 따라 병합되기 때문에, 어떤 라우트의 `beforeLoad` 옵션을 통해 확장할 수 있습니다. 루트 라우터 컨텍스트의 타입을 제한하려면, `createRootRoute()` 함수 대신 `createRootRouteWithContext<YourContextTypeHere>()(routeOptions)` 함수를 사용하여 새로운 라우터 컨텍스트를 생성해야 합니다. 다음은 예제입니다:

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'

interface MyRouterContext {
  user: User
}

// 라우터 컨텍스트를 사용하여 루트 라우트 생성
const rootRoute = createRootRouteWithContext<MyRouterContext>()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // ...
])

// 라우터 컨텍스트를 사용하여 라우터 생성
const router = createRouter({
  routeTree,
})
```


## 초기 라우터 컨텍스트 전달하기

라우터 컨텍스트는 라우터가 생성될 때 전달됩니다. 여러분은 `context` 옵션을 통해 초기 라우터 컨텍스트를 라우터에 전달할 수 있습니다.

> [!TIP]
> 만약 컨텍스트에 필수 속성이 있다면, 초기 라우터 컨텍스트로 전달하지 않을 경우 TypeScript 에러가 발생합니다. 모든 컨텍스트 속성이 선택적이라면 TypeScript 에러가 발생하지 않으며, 컨텍스트 전달은 선택 사항이 됩니다. 라우터 컨텍스트를 전달하지 않으면 기본값으로 `{}`가 설정됩니다.

```tsx
import { createRouter } from '@tanstack/react-router'

// 생성한 routerContext를 사용하여 라우터를 생성합니다.
const router = createRouter({
  routeTree,
  context: {
    user: {
      id: '123',
      name: 'John Doe',
    },
  },
})
```


### 라우터 컨텍스트 무효화하기

라우터에 전달한 컨텍스트 상태를 무효화해야 할 때는 `invalidate` 메서드를 호출하여 라우터가 컨텍스트를 다시 계산하도록 지시할 수 있습니다. 이 방법은 컨텍스트 상태를 업데이트하고 모든 라우트에 대해 컨텍스트를 다시 계산해야 할 때 유용합니다.

```tsx
function useAuth() {
  const router = useRouter()
  const [user, setUser] = useState(null)

  useEffect(() => {
    const unsubscribe = auth.onAuthStateChanged((user) => {
      setUser(user)
      router.invalidate()
    })

    return unsubscribe
  }, [])

  return user
}
```

위 예제에서는 `useAuth` 훅을 사용하여 사용자 인증 상태를 관리합니다. `auth.onAuthStateChanged`를 통해 사용자 상태가 변경될 때마다 `setUser`로 상태를 업데이트하고, `router.invalidate()`를 호출하여 라우터 컨텍스트를 무효화합니다. 이렇게 하면 라우터가 새로운 컨텍스트를 기반으로 모든 라우트를 다시 계산합니다.


## 라우터 컨텍스트 사용하기

라우터 컨텍스트 타입을 정의한 후, 라우트 정의에서 이를 사용할 수 있습니다.

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => fetchTodosByUserId(context.user.id),
})
```

데이터를 가져오거나 변경하는 구현 자체를 주입할 수도 있습니다. 사실, 이 방법을 적극 권장합니다 😜

간단한 함수를 사용하여 할 일 목록을 가져오는 예제를 살펴보겠습니다.

```tsx
const fetchTodosByUserId = async ({ userId }) => {
  const response = await fetch(`/api/todos?userId=${userId}`)
  const data = await response.json()
  return data
}

const router = createRouter({
  routeTree: rootRoute,
  context: {
    userId: '123',
    fetchTodosByUserId,
  },
})
```

그런 다음, 라우트에서 다음과 같이 사용할 수 있습니다.

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: ({ context }) => context.fetchTodosByUserId(context.userId),
})
```


### 외부 데이터 가져오기 라이브러리는 어떨까요?

```tsx
import {
  createRootRouteWithContext,
  createRouter,
} from '@tanstack/react-router'

interface MyRouterContext {
  queryClient: QueryClient
}

const rootRoute = createRootRouteWithContext()({
  component: App,
})

const queryClient = new QueryClient()

const router = createRouter({
  routeTree: rootRoute,
  context: {
    queryClient,
  },
})
```

그리고 라우트에서 다음과 같이 사용할 수 있습니다:

```tsx
// src/routes/todos.tsx
export const Route = createFileRoute('/todos')({
  component: Todos,
  loader: async ({ context }) => {
    await context.queryClient.ensureQueryData({
      queryKey: ['todos', { userId: user.id }],
      queryFn: fetchTodos,
    })
  },
})
```

이 코드는 `@tanstack/react-router` 라이브러리를 사용하여 라우트를 설정하고, `QueryClient`를 통해 데이터를 가져오는 예제입니다. `loader` 함수는 라우트가 로드될 때 데이터를 미리 가져오는 역할을 합니다.


## React Context/Hooks 사용하기

라우트의 `beforeLoad` 또는 `loader` 함수에서 React Context나 Hooks를 사용하려고 할 때, React의 [Hooks 규칙](https://react.dev/reference/rules/rules-of-hooks)을 기억해야 합니다. Hooks는 React 함수 내에서만 사용할 수 있기 때문에, `beforeLoad`나 `loader` 함수에서는 Hooks를 직접 사용할 수 없습니다.

그렇다면 라우트의 `beforeLoad`나 `loader` 함수에서 React Context나 Hooks를 어떻게 사용할 수 있을까요? 라우터 컨텍스트를 통해 React Context나 Hooks를 라우트의 `beforeLoad`나 `loader` 함수로 전달할 수 있습니다.

예제를 통해 `useNetworkStrength` 훅을 라우트의 `loader` 함수로 전달하는 방법을 살펴보겠습니다.

- `src/routes/__root.tsx`

```tsx
// 먼저 루트 라우트의 컨텍스트를 타입으로 정의합니다.
import { createRootRouteWithContext } from '@tanstack/react-router'
import { useNetworkStrength } from '@/hooks/useNetworkStrength'

interface MyRouterContext {
  networkStrength: ReturnType
}

export const Route = createRootRouteWithContext()({
  component: App,
})
```

이 예제에서는 `<RouterProvider />`를 사용하여 라우터를 렌더링하기 전에 훅을 초기화합니다. 이렇게 하면 훅이 React 환경에서 호출되므로 Hooks 규칙을 준수할 수 있습니다.

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

export const router = createRouter({
  routeTree,
  context: {
    networkStrength: undefined!, // React 환경에서 이 값을 설정할 것입니다.
  },
})
```

- `src/main.tsx`

```tsx
import { RouterProvider } from '@tanstack/react-router'
import { router } from './router'

import { useNetworkStrength } from '@/hooks/useNetworkStrength'

function App() {
  const networkStrength = useNetworkStrength()
  // 훅에서 반환된 값을 라우터 컨텍스트에 주입합니다.
  return 
}

// ...
```

이제 라우트의 `loader` 함수에서 라우터 컨텍스트를 통해 `networkStrength` 훅에 접근할 수 있습니다.

- `src/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: Posts,
  loader: ({ context }) => {
    if (context.networkStrength === 'STRONG') {
      // 특정 작업을 수행합니다.
    }
  },
})
```


## 라우터 컨텍스트 수정하기

라우터 컨텍스트는 라우트 트리를 따라 전달되며, 각 라우트에서 병합됩니다. 이는 각 라우트에서 컨텍스트를 수정할 수 있고, 그 수정 사항이 모든 하위 라우트에서 사용 가능하다는 것을 의미합니다. 다음은 예제입니다:

- `src/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

interface MyRouterContext {
  foo: boolean
}

export const Route = createRootRouteWithContext()({
  component: App,
})
```

- `src/router.tsx`

```tsx
import { createRouter } from '@tanstack/react-router'

import { routeTree } from './routeTree.gen'

const router = createRouter({
  routeTree,
  context: {
    foo: true,
  },
})
```

- `src/routes/todos.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/todos')({
  component: Todos,
  beforeLoad: () => {
    return {
      bar: true,
    }
  },
  loader: ({ context }) => {
    context.foo // true
    context.bar // true
  },
})
```


## 누적된 라우트 컨텍스트 처리

컨텍스트, 특히 격리된 라우트 `context` 객체는 모든 매칭된 라우트의 컨텍스트 객체를 누적하고 처리하는 작업을 간단하게 만듭니다. 아래 예제에서는 매칭된 모든 라우트 컨텍스트를 사용하여 브레드크럼 트레일을 생성합니다:

```tsx
// src/routes/__root.tsx
export const Route = createRootRoute({
  component: () => {
    const matches = useRouterState({ select: (s) => s.matches })

    const breadcrumbs = matches
      .filter((match) => match.context.getTitle)
      .map(({ pathname, context }) => {
        return {
          title: context.getTitle(),
          path: pathname,
        }
      })

    // ...
  },
})
```

동일한 라우트 컨텍스트를 사용하여 페이지의 `<head>`에 타이틀 태그를 생성할 수도 있습니다:

```tsx
// src/routes/__root.tsx
export const Route = createRootRoute({
  component: () => {
    const matches = useRouterState({ select: (s) => s.matches })

    const matchWithTitle = [...matches]
      .reverse()
      .find((d) => d.context.getTitle)

    const title = matchWithTitle?.context.getTitle() || 'My App'

    return (
      
        
          {title}
        
        {/* ... */}
      
    )
  },
})
```


