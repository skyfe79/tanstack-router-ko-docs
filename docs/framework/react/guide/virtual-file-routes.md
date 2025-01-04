# 가상 파일 라우트(Virtual File Routes)

> 우리는 [가상 파일 라우트 개념을 처음 제안한 Remix 팀](https://www.youtube.com/watch?v=fjTX8hQTlEc&t=730s)에게 감사의 말을 전합니다. 그들의 작업에서 영감을 받아 TanStack Router의 기존 파일 기반 라우트 트리 생성 방식에 적용했습니다.

가상 파일 라우트는 프로젝트 내 실제 파일을 참조하는 코드를 사용해 프로그래밍 방식으로 라우트 트리를 구축할 수 있는 강력한 개념입니다. 이 기능은 다음과 같은 경우에 유용합니다:

- 기존 라우트 구조를 유지하고 싶을 때
- 라우트 파일의 위치를 커스텀하고 싶을 때
- TanStack Router의 파일 기반 라우트 생성을 완전히 재정의하고 자신만의 규칙을 만들고 싶을 때

아래는 프로젝트 내 실제 파일 집합에 라우트 트리를 매핑하는 가상 파일 라우트 사용 예제입니다:

```tsx
// routes.ts
import {
  rootRoute,
  route,
  index,
  layout,
  physical,
} from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  index('index.tsx'),
  layout('layout.tsx', [
    route('/dashboard', 'app/dashboard.tsx', [
      index('app/dashboard-index.tsx'),
      route('/invoices', 'app/dashboard-invoices.tsx', [
        index('app/invoices-index.tsx'),
        route('$id', 'app/invoice-detail.tsx'),
      ]),
    ]),
    physical('/posts', 'posts'),
  ]),
])
```


## 설정

가상 파일 라우트는 다음 두 가지 방법으로 설정할 수 있습니다:

- Vite/Rspack/Webpack용 `TanStackRouter` 플러그인
- TanStack Router CLI용 `tsr.config.json` 파일


## TanStackRouter 플러그인을 통한 설정

Vite/Rspack/Webpack에서 `TanStackRouter` 플러그인을 사용한다면, 플러그인 설정 시 `virtualRoutesConfig` 옵션에 라우트 파일 경로를 전달하여 가상 파일 라우트를 구성할 수 있습니다:

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  plugins: [
    TanStackRouterVite({
      virtualRouteConfig: './routes.ts',
    }),
    react(),
  ],
})
```

또는, 설정 파일에서 직접 가상 라우트를 정의할 수도 있습니다:

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'
import { rootRoute } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [
  // ... 나머지 가상 라우트 트리
])

export default defineConfig({
  plugins: [TanStackRouterVite({ virtualRouteConfig: routes }), react()],
})
```


## 가상 파일 라우트 생성하기

가상 파일 라우트를 생성하려면 `@tanstack/virtual-file-routes` 패키지를 임포트해야 합니다. 이 패키지는 프로젝트 내 실제 파일을 참조하는 가상 라우트를 생성할 수 있는 여러 함수를 제공합니다. 패키지에서 내보내지는 주요 유틸리티 함수는 다음과 같습니다:

- `rootRoute` - 가상 루트 라우트를 생성합니다.
- `route` - 가상 라우트를 생성합니다.
- `index` - 가상 인덱스 라우트를 생성합니다.
- `layout` - 가상 레이아웃 라우트를 생성합니다.
- `physical` - 물리적 가상 라우트를 생성합니다 (이에 대해서는 나중에 자세히 설명합니다).


## 가상 루트 라우트

`rootRoute` 함수는 가상 루트 라우트를 생성하는 데 사용됩니다. 이 함수는 파일 이름과 자식 라우트 배열을 인자로 받습니다. 다음은 가상 루트 라우트의 예제입니다:

```tsx
// routes.ts
import { rootRoute } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  // ... 자식 라우트들
])
```


## 가상 라우트(Virtual Route)

`route` 함수는 가상 라우트를 생성하는 데 사용됩니다. 이 함수는 경로(path), 파일 이름, 그리고 자식 라우트 배열을 인자로 받습니다. 아래는 가상 라우트를 정의하는 예제입니다:

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/about', 'about.tsx', [
    // ... 자식 라우트들
  ]),
])
```

파일 이름 없이 가상 라우트를 정의할 수도 있습니다. 이 방식은 자식 라우트들에게 공통된 경로 접두사를 설정할 때 유용합니다:

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/hello', [
    route('/world', 'world.tsx'), // 전체 경로는 "/hello/world"가 됨
    route('/universe', 'universe.tsx'), // 전체 경로는 "/hello/universe"가 됨
  ]),
])
```


## 가상 인덱스 라우트

`index` 함수는 가상 인덱스 라우트를 생성하는 데 사용됩니다. 이 함수는 파일 이름을 인자로 받습니다. 다음은 가상 인덱스 라우트의 예제입니다:

```tsx
import { index } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [index('index.tsx')])
```


## 가상 레이아웃 라우트

`layout` 함수는 가상 레이아웃 라우트를 생성하는 데 사용됩니다. 이 함수는 파일 이름, 자식 라우트 배열, 그리고 선택적으로 레이아웃 ID를 인자로 받습니다. 다음은 가상 레이아웃 라우트의 예제입니다:

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('layout.tsx', [
    // ... 자식 라우트들
  ]),
])
```

파일 이름과 다른 고유한 식별자를 레이아웃에 부여하려면 레이아웃 ID를 지정할 수도 있습니다:

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('my-layout-id', 'layout.tsx', [
    // ... 자식 라우트들
  ]),
])
```


## 물리적 가상 라우트(Physical Virtual Routes)

물리적 가상 라우트는 특정 URL 경로 아래에 TanStack Router의 파일 기반 라우팅 규칙을 따르는 디렉토리를 "마운트"하는 방법입니다. 이 기능은 라우트 트리 상위 계층에서 일부만 커스터마이징하고, 하위 라우트와 디렉토리에는 표준 파일 기반 라우팅 규칙을 사용하고 싶을 때 유용합니다.

다음과 같은 파일 구조를 예로 들어보겠습니다:

```
/routes
├── root.tsx
├── index.tsx
├── layout.tsx
├── app
│   ├── dashboard.tsx
│   ├── dashboard-index.tsx
│   ├── dashboard-invoices.tsx
│   ├── invoices-index.tsx
│   ├── invoice-detail.tsx
└── posts
    ├── index.tsx
    ├── $postId.tsx
    ├── $postId.edit.tsx
    ├── comments/
    │   ├── index.tsx
    │   ├── $commentId.tsx
    └── likes/
        ├── index.tsx
        ├── $likeId.tsx
```

`posts` 디렉토리를 제외한 나머지 부분은 가상 라우트로 커스터마이징하고, `/posts` 경로 아래에 `posts` 디렉토리를 물리적 가상 라우트로 마운트해 보겠습니다:

```tsx
// routes.ts
export const routes = rootRoute('root.tsx', [
  // 일반적인 가상 라우트 설정
  index('index.tsx'),
  layout('layout.tsx', [
    route('/dashboard', 'app/dashboard.tsx', [
      index('app/dashboard-index.tsx'),
      route('/invoices', 'app/dashboard-invoices.tsx', [
        index('app/invoices-index.tsx'),
        route('$id', 'app/invoice-detail.tsx'),
      ]),
    ]),
    // `/posts` 경로 아래에 `posts` 디렉토리 마운트
    physical('/posts', 'posts'),
  ]),
])
```

이렇게 하면 `posts` 디렉토리 내의 파일들은 파일 기반 라우팅 규칙을 그대로 따르면서도, 상위 라우트는 커스터마이징할 수 있습니다.


## TanStack Router에서 파일 기반 라우팅 내부의 가상 라우트

이전 섹션에서는 TanStack Router의 파일 기반 라우팅 규칙을 가상 라우트 설정 안에서 사용하는 방법을 살펴봤습니다.  
하지만 반대의 경우도 가능합니다.  
여러분은 앱의 주요 라우트 트리를 TanStack Router의 파일 기반 라우팅 규칙으로 구성하고, 특정 서브트리에 대해서만 가상 라우트 설정을 선택할 수 있습니다.

다음과 같은 파일 구조를 생각해보세요:

```
/routes
├── __root.tsx
├── foo
│   ├── bar
│   │   ├── __virtual.ts
│   │   ├── details.tsx
│   │   ├── home.tsx
│   │   └── route.ts
│   └── bar.tsx
└── index.tsx
```

여기서 `bar` 디렉토리를 주목해보세요. 이 디렉토리에는 `__virtual.ts`라는 특별한 파일이 있습니다. 이 파일은 생성기(generator)에게 이 디렉토리(와 그 하위 디렉토리)에 대해 가상 파일 라우트 설정으로 전환하도록 지시합니다.

`__virtual.ts`는 라우트 트리의 특정 서브트리에 대한 가상 라우트를 설정합니다. 이전에 설명한 것과 동일한 API를 사용하지만, 이 서브트리에 대해 `rootRoute`가 정의되지 않는다는 점이 다릅니다:

```tsx
// routes/foo/bar/__virtual.ts
import {
  defineVirtualSubtreeConfig,
  index,
  route,
} from '@tanstack/virtual-file-routes'

export default defineVirtualSubtreeConfig([
  index('home.tsx'),
  route('$id', 'details.tsx'),
])
```

`defineVirtualSubtreeConfig` 헬퍼 함수는 Vite의 `defineConfig`를 모델로 만들어졌으며, 기본 내보내기를 통해 서브트리 설정을 정의할 수 있게 해줍니다. 기본 내보내기는 다음 중 하나일 수 있습니다:

- 서브트리 설정 객체
- 서브트리 설정 객체를 반환하는 함수
- 서브트리 설정 객체를 반환하는 비동기 함수


## 시작하기

TanStack Router의 파일 기반 라우팅 규칙과 가상 라우트 설정을 원하는 대로 조합하여 사용할 수 있습니다.  
더 깊이 들어가 보겠습니다!  
다음 예제를 살펴보세요. 이 예제는 파일 기반 라우팅 규칙으로 시작하고, `/posts` 경로에서는 가상 라우트 설정으로 전환합니다. 그리고 `/posts/lets-go` 경로에서는 다시 파일 기반 라우팅 규칙으로 돌아가며, `/posts/lets-go/deeper` 경로에서는 다시 가상 라우트 설정으로 전환합니다.

```
├── __root.tsx
├── index.tsx
├── posts
│   ├── __virtual.ts
│   ├── details.tsx
│   ├── home.tsx
│   └── lets-go
│       ├── deeper
│       │   ├── __virtual.ts
│       │   └── home.tsx
│       └── index.tsx
└── posts.tsx
```


## TanStack Router CLI를 통한 설정

TanStack Router CLI를 사용한다면, `tsr.config.json` 파일에 라우트 파일의 경로를 정의하여 가상 파일 라우트를 설정할 수 있습니다:

```json
// tsr.config.json
{
  "virtualRouteConfig": "./routes.ts"
}
```

또는 가상 라우트를 직접 설정할 수도 있습니다. 이 방법은 덜 일반적이지만, `tsr.config.json` 파일에 `virtualRouteConfig` 객체를 추가하고 가상 라우트를 정의한 후, `@tanstack/virtual-file-routes` 패키지에서 제공하는 `rootRoute`, `route`, `index` 등의 함수를 호출하여 생성된 JSON을 전달하는 방식으로 설정할 수 있습니다:

```json
// tsr.config.json
{
  "virtualRouteConfig": {
    "type": "root",
    "file": "root.tsx",
    "children": [
      {
        "type": "index",
        "file": "home.tsx"
      },
      {
        "type": "route",
        "file": "posts/posts.tsx",
        "path": "/posts",
        "children": [
          {
            "type": "index",
            "file": "posts/posts-home.tsx"
          },
          {
            "type": "route",
            "file": "posts/posts-detail.tsx",
            "path": "$postId"
          }
        ]
      },
      {
        "type": "layout",
        "id": "first",
        "file": "layout/first-layout.tsx",
        "children": [
          {
            "type": "layout",
            "id": "second",
            "file": "layout/second-layout.tsx",
            "children": [
              {
                "type": "route",
                "file": "a.tsx",
                "path": "/layout-a"
              },
              {
                "type": "route",
                "file": "b.tsx",
                "path": "/layout-b"
              }
            ]
          }
        ]
      }
    ]
  }
}
```


