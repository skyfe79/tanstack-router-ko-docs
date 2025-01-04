# Table of Contents

- [라우팅 개념](#라우팅-개념)
  - [루트 라우트(Root Route)](#루트-라우트root-route)
  - [라우트의 구조](#라우트의-구조)
    - [`createFileRoute`의 경로 인자](#createfileroute의-경로-인자)
  - [정적 라우트(Static Routes)](#정적-라우트static-routes)
  - [인덱스 라우트(Index Routes)](#인덱스-라우트index-routes)
  - [동적 라우트 세그먼트](#동적-라우트-세그먼트)
  - [Splat / Catch-All Routes (스플랫/캐치올 라우트)](#splat--catch-all-routes-스플랫캐치올-라우트)
  - [경로 없는 라우트 (Pathless Routes)](#경로-없는-라우트-pathless-routes)
  - [중첩되지 않은 라우트(Non-Nested Routes)](#중첩되지-않은-라우트non-nested-routes)
  - [404 / `NotFoundRoute`](#404--notfoundroute)
  - [경로 없는 라우트 그룹 디렉터리](#경로-없는-라우트-그룹-디렉터리)

# 라우팅 개념

TanStack Router는 복잡하고 동적인 라우팅 시스템을 쉽게 구축할 수 있도록 다양한 강력한 라우팅 개념을 지원합니다.

- [루트 라우트](./routing-concepts.md#the-root-route)
- [정적 라우트](./routing-concepts.md#static-routes)
- [인덱스 라우트](./routing-concepts.md#index-routes)
- [동적 라우트 세그먼트](./routing-concepts.md#dynamic-route-segments)
- [스플랫 / 캐치-올 라우트](./routing-concepts.md#splat--catch-all-routes)
- [경로 없는 라우트](./routing-concepts.md#pathless-routes)
- [중첩되지 않은 라우트](./routing-concepts.md#non-nested-routes)
- [404 / Not Found 라우트](./routing-concepts.md#404--notfoundroutes)

이러한 각 개념은 유용하고 강력하며, 다음 섹션에서 각각에 대해 자세히 알아보겠습니다.


## 루트 라우트(Root Route)

루트 라우트는 전체 트리 구조에서 가장 상위에 위치한 라우트입니다. 모든 다른 라우트를 자식으로 포함합니다.

- 경로(path)가 없음
- 항상 매칭됨
- `컴포넌트(component)`가 항상 렌더링됨

루트 라우트는 경로가 없더라도 다른 라우트와 동일한 기능을 모두 사용할 수 있습니다. 예를 들어:

- 컴포넌트
- 로더(loader)
- 검색 파라미터 유효성 검사
- 기타 기능

루트 라우트를 생성하려면 `createRootRoute()` 생성자를 호출하고, 라우트 파일에서 `Route` 변수로 내보내면 됩니다.

```tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute()
```

> 🧠 `createRootRouteWithContext<TContext>()` 함수를 사용해 루트 라우트를 생성할 수도 있습니다. 이 방법은 전체 라우터에 대해 타입 안전한 방식으로 의존성 주입을 수행합니다. 자세한 내용은 [컨텍스트 섹션](./router-context.md)에서 확인할 수 있습니다.


## 라우트의 구조

루트 라우트를 제외한 모든 라우트는 `createFileRoute` 함수를 사용하여 설정합니다. 이 함수는 파일 기반 라우팅을 사용할 때 타입 안전성을 제공합니다:

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})
```

위 코드에서 `createFileRoute` 함수는 `/posts` 경로에 대한 라우트를 생성하고, 해당 라우트가 렌더링할 컴포넌트로 `PostsComponent`를 지정합니다. 이렇게 설정하면 라우트와 컴포넌트 간의 연결이 타입 안전하게 이루어집니다.


### `createFileRoute`의 경로 인자

`createFileRoute` 함수는 단일 인자를 받습니다. 이 인자는 파일 라우트의 경로를 문자열로 전달합니다.

**❓❓❓ "잠깐만, 라우트 파일의 경로를 `createFileRoute`에 직접 전달해야 한다고?"**

맞습니다! 하지만 걱정하지 마세요. 이 경로는 **TanStack Router 플러그인이나 Router CLI를 통해 자동으로 작성되고 관리됩니다.** 따라서 여러분이 새로운 라우트를 만들거나, 라우트를 이동하거나, 이름을 변경할 때마다 경로가 자동으로 업데이트됩니다.

> 🧠 이 경로명이 필요한 이유는 TanStack Router의 마법 같은 타입 안전성과 관련이 있습니다. 이 경로명이 없다면, TypeScript는 우리가 어떤 파일에 있는지 전혀 알 수 없습니다! (TypeScript에 이런 기능이 내장되길 바라지만, 아직은 없습니다 🤷‍♂️)


## 정적 라우트(Static Routes)

정적 라우트는 특정 경로와 정확히 일치합니다. 예를 들어 `/about`, `/settings`, `/settings/notifications`와 같은 경로는 모두 정적 라우트입니다. 이들은 경로와 정확히 일치하기 때문입니다.

`/about` 라우트를 살펴보겠습니다:

```tsx
// about.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutComponent,
})

function AboutComponent() {
  return About
}
```

정적 라우트는 간단하고 직관적입니다. 경로와 정확히 일치하며, 제공된 컴포넌트를 렌더링합니다.


## 인덱스 라우트(Index Routes)

인덱스 라우트는 **부모 라우트가 정확히 매칭되고, 자식 라우트가 매칭되지 않을 때** 특별히 해당 부모 라우트를 대상으로 합니다.

`/posts` URL에 대한 인덱스 라우트 예제를 살펴보겠습니다:

```tsx
// posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

// 인덱스 라우트를 대상으로 하기 위해 슬래시(/)를 사용
export const Route = createFileRoute('/posts/')({
  component: PostsIndexComponent,
})

function PostsIndexComponent() {
  return 게시물을 선택해 주세요!
}
```

이 라우트는 URL이 정확히 `/posts`일 때 매칭됩니다.


## 동적 라우트 세그먼트

라우트 경로 세그먼트 중 `$`로 시작하고 뒤에 라벨이 붙는 부분은 동적 세그먼트로, URL의 해당 부분을 캡처하여 애플리케이션에서 사용할 수 있는 `params` 객체로 변환합니다. 예를 들어, `/posts/123`이라는 경로는 `/posts/$postId` 라우트와 매치되며, `params` 객체는 `{ postId: '123' }`이 됩니다.

이렇게 캡처된 파라미터는 라우트 설정과 컴포넌트에서 사용할 수 있습니다. `posts.$postId.tsx` 라우트를 살펴보겠습니다:

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // 로더에서 사용
  loader: ({ params }) => fetchPost(params.postId),
  // 컴포넌트에서 사용
  component: PostComponent,
})

function PostComponent() {
  // 컴포넌트 내부에서 사용!
  const { postId } = Route.useParams()
  return Post ID: {postId}
}
```

> 🧠 동적 세그먼트는 경로의 **각** 세그먼트에서 동작합니다. 예를 들어, `/posts/$postId/$revisionId`와 같은 경로를 가진 라우트를 만들 수 있으며, 각 `$` 세그먼트는 `params` 객체에 캡처됩니다.


## Splat / Catch-All Routes (스플랫/캐치올 라우트)

경로가 `$`로만 구성된 라우트를 "스플랫" 라우트라고 부릅니다. 이 라우트는 `$`부터 URL 경로의 끝까지 **항상** 나머지 부분을 캡처합니다. 캡처된 경로는 `params` 객체의 특수한 `_splat` 속성 아래에 저장됩니다.

예를 들어, `files/$` 경로를 대상으로 하는 라우트는 스플랫 라우트입니다. 만약 URL 경로가 `/files/documents/hello-world`라면, `params` 객체는 특수한 `_splat` 속성 아래에 `documents/hello-world`를 포함하게 됩니다:

```js
{
  '_splat': 'documents/hello-world'
}
```

> ⚠️ 라우터 v1에서는 스플랫 라우트가 `_splat` 키 대신 `*`로 표시되기도 합니다. 이는 이전 버전과의 호환성을 위한 것이며, v2에서는 제거될 예정입니다.

> 🧠 왜 `$`를 사용할까요? Remix와 같은 도구 덕분에, 와일드카드를 나타내는 데 가장 흔히 사용되는 `*`가 파일 이름이나 CLI 도구와 잘 맞지 않는다는 것을 알게 되었습니다. 그래서 Remix와 마찬가지로 `$`를 사용하기로 결정했습니다.


## 경로 없는 라우트 (Pathless Routes)

밑줄(`_`)로 시작하는 라우트는 "경로 없는 라우트"로 간주됩니다. 이 라우트는 URL에서 일치하는 `path`를 요구하지 않고도 자식 라우트를 추가 컴포넌트와 로직으로 감싸는 데 사용됩니다. 경로 없는 라우트를 사용하면 다음과 같은 작업을 할 수 있습니다:

- 자식 라우트를 레이아웃 컴포넌트로 감싸기
- 자식 라우트를 표시하기 전에 `loader` 요구사항을 강제하기
- 자식 라우트에 검색 파라미터를 검증하고 제공하기
- 자식 라우트에 오류 컴포넌트나 대기 중인 엘리먼트를 위한 대체 제공하기
- 모든 자식 라우트에 공유 컨텍스트 제공하기

> 🧠 `_` 접두사 이후의 경로 부분은 라우트의 ID로 사용됩니다. 모든 라우트는 고유하게 식별 가능해야 하며, 특히 TypeScript를 사용할 때 타입 오류를 방지하고 자동 완성을 효과적으로 수행하기 위해 이 ID가 필요합니다.

다음은 `_pathless.tsx`라는 예제 라우트를 살펴보겠습니다:

```
routes/
├── _pathless.tsx
├── _pathless.a.tsx
├── _pathless.b.tsx
```

위 트리에서 `_pathless.tsx`는 두 개의 자식 라우트인 `_pathless.a.tsx`와 `_pathless.b.tsx`를 감싸는 경로 없는 라우트입니다. `_pathless.tsx` 라우트는 자식 라우트를 레이아웃 컴포넌트로 감싸는 데 사용됩니다:

```tsx
import { Outlet, createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/_pathless')({
  component: LayoutComponent,
})

function LayoutComponent() {
  return (
    <div>
      Layout
      <Outlet />
    </div>
  )
}
```

다음 표는 URL에 따라 어떤 컴포넌트가 렌더링되는지 보여줍니다:

| URL 경로 | 컴포넌트          |
| -------- | ----------------- |
| `/`      | `<Index>`         |
| `/a`     | `<Layout><A>`     |
| `/b`     | `<Layout><B>`     |


## 중첩되지 않은 라우트(Non-Nested Routes)

중첩되지 않은 라우트는 부모 파일 라우트 세그먼트에 `_`를 붙여 생성할 수 있습니다. 이를 통해 라우트를 부모로부터 **분리**하고 독자적인 컴포넌트 트리를 렌더링할 수 있습니다.

다음은 평면적인 라우트 트리 예제입니다:

```
routes/
├── posts.tsx
├── posts.$postId.tsx
├── posts_.$postId.edit.tsx
```

아래 표는 URL에 따라 어떤 컴포넌트가 렌더링되는지 보여줍니다:

| URL 경로          | 렌더링되는 컴포넌트          |
| ----------------- | ---------------------------- |
| `/posts`          | `<Posts>`                    |
| `/posts/123`      | `<Posts><Post postId="123">` |
| `/posts/123/edit` | `<PostEditor postId="123">`  |

- `posts.$postId.tsx` 라우트는 일반적으로 `posts.tsx` 라우트 아래에 중첩되며, `<Posts><Post>`를 렌더링합니다.
- `posts_.$postId.edit.tsx` 라우트는 다른 라우트와 동일한 `posts` 접두사를 **공유하지 않습니다**. 따라서 최상위 라우트로 취급되어 `<PostEditor>`를 렌더링합니다.


## 404 / `NotFoundRoute`

404 또는 Not-Found 라우트는 라우트 트리의 명시적인 부분은 아니지만, 유용한 추상화 개념입니다.

물론, 모든 라우트 브랜치 아래에 스플랫(splat) 또는 캐치-올(catch-all) 라우트를 일일이 추가할 수도 있습니다. 하지만 작은 규모에서도 이 방법은 번거롭고 오류가 발생하기 쉽습니다. 대신, 특별한 `NotFoundRoute`를 생성하고 라우터의 `notFoundRoute` 옵션에 제공할 수 있습니다.

> ⚠️ 절대 `NotFoundRoute`를 라우트 트리에 포함시키지 마세요. 이렇게 하면 라우트 트리의 모든 브랜치에서 작동하지 않습니다.

`NotFoundRoute`는 다음과 같은 경우에 렌더링됩니다:

- URL에 모든 가능한 라우트 매치를 초과하는 경로 세그먼트가 있을 때
- 초과된 경로 세그먼트를 캡처할 동적 세그먼트나 스플랫 라우트가 없을 때
- 부모 라우트가 매치되었을 때 렌더링할 인덱스 라우트가 없을 때
- 라우터에 `notFoundRoute`가 제공되었을 때

`NotFoundRoute`는 다음과 같은 특별한 버전의 `Route`입니다:

- `path`가 없음
- `id`가 없음
- 경로 파라미터를 파싱하거나 검증할 수 없음

그러나 여전히 다음과 같은 기능을 가지고 있습니다:

- `component`, `pendingComponent`, `errorComponent`를 렌더링할 수 있음
- `search` 파라미터를 검증하고 받을 수 있음
- `loader`와 `beforeLoad` 훅을 구성할 수 있음
- 루트 라우트로부터 `data`와 검색 파라미터를 받을 수 있음

`NotFoundRoute`를 구성하는 방법은 [라우트 매칭 - Not-Found 라우트](./route-matching.md#not-found-routes) 가이드에서 다룰 예정입니다.


## 경로 없는 라우트 그룹 디렉터리

경로 없는 라우트 그룹 디렉터리는 `()`를 사용하여 경로와 상관없이 라우트 파일을 그룹화하는 방법입니다. 이는 순전히 조직적인 목적으로 사용되며, 라우트 트리나 컴포넌트 트리에 어떤 영향도 미치지 않습니다.

```
routes/
├── index.tsx
├── (app)/
│   ├── dashboard.tsx
│   ├── settings.tsx
│   ├── users.tsx
├── (auth)/
│   ├── login.tsx
│   ├── register.tsx
```

위 예제에서 `app`과 `auth` 디렉터리는 순전히 조직적인 목적으로 사용되며, 라우트 트리나 컴포넌트 트리에 어떤 영향도 미치지 않습니다. 이들은 관련된 라우트를 그룹화하여 탐색과 조직을 더 쉽게 만드는 데 사용됩니다.

아래 표는 URL에 따라 어떤 컴포넌트가 렌더링되는지 보여줍니다:

| URL 경로     | 컴포넌트     |
| ------------ | ------------- |
| `/`          | `<Index>`     |
| `/dashboard` | `<Dashboard>` |
| `/settings`  | `<Settings>`  |
| `/users`     | `<Users>`     |
| `/login`     | `<Login>`     |
| `/register`  | `<Register>`  |

보시다시피, `app`과 `auth` 디렉터리는 순전히 조직적인 목적으로 사용되며, 라우트 트리나 컴포넌트 트리에 어떤 영향도 미치지 않습니다.


