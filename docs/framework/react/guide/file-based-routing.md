# Table of Contents

- [파일 기반 라우팅](#파일-기반-라우팅)
  - [파일 기반 라우팅이란?](#파일-기반-라우팅이란)
  - [`/`와 `.` 중 어떤 걸 쓸까?](#와--중-어떤-걸-쓸까)
  - [디렉토리 라우트](#디렉토리-라우트)
  - [플랫 라우팅(Flat Routing)](#플랫-라우팅flat-routing)
  - [플랫(Flat)과 디렉토리(Directory) 라우트 혼합 사용](#플랫flat과-디렉토리directory-라우트-혼합-사용)
  - [동적 경로 파라미터](#동적-경로-파라미터)
  - [경로 없는 라우트(Pathless Routes)](#경로-없는-라우트pathless-routes)
  - [파일 이름 규칙](#파일-이름-규칙)
  - [설치](#설치)
    - [Vite로 설정하기](#vite로-설정하기)
    - [Rspack/Rsbuild 설정하기](#rspackrsbuild-설정하기)
    - [Webpack으로 설정하기](#webpack으로-설정하기)
    - [Esbuild 설정하기](#esbuild-설정하기)
    - [TanStack Router CLI로 설정하기](#tanstack-router-cli로-설정하기)
      - [`generate` 커맨드 사용하기](#generate-커맨드-사용하기)
      - [`watch` 커맨드 사용하기](#watch-커맨드-사용하기)
    - [테스트 중에 TanStack Router 플러그인 비활성화하기](#테스트-중에-tanstack-router-플러그인-비활성화하기)
    - [생성된 라우트 트리 파일 무시하기](#생성된-라우트-트리-파일-무시하기)
  - [설정](#설정)
    - [설정 옵션](#설정-옵션)
  - [라우트 포함/제외](#라우트-포함제외)
    - [라우트 포함 예제](#라우트-포함-예제)
    - [라우트 제외 예제](#라우트-제외-예제)

# 파일 기반 라우팅

TanStack Router 문서의 대부분은 파일 기반 라우팅을 다루고 있습니다. 이 문서는 파일 기반 라우팅을 구성하는 방법과 그 작동 방식에 대한 기술적 세부 사항을 더 깊이 이해하는 데 도움을 주기 위해 작성되었습니다. 파일 기반 라우팅은 TanStack Router를 구성하는 선호되고 권장되는 방법이지만, 원한다면 [코드 기반 라우팅](./code-based-routing.md)을 사용할 수도 있습니다.

> [!NOTE]
> 🧠 이미 파일 기반 라우팅이 어떻게 동작하는지 알고 있고 설정 방법을 찾고 있다면, [설치](#installation) 섹션으로 건너뛰어도 됩니다. 또는 구성 옵션을 찾고 있다면 [옵션](#options) 섹션으로 이동하세요.


## 파일 기반 라우팅이란?

파일 기반 라우팅은 파일 시스템을 사용해 라우트를 구성하는 방식입니다. 코드로 라우트 구조를 정의하는 대신, 애플리케이션의 라우트 계층 구조를 나타내는 일련의 파일과 디렉토리를 통해 라우트를 정의할 수 있습니다. 이 방식은 여러 가지 장점을 제공합니다:

- **간결성**: 파일 기반 라우팅은 시각적으로 직관적이며, 초보 개발자와 경험 많은 개발자 모두 쉽게 이해할 수 있습니다.
- **구조화**: 라우트는 애플리케이션의 URL 구조를 반영하는 방식으로 조직됩니다.
- **확장성**: 애플리케이션이 커질수록 파일 기반 라우팅은 새로운 라우트를 추가하거나 기존 라우트를 유지보수하기 쉽게 만듭니다.
- **코드 분할**: 파일 기반 라우팅은 TanStack Router가 자동으로 라우트를 코드 분할하여 성능을 향상시킬 수 있게 합니다.
- **타입 안정성**: 파일 기반 라우팅은 라우트 간 타입 연결을 자동으로 생성하고 관리함으로써 타입 안정성을 높입니다. 이는 코드 기반 라우팅에서는 지루한 작업일 수 있습니다.
- **일관성**: 파일 기반 라우팅은 라우트 구조를 일관되게 유지하도록 강제합니다. 이는 애플리케이션을 유지보수하거나 다른 프로젝트로 이동할 때 더 쉽게 작업할 수 있게 해줍니다.


## `/`와 `.` 중 어떤 걸 쓸까?

디렉토리는 오랫동안 라우트 계층 구조를 표현하는 데 사용되어 왔습니다. 하지만 파일 기반 라우팅에서는 파일 이름에 `.` 문자를 사용하여 라우트 중첩을 나타내는 개념이 추가되었습니다. 이를 통해 깊게 중첩된 라우트를 위해 디렉토리를 만들지 않고도, 더 넓은 라우트 계층 구조를 위해 디렉토리를 계속 사용할 수 있습니다. 몇 가지 예제를 살펴보겠습니다!

```javascript
// 디렉토리 기반 라우트
/pages
  /about
    /team
      index.js  // /about/team
    index.js    // /about

// 파일 기반 라우트
/pages
  about.js      // /about
  about.team.js // /about/team
```

위 예제에서 볼 수 있듯이, 파일 기반 라우팅은 디렉토리 구조를 단순화하면서도 동일한 라우트 계층을 표현할 수 있습니다. 이 방식은 특히 깊게 중첩된 라우트를 다룰 때 유용합니다.


## 디렉토리 라우트

디렉토리를 사용하면 라우트 계층 구조를 나타낼 수 있습니다. 이는 여러 라우트를 논리적인 그룹으로 구성하고, 깊게 중첩된 라우트 그룹의 파일명 길이를 줄이는 데 유용합니다.

| 파일명                  | 라우트 경로                | 컴포넌트 출력                     |
| ----------------------- | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`          |                           | `<Root>`                          |
| ʦ `index.tsx`           | `/` (정확히 일치)          | `<Root><RootIndex>`               |
| ʦ `about.tsx`           | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`           | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`              |                           |                                   |
| ┄ ʦ `index.tsx`         | `/posts` (정확히 일치)     | `<Root><Posts><PostsIndex>`       |
| ┄ ʦ `$postId.tsx`       | `/posts/$postId`          | `<Root><Posts><Post>`             |
| 📂 `posts_`             |                           |                                   |
| ┄ 📂 `$postId`          |                           |                                   |
| ┄ ┄ ʦ `edit.tsx`        | `/posts/$postId/edit`     | `<Root><EditPost>`                |
| ʦ `settings.tsx`        | `/settings`               | `<Root><Settings>`                |
| 📂 `settings`           |                           | `<Root><Settings>`                |
| ┄ ʦ `profile.tsx`       | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ┄ ʦ `notifications.tsx` | `/settings/notifications` | `<Root><Settings><Notifications>` |
| ʦ `_layout.tsx`         |                           | `<Root><Layout>`                  |
| 📂 `_layout`            |                           |                                   |
| ┄ ʦ `layout-a.tsx`      | `/layout-a`               | `<Root><Layout><LayoutA>`         |
| ┄ ʦ `layout-b.tsx`      | `/layout-b`               | `<Root><Layout><LayoutB>`         |
| 📂 `files`              |                           |                                   |
| ┄ ʦ `$.tsx`             | `/files/$`                | `<Root><Files>`                   |

이 표는 디렉토리 구조와 라우트 경로, 그리고 해당 경로에서 렌더링되는 컴포넌트를 보여줍니다. 디렉토리를 사용하면 라우트를 더 체계적으로 관리할 수 있습니다.


## 플랫 라우팅(Flat Routing)

플랫 라우팅은 `.`을 사용하여 라우트의 중첩 수준을 표시할 수 있는 기능입니다. 이는 깊게 중첩된 라우트가 많고, 각각에 대해 디렉토리를 생성하는 것을 피하고 싶을 때 유용합니다.

| 파일명                          | 라우트 경로                | 컴포넌트 출력                     |
| ------------------------------- | ------------------------- | --------------------------------- |
| `__root.tsx`                    |                           | `<Root>`                          |
| `index.tsx`                     | `/` (정확히 일치)         | `<Root><RootIndex>`               |
| `about.tsx`                     | `/about`                  | `<Root><About>`                   |
| `posts.tsx`                     | `/posts`                  | `<Root><Posts>`                   |
| `posts.index.tsx`               | `/posts` (정확히 일치)    | `<Root><Posts><PostsIndex>`       |
| `posts.$postId.tsx`             | `/posts/$postId`          | `<Root><Posts><Post>`             |
| `posts_.$postId.edit.tsx`       | `/posts/$postId/edit`     | `<Root><EditPost>`                |
| `settings.tsx`                  | `/settings`               | `<Root><Settings>`                |
| `settings.profile.tsx`          | `/settings/profile`       | `<Root><Settings><Profile>`       |
| `settings.notifications.tsx`    | `/settings/notifications` | `<Root><Settings><Notifications>` |
| `_layout.tsx`                   |                           | `<Root><Layout>`                  |
| `_layout.layout-a.tsx`          | `/layout-a`               | `<Root><Layout><LayoutA>`         |
| `_layout.layout-b.tsx`          | `/layout-b`               | `<Root><Layout><LayoutB>`         |
| `files.$.tsx`                   | `/files/$`                | `<Root><Files>`                   |

이 표는 파일명과 해당 파일이 생성하는 라우트 경로, 그리고 렌더링되는 컴포넌트 구조를 보여줍니다. 예를 들어, `posts.$postId.tsx` 파일은 `/posts/$postId` 경로에 매핑되며, `<Root><Posts><Post>` 컴포넌트 구조를 렌더링합니다.


## 플랫(Flat)과 디렉토리(Directory) 라우트 혼합 사용

프로젝트에서 100% 디렉토리 구조나 플랫 구조만 사용하는 것이 항상 최적의 선택은 아닙니다. 그래서 TanStack Router는 두 방식을 혼합하여 사용할 수 있도록 지원합니다. 이렇게 하면 상황에 맞게 두 방식의 장점을 모두 활용할 수 있습니다.

| 파일명                         | 라우트 경로                | 컴포넌트 출력                     |
| ------------------------------ | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`                 |                           | `<Root>`                          |
| ʦ `index.tsx`                  | `/` (정확히 일치)          | `<Root><RootIndex>`               |
| ʦ `about.tsx`                  | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`                  | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`                     |                           |                                   |
| ┄ ʦ `index.tsx`                | `/posts` (정확히 일치)     | `<Root><Posts><PostsIndex>`       |
| ┄ ʦ `$postId.tsx`              | `/posts/$postId`          | `<Root><Posts><Post>`             |
| ┄ ʦ `$postId.edit.tsx`         | `/posts/$postId/edit`     | `<Root><Posts><Post><EditPost>`   |
| ʦ `settings.tsx`               | `/settings`               | `<Root><Settings>`                |
| ʦ `settings.profile.tsx`       | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ʦ `settings.notifications.tsx` | `/settings/notifications` | `<Root><Settings><Notifications>` |

플랫 라우트와 디렉토리 라우트를 혼합하여 사용하면, 상황에 맞게 두 방식의 장점을 모두 활용할 수 있는 라우트 트리를 구성할 수 있습니다.

> [!TIP]
> 라우트 파일의 위치를 커스터마이징하거나 라우트 탐색 방식을 완전히 재정의해야 한다면, [가상 파일 라우트(Virtual File Routes)](./virtual-file-routes.md)를 사용하여 프로그래밍 방식으로 라우트 트리를 구축할 수 있습니다. 이렇게 하면 파일 기반 라우팅의 장점을 그대로 유지하면서도 유연하게 라우트를 관리할 수 있습니다.


## 동적 경로 파라미터

동적 경로 파라미터는 플랫 라우트와 디렉토리 라우트 모두에서 사용할 수 있습니다. 이를 통해 URL 경로의 동적 세그먼트와 일치하는 라우트를 생성할 수 있습니다. 동적 경로 파라미터는 파일명에서 `$` 문자로 표시됩니다:

| 파일명                | 라우트 경로      | 컴포넌트 출력         |
| --------------------- | ---------------- | --------------------- |
| ...                   | ...              | ...                   |
| `posts.$postId.tsx`   | `/posts/$postId` | `<Root><Posts><Post>` |

동적 경로 파라미터에 대해 더 자세히 알고 싶다면 [경로 파라미터](./path-params.md) 가이드를 참고하세요.


## 경로 없는 라우트(Pathless Routes)

경로 없는 라우트는 URL 경로를 요구하지 않고 자식 라우트를 로직이나 컴포넌트로 감싸는 역할을 합니다. 경로 없는 라우트는 파일명에 `_` 문자를 사용하여 표시합니다:

| 파일명         | 라우트 경로 | 컴포넌트 출력         |
| -------------- | ----------- | --------------------- |
| ʦ `_app.tsx`   |             |                       |
| ʦ `_app.a.tsx` | /a          | `<Root><App><A>`      |
| ʦ `_app.b.tsx` | /b          | `<Root><App><B>`      |

경로 없는 라우트에 대해 더 알아보려면 [라우팅 개념 - 경로 없는 라우트](./routing-concepts.md#pathless-routes) 가이드를 참고하세요.


## 파일 이름 규칙

파일 기반 라우팅을 사용하려면 라우트가 올바르게 생성되도록 몇 가지 간단한 파일 이름 규칙을 따라야 합니다. 이러한 규칙이 가능하게 하는 개념은 [라우트 트리 & 중첩](./route-trees.md) 가이드에서 자세히 설명합니다.

> [!IMPORTANT]
> `/api`로 시작하는 라우트는 예약되어 있으며 파일 기반 라우팅에 사용할 수 없습니다. 이러한 라우트는 TanStack Start에서 API 라우트를 위해 미래에 사용할 예정입니다. 파일 기반 라우팅을 사용하면서 `/api`로 시작하는 라우트를 사용해야 한다면, `apiBase` 옵션을 다른 값으로 설정해야 합니다.

> **💡 기억하세요:** 프로젝트의 파일 이름 규칙은 `tsr.config.json`에 설정된 [옵션](#options)에 영향을 받을 수 있습니다. 기본적으로 `routeFileIgnorePrefix` 옵션은 `-`로 설정되어 있으며, `-`로 시작하는 파일과 디렉토리는 라우팅에서 제외됩니다.

- **`__root.tsx`**
  - 루트 라우트 파일은 반드시 `__root.tsx`로 이름을 지어야 하며, 설정된 `routesDirectory`의 루트에 위치해야 합니다.
- **`.` 구분자**
  - 라우트는 `.` 문자를 사용하여 중첩된 라우트를 나타낼 수 있습니다. 예를 들어, `blog.post`는 `blog`의 하위 라우트로 생성됩니다.
- **`$` 토큰**
  - `$` 토큰이 있는 라우트 세그먼트는 매개변수화되며, URL 경로에서 값을 추출하여 라우트 `param`으로 사용합니다.
- **`_` 접두사**
  - `_` 접두사가 있는 라우트 세그먼트는 레이아웃 라우트로 간주되며, URL 경로와 일치할 때 하위 라우트를 매칭하는 데 사용되지 않습니다.
- **`_` 접미사**
  - `_` 접미사가 있는 라우트 세그먼트는 어떤 부모 라우트 아래에도 중첩되지 않습니다.
- **`(folder)` 폴더 이름 패턴**
  - 이 패턴과 일치하는 폴더는 **라우트 그룹**으로 처리되며, 이 폴더는 라우트의 URL 경로에 포함되지 않습니다.
- **`index` 토큰**
  - `index` 토큰으로 끝나는 라우트 세그먼트(파일 타입 이전)는 URL 경로가 부모 라우트와 정확히 일치할 때 부모 라우트를 매칭하는 데 사용됩니다. 이는 `indexToken` 설정 옵션을 통해 구성할 수 있으며, [옵션](#options)을 참조하세요.
- **`.route.tsx` 파일 타입**
  - 디렉토리를 사용하여 라우트를 구성할 때, `route` 접미사를 사용하여 디렉토리 경로에 라우트 파일을 생성할 수 있습니다. 예를 들어, `blog.post.route.tsx` 또는 `blog/post/route.tsx`는 `/blog/post` 라우트의 라우트 파일로 사용될 수 있습니다. 이는 `routeToken` 설정 옵션을 통해 구성할 수 있으며, [옵션](#options)을 참조하세요.
- **`.lazy.tsx` 파일 타입**
  - `lazy` 접미사를 사용하여 라우트의 컴포넌트를 코드 분할할 수 있습니다. 예를 들어, `blog.post.lazy.tsx`는 `blog.post` 라우트의 컴포넌트로 사용됩니다.


## 설치

파일 기반 라우팅을 시작하려면 프로젝트의 번들러를 설정하여 TanStack Router 플러그인이나 TanStack Router CLI를 사용해야 합니다.

파일 기반 라우팅을 활성화하려면 React와 지원되는 번들러를 사용 중이어야 합니다. 현재 TanStack Router는 다음 번들러를 지원합니다:

- [Vite](#configuration-with-vite)
- [Rspack/Rsbuild](#configuration-with-rspackrsbuild)
- [Webpack](#configuration-with-webpack)
- [Esbuild](#configuration-with-esbuild)
- 기타??? (특정 번들러에 대한 지원을 원한다면 알려주세요)

지원되는 번들러 중 하나를 통해 TanStack Router의 파일 기반 라우팅을 사용할 때, 플러그인은 **번들러의 개발 및 빌드 프로세스를 통해 자동으로 라우트 설정을 생성**합니다. 이는 TanStack Router의 라우트 생성 기능을 사용하는 가장 쉬운 방법입니다.

아직 지원되지 않는 번들러를 사용 중이라면, Discord나 GitHub를 통해 문의해 주세요. 그동안 걱정하지 마세요! [`@tanstack/router-cli`](#configuration-with-the-tanstack-router-cli) 패키지를 사용하여 라우트 트리 파일을 생성할 수 있습니다.


### Vite로 설정하기

**Vite**에서 파일 기반 라우팅을 사용하려면 `@tanstack/router-plugin` 패키지를 설치해야 합니다.

```sh
npm install -D @tanstack/router-plugin
```

설치가 완료되면, Vite 설정에 플러그인을 추가합니다.

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import viteReact from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    TanStackRouterVite(),
    viteReact(),
    // ...
  ],
})
```

또는 [Quickstart Vite 예제](https://github.com/TanStack/router/tree/main/examples/react/quickstart-file-based)를 클론하여 시작할 수도 있습니다.

> [!WARNING]
> 이전 버전의 `@tanstack/router-vite-plugin` 패키지를 사용 중이라면, `@tanstack/router-plugin/vite` 패키지로 별칭이 지정되어 있으므로 계속 사용할 수 있습니다. 하지만 `@tanstack/router-plugin` 패키지를 직접 사용하는 것을 권장합니다.

이제 Vite 설정에 플러그인을 추가했으므로, TanStack Router를 사용하여 파일 기반 라우팅을 시작할 준비가 되었습니다.

생성된 라우트 트리 파일을 `ignore` 처리하는 것을 잊지 마세요. 자세한 내용은 [생성된 라우트 트리 파일 무시하기](#ignoring-the-generated-route-tree-file) 섹션을 참고하세요.


### Rspack/Rsbuild 설정하기

**Rspack** 또는 **Rsbuild**에서 파일 기반 라우팅을 사용하려면 `@tanstack/router-plugin` 패키지를 설치해야 합니다.

```sh
npm install -D @tanstack/router-plugin
```

설치가 완료되면, 플러그인을 설정 파일에 추가해야 합니다.

```tsx
// rsbuild.config.ts
import { defineConfig } from '@rsbuild/core'
import { pluginReact } from '@rsbuild/plugin-react'
import { TanStackRouterRspack } from '@tanstack/router-plugin/rspack'

export default defineConfig({
  plugins: [pluginReact()],
  tools: {
    rspack: {
      plugins: [TanStackRouterRspack()],
    },
  },
})
```

또는 [Quickstart Rspack/Rsbuild 예제](https://github.com/TanStack/router/tree/main/examples/react/quickstart-rspack-file-based)를 클론하여 시작할 수도 있습니다.

이제 Rspack/Rsbuild 설정에 플러그인을 추가했으므로, TanStack Router를 사용하여 파일 기반 라우팅을 시작할 준비가 되었습니다.

생성된 라우트 트리 파일을 `ignore` 처리하는 것을 잊지 마세요. 자세한 내용은 [생성된 라우트 트리 파일 무시하기](#ignoring-the-generated-route-tree-file) 섹션을 참고하세요.


### Webpack으로 설정하기

**Webpack**에서 파일 기반 라우팅을 사용하려면 `@tanstack/router-plugin` 패키지를 설치해야 합니다.

```sh
npm install -D @tanstack/router-plugin
```

설치가 완료되면, 플러그인을 설정 파일에 추가합니다.

```tsx
// webpack.config.ts
import { TanStackRouterWebpack } from '@tanstack/router-plugin/webpack'

export default {
  plugins: [TanStackRouterWebpack()],
}
```

또는 [Quickstart Webpack 예제](https://github.com/TanStack/router/tree/main/examples/react/quickstart-webpack-file-based)를 클론하여 시작할 수도 있습니다.

이제 Webpack 설정에 플러그인을 추가했으므로, TanStack Router와 함께 파일 기반 라우팅을 사용할 준비가 되었습니다.

생성된 라우트 트리 파일을 `ignore` 처리하는 것을 잊지 마세요. 자세한 내용은 [생성된 라우트 트리 파일 무시하기](#ignoring-the-generated-route-tree-file) 섹션을 참고하세요.


### Esbuild 설정하기

**Esbuild**에서 파일 기반 라우팅을 사용하려면 `@tanstack/router-plugin` 패키지를 설치해야 합니다.

```sh
npm install -D @tanstack/router-plugin
```

설치가 완료되면, 플러그인을 설정 파일에 추가합니다.

```tsx
// esbuild.config.js
import { TanStackRouterEsbuild } from '@tanstack/router-plugin/esbuild'

export default {
  // ...
  plugins: [TanStackRouterEsbuild()],
}
```

또는 [Quickstart Esbuild 예제](https://github.com/TanStack/router/tree/main/examples/react/quickstart-esbuild-file-based)를 클론하여 시작할 수도 있습니다.

이제 Esbuild 설정에 플러그인을 추가했으므로, TanStack Router와 함께 파일 기반 라우팅을 사용할 준비가 되었습니다.

생성된 라우트 트리 파일을 `ignore` 처리하는 것을 잊지 마세요. 자세한 내용은 [생성된 라우트 트리 파일 무시하기](#ignoring-the-generated-route-tree-file) 섹션을 참고하세요.


### TanStack Router CLI로 설정하기

파일 기반 라우팅을 사용하려면 `@tanstack/router-cli` 패키지를 설치해야 합니다.

```sh
npm install -D @tanstack/router-cli
```

설치가 완료되면, CLI가 파일을 `watch`하고 `generate`할 수 있도록 `package.json`의 스크립트를 수정해야 합니다.

```json
{
  "scripts": {
    "generate-routes": "tsr generate",
    "watch-routes": "tsr watch",
    "build": "npm run generate-routes && ...",
    "dev": "npm run watch-routes && ..."
  }
}
```

생성된 라우트 트리 파일을 `ignore` 처리하는 것을 잊지 마세요. 자세한 내용은 [생성된 라우트 트리 파일 무시하기](#ignoring-the-generated-route-tree-file) 섹션을 참고하세요.

CLI가 설치되면, `tsr` 커맨드를 통해 다음과 같은 명령어를 사용할 수 있습니다.


#### `generate` 커맨드 사용하기

제공된 설정을 기반으로 프로젝트의 라우트를 생성합니다.

```sh
tsr generate
```


#### `watch` 커맨드 사용하기

지정된 디렉토리를 지속적으로 감시하고 필요할 때마다 라우트를 다시 생성합니다.

**사용법:**

```sh
tsr watch
```

파일 기반 라우팅이 활성화된 상태에서 개발 모드로 애플리케이션을 시작하면, TanStack Router는 설정된 `routesDirectory`를 감시합니다. 파일이 추가, 삭제 또는 변경될 때마다 라우트 트리를 자동으로 생성합니다.


### 테스트 중에 TanStack Router 플러그인 비활성화하기

> ⚠️ 참고: vitest를 통해 테스트를 실행할 때 플러그인을 비활성화하려면, 현재 `NODE_ENV`를 기반으로 조건부로 추가할 수 있습니다:

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import viteReact from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-vite-plugin'

// vitest는 테스트 실행 시 자동으로 NODE_ENV를 'test'로 설정합니다
const isTest = process.env.NODE_ENV === 'test'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    !isTest && TanStackRouterVite(),
    viteReact(),
    // ...
  ],
})
```


### 생성된 라우트 트리 파일 무시하기

여러분의 프로젝트가 린터(linter)나 포매터(formatter)를 사용하도록 설정되어 있다면, 생성된 라우트 트리 파일을 무시하고 싶을 수 있습니다. 이 파일은 TanStack Router에 의해 관리되므로, 린터나 포매터가 변경해서는 안 됩니다.

생성된 라우트 트리 파일을 무시하는 데 도움이 될 만한 자료는 다음과 같습니다:

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

VSCode를 사용 중이라면, `.vscode/settings.json` 파일에 다음 내용을 추가하여 생성된 라우트 트리 파일을 편집기의 파일 감시에서 제외할 수 있습니다:

```json
{
  "files.watcherExclude": {
    "**/routeTree.gen.ts": true
  }
}
```


## 설정

파일 기반 라우팅은 대부분의 프로젝트에 적합한 기본 설정을 제공합니다:

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

이 기본 설정이 여러분의 프로젝트에 적합하다면, 별도의 설정 없이 바로 사용할 수 있습니다. 하지만 설정을 커스텀해야 한다면, 프로젝트 루트 디렉토리에 `tsr.config.json` 파일을 생성하여 설정을 변경할 수 있습니다.

- `tsr.config.json` 파일을 프로젝트 루트 디렉토리에 위치시킵니다.


### 설정 옵션

`tsr.config.json` 파일을 통해 다음과 같은 옵션을 설정할 수 있습니다.

> [!IMPORTANT]
> [파일 명명 규칙](#file-naming-conventions) 섹션에서 사용된 토큰과 일치하도록 `routeFilePrefix`, `routeFileIgnorePrefix`, `routeFileIgnorePattern` 옵션을 설정하지 마세요.

- **`routeFilePrefix`**
  - (선택 사항) 이 문자열로 시작하는 라우트 파일과 디렉토리만 라우팅에 포함됩니다.
  
- **`routeFileIgnorePrefix`**
  - (선택 사항, **기본값: `-`**) 이 문자열로 시작하는 라우트 파일과 디렉토리는 무시됩니다. 기본값은 `-`로 설정되어 있으며, 라우트 파일이 아닌 관련 파일을 포함하는 디렉토리를 사용할 수 있도록 합니다.
  
- **`routeFileIgnorePattern`**
  - (선택 사항) 라우트 디렉토리에서 특정 파일과 디렉토리를 무시합니다. 정규 표현식 형식으로 사용할 수 있습니다. 예를 들어, `.((css|const).ts)|test-page`는 `.css.ts`, `.const.ts`, `test-page`가 포함된 파일/디렉토리를 무시합니다.
  
- **`indexToken`**
  - (선택 사항, **기본값: `'index'`**) `index` 토큰을 [파일 명명 규칙](#file-naming-conventions)에 맞게 커스터마이징할 수 있습니다.
  
- **`routeToken`**
  - (선택 사항, **기본값: `'route'`**) `route` 토큰을 [파일 명명 규칙](#file-naming-conventions)에 맞게 커스터마이징할 수 있습니다.
  
- **`routesDirectory`**
  - (필수) 현재 작업 디렉토리(cwd)를 기준으로 라우트가 포함된 디렉토리를 지정합니다.
  
- **`generatedRouteTree`**
  - (필수) 생성된 라우트 트리가 저장될 파일의 경로를 현재 작업 디렉토리(cwd)를 기준으로 지정합니다.
  
- **`autoCodeSplitting`**
  - (선택 사항, **기본값: `false`**)
  - `true`로 설정하면 모든 비중요 라우트 설정 항목이 자동으로 코드 분할됩니다.
  - [자동 코드 분할 사용하기](./code-splitting.md#using-automatic-code-splitting) 가이드를 참고하세요.
  
- **`quoteStyle`**
  - (선택 사항, **기본값: `single`**) 생성된 라우트 트리 파일에서 `single` 또는 `double` 따옴표를 사용할지 지정합니다.
  
- **`semicolons`**
  - (선택 사항, **기본값: `false`**) 생성된 라우트 트리 파일에서 세미콜론을 사용할지 지정합니다.
  
- **`apiBase`**
  - (선택 사항) API 라우트의 기본 경로를 지정합니다. 기본값은 `/api`입니다.
  - 이 옵션은 TanStack Start에서 API 라우트를 위해 향후 사용될 예정입니다.
  
- **`disableTypes`**
  - (선택 사항, **기본값: `false`**) 라우트 트리에 대한 타입 생성을 비활성화합니다.
  - `true`로 설정하면 생성된 라우트 트리에 타입이 포함되지 않습니다.
  - `true`로 설정하고 `generatedRouteTree` 파일이 `.ts` 또는 `.tsx`로 끝나는 경우, 생성된 라우트 트리는 `.js` 파일로 작성됩니다.
  
- **`addExtensions`**
  - (선택 사항, **기본값: `false`**) 생성된 라우트 트리에서 라우트 이름에 파일 확장자를 추가합니다.
  
- **`disableLogging`**
  - (선택 사항, **기본값: `false`**) 라우트 생성 과정에서 로깅을 비활성화합니다.
  
- **`routeTreeFileHeader`**
  - (선택 사항) 생성된 라우트 트리 파일 내용 앞에 추가할 문자열 배열을 지정합니다.
  - 기본값:
    ```json
    [
      '/* eslint-disable */',
      '// @ts-nocheck',
      '// noinspection JSUnusedGlobalSymbols'
    ]
    ```
  
- **`routeTreeFileFooter`**
  - (선택 사항) 생성된 라우트 트리 파일 내용 뒤에 추가할 문자열 배열을 지정합니다.
  - 기본값: `[]`
  
- **`disableManifestGeneration`**
  - (선택 사항, **기본값: `false`**) 라우트 트리 매니페스트 생성을 비활성화합니다.

## 라우트 포함/제외

`routeFilePrefix`와 `routeFileIgnorePrefix` 옵션을 통해 CLI를 설정하여 특정 접두사로 시작하는 파일과 디렉토리만 포함하거나, 특정 접두사로 시작하는 파일과 디렉토리를 무시할 수 있습니다. 이는 라우트 파일과 비라우트 파일을 같은 디렉토리에 혼합하여 사용하거나, 평면 구조를 사용하면서 특정 파일을 라우팅에서 제외하려는 경우에 특히 유용합니다.

### 라우트 포함 예제

`~`로 시작하는 파일과 디렉토리만 라우팅에 포함하려면 다음과 같이 설정할 수 있습니다.

> 🧠 이 옵션을 사용할 때는 일반적으로 `~` 접두사를 권장합니다. 이 기호는 유닉스 기반 시스템에서 홈 폴더 탐색과 관련이 있을 뿐만 아니라, 파일명과 URL에서 유효한 문자로 사용되며, 라우트를 시각적으로 쉽게 구분할 수 있도록 디렉토리 상단에 위치시킵니다.

```json
{
  "routeFilePrefix": "~",
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts"
}
```

이 설정을 사용하면 `Posts.tsx`, `Post.tsx`, `PostEditor.tsx` 파일은 라우트 생성 과정에서 무시됩니다.

```
~__root.tsx
~posts.tsx
~posts
  ~index.tsx
  ~$postId.tsx
  ~$postId
    ~edit.tsx
    PostEditor.tsx
  Post.tsx
Posts.tsx
```

또한 라우트 파일이 아닌 관련 파일을 포함하는 디렉토리를 사용하는 경우도 일반적입니다.

```
~__root.tsx
~posts.tsx
~posts
  ~index.tsx
  ~$postId.tsx
  ~$postId
    ~edit.tsx
    components
      PostEditor.tsx
  components
    Post.tsx
components
  Posts.tsx
utils
  Posts.tsx
```

### 라우트 제외 예제

`-`로 시작하는 파일과 디렉토리를 라우팅에서 제외하려면 다음과 같이 설정할 수 있습니다.

> 🧠 이 옵션을 사용할 때는 일반적으로 `-` 접두사를 권장합니다. 마이너스 기호는 일반적으로 제거 또는 제외와 관련이 있습니다.

```json
{
  "routeFileIgnorePrefix": "-",
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts"
}
```

이 설정을 사용하면 `Posts.tsx`, `Post.tsx`, `PostEditor.tsx` 파일은 라우트 생성 과정에서 무시됩니다.

```
__root.tsx
posts.tsx
posts
  index.tsx
  $postId.tsx
  $postId
    edit.tsx
    -PostEditor.tsx
  -Post.tsx
-Posts.tsx
```

또한 라우트 파일이 아닌 관련 파일을 포함하는 디렉토리를 무시하는 경우도 일반적입니다.

```
__root.tsx
posts.tsx
posts
  index.tsx
  $postId.tsx
  $postId
    edit.tsx
    -components
      PostEditor.tsx
  -components
    Post.tsx
-components
  Posts.tsx
-utils
  Posts.tsx
```


