# Path Params (경로 매개변수)

Path params(경로 매개변수)는 단일 세그먼트(다음 `/`까지의 텍스트)를 매칭하고, 그 값을 **이름이 지정된** 변수로 제공합니다. 경로에서 `$` 문자를 접두사로 사용하고, 그 뒤에 할당할 키 변수를 붙여 정의합니다. 다음은 유효한 경로 매개변수 경로 예제입니다:

- `$postId`
- `$name`
- `$teamId`
- `about/$name`
- `team/$teamId`
- `blog/$postId`

경로 매개변수 라우트는 다음 `/`까지만 매칭되기 때문에, 계층 구조를 계속 표현하기 위해 하위 라우트를 생성할 수 있습니다.

이제 게시물 ID를 매칭하기 위해 경로 매개변수를 사용하는 게시물 라우트 파일을 만들어 보겠습니다:

- `posts.$postId.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

이 코드는 `posts/$postId` 경로에 대한 라우트를 생성하고, `loader` 함수를 통해 `postId` 매개변수를 사용하여 게시물 데이터를 가져옵니다.


## 경로 파라미터는 자식 라우트에서도 사용 가능

경로 파라미터가 파싱되면, 모든 자식 라우트에서 사용할 수 있습니다. 즉, `postRoute`에 자식 라우트를 정의할 때, URL의 `postId` 변수를 자식 라우트의 경로에서 사용할 수 있습니다!

예를 들어:

```javascript
const postRoute = createRoute({
  path: 'posts/:postId',
  component: PostComponent,
});

const commentRoute = createRoute({
  path: 'comments',
  component: CommentComponent,
  parent: postRoute,
});
```

위 예제에서 `commentRoute`는 `postId` 파라미터에 접근할 수 있습니다. 이를 통해 특정 게시물의 댓글을 표시하는 등의 작업을 수행할 수 있습니다.


## 라우트 경로 파라미터(Path Params)를 로더에서 사용하기

라우트 경로 파라미터는 `params` 객체 형태로 로더에 전달됩니다. 이 객체의 키는 경로 파라미터 이름이고, 값은 실제 URL 경로에서 파싱된 값입니다. 예를 들어, `/blog/123` URL에 접근하면 `params` 객체는 `{ postId: '123' }`이 됩니다.

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

`params` 객체는 `beforeLoad` 옵션에도 전달됩니다.

```tsx
export const Route = createFileRoute('/posts/$postId')({
  beforeLoad: async ({ params }) => {
    // params.postId를 사용하여 작업 수행
  },
})
```


## 컴포넌트에서 경로 파라미터 사용하기

`postRoute`에 컴포넌트를 추가하면, 라우트의 `useParams` 훅을 사용해 URL에서 `postId` 변수에 접근할 수 있습니다.

```tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostComponent,
})

function PostComponent() {
  const { postId } = Route.useParams()
  return <div>Post {postId}</div>
}
```

> 🧠 빠른 팁: 컴포넌트가 코드 분할(code-split)된 경우, [getRouteApi 함수](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)를 사용해 `Route` 설정을 임포트하지 않고도 타입이 지정된 `useParams()` 훅에 접근할 수 있습니다.


## 라우트 외부에서 Path Params 사용하기

여러분은 앱 내의 어떤 컴포넌트에서든 전역으로 내보내진 `useParams` 훅을 사용하여 파싱된 경로 파라미터에 접근할 수 있습니다. 이때 `useParams`에 `strict: false` 옵션을 전달해야 합니다. 이 옵션은 모호한 위치에서 파라미터에 접근하고자 함을 나타냅니다.

```tsx
function PostComponent() {
  const { postId } = useParams({ strict: false })
  return Post {postId}
}
```


## 경로 파라미터를 사용한 네비게이션

경로 파라미터가 있는 라우트로 이동할 때, TypeScript는 파라미터를 객체로 전달하거나 객체를 반환하는 함수로 전달하도록 요구합니다.

먼저 객체 스타일을 살펴보겠습니다:

```tsx
function Component() {
  return (
    <Link to="/posts/:postId" params={{ postId: '123' }}>
      Post 123
    </Link>
  )
}
```

다음은 함수 스타일의 예제입니다:

```tsx
function Component() {
  return (
    <Link to="/posts/:postId" params={(prev) => ({ ...prev, postId: '123' })}>
      Post 123
    </Link>
  )
}
```

함수 스타일은 URL에 이미 존재하는 다른 라우트의 파라미터를 유지해야 할 때 유용합니다. 함수 스타일은 현재 파라미터를 인자로 받아 필요한 수정을 가한 후 최종 파라미터 객체를 반환할 수 있기 때문입니다.


## 허용되는 문자

기본적으로 경로 매개변수는 `encodeURIComponent`를 사용하여 이스케이프 처리됩니다. 만약 `@`나 `+`와 같은 다른 유효한 URI 문자를 허용하고 싶다면, [RouterOptions](../api/router/RouterOptionsType.md#pathparamsallowedcharacters-property)에서 이를 지정할 수 있습니다.

예제 사용법:

```tsx
const router = createRouter({
  ...
  pathParamsAllowedCharacters: ['@']
})
```

다음은 허용되는 문자 목록입니다:
`;` `:` `@` `&` `=` `+` `$` `,`


