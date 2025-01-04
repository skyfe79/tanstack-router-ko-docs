# 프로젝트 처음부터 만들기

> [!NOTE]
> 예제 프로젝트를 사용하거나 클론한 프로젝트로 빠르게 시작하려면 이 가이드를 건너뛰고 [기초 학습](../learn-the-basics) 가이드로 이동하세요.

_TanStack Start 프로젝트를 처음부터 만들어 보고 싶으신가요?_

이 가이드에서는 **아주** 기본적인 TanStack Start 웹 애플리케이션을 만드는 방법을 설명합니다. 함께 TanStack Start를 사용하여 다음과 같은 작업을 수행할 것입니다:

- 인덱스 페이지를 제공하고...
- 카운터를 표시하며...
- 카운터를 지속적으로 증가시키는 버튼을 추가합니다.

[여기서 결과물을 확인할 수 있습니다](https://stackblitz.com/github/tanstack/router/tree/main/examples/react/start-counter)

새 프로젝트 디렉토리를 만들고 초기화해 봅시다.

```shell
mkdir myApp
cd myApp
npm init -y
```


## 타입스크립트 설정

TanStack Start와 함께 타입스크립트를 사용하는 것을 적극 권장합니다. 최소한 아래 설정을 포함한 `tsconfig.json` 파일을 생성하세요:

```jsonc
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "moduleResolution": "Bundler",
    "module": "ESNext",
    "target": "ES2022",
    "skipLibCheck": true,
    "strictNullChecks": true,
  },
}
```


## 의존성 설치

TanStack Start는 현재 [Vinxi](https://vinxi.vercel.app/)와 [TanStack Router](https://tanstack.com/router)를 기반으로 동작하며, 이들을 의존성으로 필요로 합니다.

> [!NOTE] > \*Vinxi는 버전 1.0.0이 출시되기 전에 제거될 예정이며, TanStack은 Vite와 Nitro만을 사용할 계획입니다. Vinxi를 사용하는 커맨드와 API는 Vite 플러그인으로 대체될 가능성이 높습니다.

이를 설치하려면 다음 명령어를 실행하세요:

```shell
npm i @tanstack/start @tanstack/react-router vinxi
```

또한 React와 Vite React 플러그인이 필요하므로, 이들도 함께 설치하세요:

```shell
npm i react react-dom; npm i -D @vitejs/plugin-react
```

그리고 TypeScript도 설치합니다:

```shell
npm i -D typescript @types/react @types/react-dom
```


## 설정 파일 업데이트

먼저 `package.json` 파일을 수정하여 새로운 Vinxi 진입점을 참조하고 `"type": "module"`을 설정합니다.

```json
{
  // ...
  "type": "module",
  "scripts": {
    "dev": "vinxi dev",
    "build": "vinxi build",
    "start": "vinxi start"
  }
}
```

Vinxi가 TanStack Start의 최소 동작을 시작하도록 하려면 `app.config.ts` 파일을 설정해야 합니다.

```typescript
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({})
```


## 기본 템플릿 추가하기

TanStack Start를 사용하려면 다음 네 가지 파일이 필요합니다:

1. 라우터 설정 파일
2. 서버 진입점 파일
3. 클라이언트 진입점 파일
4. 애플리케이션의 루트 파일

설정이 완료되면 다음과 같은 파일 트리가 생성됩니다:

```
.
├── app/
│   ├── routes/
│   │   └── `__root.tsx`
│   ├── `client.tsx`
│   ├── `router.tsx`
│   ├── `routeTree.gen.ts`
│   └── `ssr.tsx`
├── `.gitignore`
├── `app.config.ts`
├── `package.json`
└── `tsconfig.json`
```


## 라우터 설정

이 파일은 Start 내에서 사용되는 TanStack Router의 동작을 정의합니다. 여기서는 기본 [미리 가져오기 기능](../guide/preloading.md)부터 [캐시 만료 설정](../guide/data-loading.md)까지 모든 것을 구성할 수 있습니다.

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

> `routeTree.gen.ts` 파일은 아직 존재하지 않습니다.  
> 이 파일은 TanStack Start를 처음 실행할 때(`npm run dev` 또는 `npm run start`) 자동으로 생성됩니다.


## 서버 진입점

TanStack Start는 [SSR(서버 사이드 렌더링)](https://unicorn-utterances.com/posts/what-is-ssr-and-ssg) 프레임워크이기 때문에, 라우터 정보를 서버 진입점으로 전달해야 합니다:

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

이 코드를 통해 사용자가 특정 라우트에 접근할 때 실행해야 할 라우트와 로더를 알 수 있습니다.


## 클라이언트 진입점

이제 라우트가 클라이언트로 해결될 때 클라이언트 측 자바스크립트를 하이드레이트할 방법이 필요합니다. 이를 위해 동일한 라우터 정보를 클라이언트 진입점으로 전달합니다:

```tsx
// app/client.tsx
import { hydrateRoot } from 'react-dom/client'
import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

hydrateRoot(document, <StartClient router={router} />)
```

이렇게 하면 사용자의 초기 서버 요청이 완료된 후 클라이언트 측 라우팅을 시작할 수 있습니다.


## 애플리케이션의 루트

마지막으로, 애플리케이션의 루트를 생성해야 합니다. 이 파일은 모든 다른 라우트의 진입점 역할을 합니다. 이 파일의 코드는 애플리케이션의 모든 라우트를 감싸게 됩니다.

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
    <RootDocument>
      <Outlet />
      <ScrollRestoration />
    </RootDocument>
  )
}

function RootDocument({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <head>
        <Meta />
        <Scripts />
      </head>
      <body>
        {children}
      </body>
    </html>
  )
}
```

이 코드는 애플리케이션의 루트 컴포넌트를 정의합니다. `RootComponent`는 `RootDocument` 컴포넌트를 사용하여 HTML 문서의 구조를 정의하고, `Outlet`을 통해 자식 라우트를 렌더링합니다. 또한, `ScrollRestoration`을 사용하여 페이지 이동 시 스크롤 위치를 복원합니다. `Meta`와 `Scripts`는 각각 메타 태그와 스크립트를 관리하는 데 사용됩니다.


## 첫 번째 라우트 작성하기

기본 템플릿 설정이 완료되었으니, 이제 첫 번째 라우트를 작성할 차례입니다. `app/routes` 디렉토리에 새로운 파일을 생성하여 시작합니다.

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

// 루트 경로('/')에 대한 라우트 설정
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
      Add 1 to {state}?
    </button>
  )
}
```

이제 여러분은 TanStack Start 프로젝트를 설정하고 첫 번째 라우트를 작성했습니다. 🎉

서버를 실행하려면 `npm run dev` 명령어를 입력하고, `http://localhost:3000`으로 이동하여 라우트가 동작하는지 확인할 수 있습니다.

애플리케이션을 배포하고 싶다면 [호스팅 가이드](./hosting.md)를 참고하세요.


