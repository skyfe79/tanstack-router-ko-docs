# 기본 개념 배우기

이 가이드는 여러분이 프로젝트를 어떻게 설정했는지와 상관없이, TanStack Start가 어떻게 동작하는지에 대한 기본 개념을 이해하는 데 도움을 줄 것입니다.


## 의존성

TanStack Start는 (현재\*) [Vinxi](https://vinxi.vercel.app/), [Nitro](https://nitro.unjs.io/), 그리고 [TanStack Router](https://tanstack.com/router)를 기반으로 동작합니다.

- **TanStack Router**: 웹 애플리케이션을 구축하기 위한 라우터입니다.
- **Nitro**: 서버 애플리케이션을 구축하기 위한 프레임워크입니다.
- **Vinxi**: 웹 애플리케이션을 구축하기 위한 서버 프레임워크입니다.

> [!NOTE] Vinxi는 버전 1.0.0이 출시되기 전에 제거될 예정이며, TanStack은 Vite와 Nitro만을 사용할 것입니다. Vinxi를 사용하는 커맨드와 API는 Vite 플러그인으로 대체될 가능성이 높습니다.


## 모든 것은 라우터에서 시작된다

이 파일은 Start 프로젝트 내에서 사용되는 TanStack Router의 동작을 정의한다. 여기서는 기본 [미리 가져오기 기능](../guide/preloading.md)부터 [캐시 만료 설정](../guide/data-loading.md)까지 모든 것을 구성할 수 있다.

```tsx
// app/router.tsx
import { createRouter as createTanStackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  const router = createTanStackRouter({
    routeTree,
  })

  return router
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType
  }
}
```


## 라우트 생성

`routeTree.gen.ts` 파일은 여러분이 TanStack Start를 처음 실행할 때(`npm run dev` 또는 `npm run start` 명령어로) 생성됩니다. 이 파일은 생성된 라우트 트리와 TanStack Start를 완전히 타입 안전하게 만들어주는 몇 가지 TypeScript 유틸리티를 포함하고 있습니다.


## 서버 진입점

TanStack Start는 클라이언트 중심 API로 설계되었지만, 기본적으로 풀스택 프레임워크입니다. 이는 동적 및 정적 사용 사례를 포함한 모든 경우에 애플리케이션의 초기 HTML 페이로드를 렌더링하기 위해 서버 또는 빌드 타임 진입점이 필요함을 의미합니다.

이 작업은 `app/ssr.tsx` 파일을 통해 수행됩니다:

```tsx
// app/ssr.tsx
import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'
import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

애플리케이션을 정적으로 생성하거나 동적으로 제공하는 경우, `ssr.tsx` 파일은 모든 SSR 관련 작업을 수행하는 진입점입니다.

- 각 요청마다 새로운 라우터를 생성하는 것이 중요합니다. 이는 라우터가 처리하는 데이터가 요청에 고유하도록 보장합니다.
- `getRouterManifest` 함수는 라우터 매니페스트를 생성하는 데 사용됩니다. 이 매니페스트는 애플리케이션의 리소스 관리 및 미리 가져오기와 같은 여러 측면을 결정하는 데 활용됩니다.
- `defaultStreamHandler` 함수는 애플리케이션을 스트림으로 렌더링하는 데 사용됩니다. 이를 통해 클라이언트에게 HTML을 스트리밍할 수 있습니다. (이것은 기본 핸들러이며, `defaultRenderHandler`와 같은 다른 핸들러를 사용하거나 직접 핸들러를 만들 수도 있습니다)


## 클라이언트 진입점

HTML을 클라이언트로 전달하는 것은 전투의 절반에 불과합니다. 클라이언트로 전달된 후에는 라우트가 클라이언트로 해결되면 클라이언트 측 자바스크립트를 **하이드레이션(hydrate)**해야 합니다. 이를 위해 애플리케이션의 루트를 `StartClient` 컴포넌트로 하이드레이션합니다:

```tsx
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

이 코드는 사용자의 초기 서버 요청이 완료된 후 클라이언트 측 라우팅을 시작할 수 있게 해줍니다.


## 애플리케이션의 루트

클라이언트 진입점 외에도, 애플리케이션의 `__root` 라우트는 애플리케이션의 진입점 역할을 합니다. 이 파일에 있는 코드는 홈 페이지를 포함한 모든 라우트를 감싸게 됩니다. 이 라우트는 전체 애플리케이션을 위한 레이아웃 라우트처럼 동작합니다.

**항상 렌더링되기 때문에**, 애플리케이션의 기본 구조를 구성하고 전역 로직을 처리하기에 가장 적합한 위치입니다.

```tsx
// app/routes/__root.tsx
import {
  Outlet,
  ScrollRestoration,
  createRootRoute,
} from '@tanstack/react-router'
import { Meta, Scripts } from '@tanstack/start'
import type { ReactNode } from 'react'

export const Route = createRootRoute({
  head: () => ({
    meta: [
      {
        charSet: 'utf-8',
      },
      {
        name: 'viewport',
        content: 'width=device-width, initial-scale=1',
      },
      {
        title: 'TanStack Start Starter',
      },
    ],
  }),
  component: RootComponent,
})

function RootComponent() {
  return (
    
      
    
  )
}

function RootDocument({ children }: Readonly) {
  return (
    
      
        
      
      
        {children}
        
        
      
    
  )
}
```

- 이 레이아웃은 SPA 모드가 도입되면 변경될 수 있습니다. SPA 모드는 루트 라우트가 페이지별 콘텐츠 없이 SPA 기본 구조를 렌더링할 수 있게 해줍니다.
- `ScrollRestoration` 컴포넌트를 확인하세요. 이 컴포넌트는 라우트 간 네비게이션 시 페이지의 스크롤 위치를 복원하는 데 사용됩니다.
- `Scripts` 컴포넌트도 주목하세요. 이 컴포넌트는 애플리케이션의 클라이언트 측 자바스크립트를 로드하는 데 사용됩니다.


## 라우트

라우트는 TanStack Router의 핵심 기능 중 하나로, [라우팅 가이드](../../guide/file-based-routing)에서 자세히 다룹니다. 간단히 요약하면 다음과 같습니다:

- `createFileRoute` 함수를 사용해 라우트를 정의합니다.
- 라우트는 자동으로 코드 분할(code-split)되고 지연 로딩(lazy-loaded)됩니다.
- 중요한 데이터 가져오기는 라우트의 로더(loader)에서 조정됩니다.
- 그 외에도 다양한 기능이 제공됩니다!

```tsx
// app/routes/index.tsx
import * as fs from 'node:fs'
import { createFileRoute, useRouter } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

const filePath = 'count.txt'

// 파일에서 카운트 값을 읽어오는 함수
async function readCount() {
  return parseInt(
    await fs.promises.readFile(filePath, 'utf-8').catch(() => '0'),
  )
}

// GET 요청을 처리하는 서버 함수
const getCount = createServerFn({
  method: 'GET',
}).handler(() => {
  return readCount()
})

// POST 요청을 처리하는 서버 함수
const updateCount = createServerFn({ method: 'POST' })
  .validator((d: number) => d)
  .handler(async ({ data }) => {
    const count = await readCount()
    await fs.promises.writeFile(filePath, `${count + data}`)
  })

// 루트 경로('/')에 대한 라우트 정의
export const Route = createFileRoute('/')({
  component: Home,
  loader: async () => await getCount(),
})

// 홈 컴포넌트
function Home() {
  const router = useRouter()
  const state = Route.useLoaderData()

  return (
    <button
      type="button"
      onClick={() => {
        updateCount({ data: 1 }).then(() => {
          router.invalidate()
        })
      }}
    >
      {state}에 1을 더할까요?
    </button>
  )
}
```

이 예제에서는 파일 기반 라우팅을 사용해 간단한 카운터를 구현했습니다. 버튼을 클릭하면 카운트 값이 증가하고, 라우터를 통해 데이터를 갱신합니다.


## 네비게이션

TanStack Start는 TanStack Router를 기반으로 100% 구축되었기 때문에, TanStack Router의 모든 네비게이션 기능을 사용할 수 있습니다. 요약하면 다음과 같습니다:

- 새로운 라우트로 이동하려면 `Link` 컴포넌트를 사용하세요.
- 명령형으로 네비게이션을 수행하려면 `useNavigate` 훅을 사용하세요.
- 애플리케이션 어디에서나 `useRouter` 훅을 사용하여 라우터 인스턴스에 접근하고 무효화 작업을 수행할 수 있습니다.
- 상태를 반환하는 모든 라우터 훅은 반응형입니다. 즉, 적절한 상태가 변경되면 자동으로 다시 실행됩니다.

다음은 `Link` 컴포넌트를 사용하여 새로운 라우트로 이동하는 간단한 예제입니다:

```tsx
import { Link } from '@tanstack/react-router'

function Home() {
  return (
    <Link to="/about">About</Link>
  )
}
```

네비게이션에 대한 더 자세한 정보는 [네비게이션 가이드](../../guide/navigation)를 참고하세요.


## 서버 함수 (RPCs)

위에서 `createServerFn`을 사용해 만든 **서버 함수**를 눈치챘을 겁니다. 이는 TanStack의 가장 강력한 기능 중 하나로, SSR 중인 서버와 클라이언트 양쪽에서 호출할 수 있는 서버 측 함수를 만들 수 있게 해줍니다!

서버 함수가 어떻게 동작하는지 간단히 살펴보겠습니다:

- 서버 함수는 `createServerFn` 함수를 사용해 생성됩니다.
- SSR 중인 서버와 클라이언트 양쪽에서 호출할 수 있습니다.
- 서버에서 데이터를 가져오거나 다른 서버 측 작업을 수행하는 데 사용할 수 있습니다.

다음은 서버 함수를 사용해 서버에서 데이터를 가져와 반환하는 간단한 예제입니다:

```tsx
import { createServerFn } from '@tanstack/start'
import * as fs from 'node:fs'
import { z } from 'zod'

const getUserById = createServerFn({ method: 'GET' })
  // 함수로 전달되는 데이터는 항상 검증해야 합니다. 여기서는 Zod를 사용합니다.
  .validator(z.string())
  // 핸들러 함수는 서버 측 로직을 수행하는 곳입니다.
  .handler(async ({ data }) => {
    return db.query.users.findFirst({ where: eq(users.id, data) })
  })

// 애플리케이션의 다른 곳에서
const user = await getUserById({ data: '1' })
```

서버 함수에 대해 더 알아보려면 [서버 함수 가이드](../server-functions)를 확인하세요.


### 데이터 변경(Mutations)

서버 함수를 사용하면 서버에서 데이터를 변경할 수도 있습니다. 이는 동일한 `createServerFn` 함수를 사용하지만, 변경으로 인해 영향을 받은 클라이언트의 데이터를 무효화해야 한다는 추가 요구사항이 있습니다.

- TanStack Router만 사용하는 경우, `router.invalidate()` 메서드를 사용하여 모든 라우터 데이터를 무효화하고 다시 가져올 수 있습니다.
- TanStack Query를 사용하는 경우, `queryClient.invalidateQueries()` 메서드를 사용하여 데이터를 무효화하거나, 특정 쿼리를 대상으로 하는 더 구체적인 메서드를 사용할 수 있습니다.

다음은 서버 함수를 사용하여 서버에서 데이터를 변경하고 클라이언트에서 데이터를 무효화하는 간단한 예제입니다:

```tsx
import { createServerFn } from '@tanstack/start'

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
})

const updateUser = createServerFn({ method: 'POST' })
  .validator(UserSchema)
  .handler(async ({ data }) => {
    return db
      .update(users)
      .set({ name: data.name })
      .where(eq(users.id, data.id))
  })

// 애플리케이션의 다른 부분에서
await updateUser({ data: { id: '1', name: 'John' } })
```

데이터 변경에 대해 더 자세히 알아보려면 [데이터 변경 가이드](../../guide/data-mutations)를 참고하세요.


## 데이터 로딩

TanStack Router의 또 다른 강력한 기능은 데이터 로딩입니다. 이를 통해 SSR(서버 사이드 렌더링)을 위한 데이터를 가져오고, 라우트가 렌더링되기 전에 데이터를 미리 로드할 수 있습니다. 이 기능은 라우트의 `loader` 함수를 사용하여 구현됩니다.

데이터 로딩이 어떻게 동작하는지 간단히 살펴보겠습니다:

- 데이터 로딩은 라우트의 `loader` 함수를 통해 수행됩니다.
- 데이터 로더는 **isomorphic**(동형)으로, 서버와 클라이언트 양쪽에서 실행됩니다.
- 서버 전용 로직을 수행하려면 로더 내부에서 서버 함수를 호출합니다.
- TanStack Query와 유사하게, 데이터 로더는 클라이언트에서 캐시되며, 데이터가 오래된 경우 백그라운드에서 재사용되거나 다시 가져옵니다.

데이터 로딩에 대해 더 자세히 알아보려면 [데이터 로딩 가이드](../../guide/data-loading)를 참고하세요.


