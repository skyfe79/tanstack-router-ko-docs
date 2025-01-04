# 타입 안전성

TanStack Router는 TypeScript 컴파일러와 런타임의 한계 내에서 최대한 타입 안전성을 보장하도록 설계되었습니다. 이는 단순히 TypeScript로 작성되었다는 것을 넘어서, **제공된 타입을 완전히 추론하고 이를 라우팅 전반에 걸쳐 철저히 전달한다**는 의미입니다.

결과적으로, 여러분은 **개발자로서 더 적은 타입을 작성**하게 되고, 코드가 발전함에 따라 **더 큰 확신을 가질 수 있습니다**.


## Route Definitions





### 파일 기반 라우팅

라우트는 계층적 구조를 가지며, 이는 라우트 정의에서도 동일하게 적용됩니다. 파일 기반 라우팅을 사용하면 타입 안전성(type-safety)의 많은 부분이 자동으로 처리됩니다.


### 코드 기반 라우팅

`Route` 클래스를 직접 사용하는 경우, `Route`의 `getParentRoute` 옵션을 통해 라우트 타입을 올바르게 지정하는 방법을 알아야 합니다. 자식 라우트는 **모든** 부모 라우트의 타입을 인식해야 하기 때문입니다. 이를 하지 않으면, 레이아웃 라우트에서 3단계 위에서 파싱한 중요한 검색 파라미터가 JS의 공허 속으로 사라질 수 있습니다.

따라서, 자식 라우트에 부모 라우트를 전달하는 것을 잊지 마세요!

```tsx
const parentRoute = createRoute({
  getParentRoute: () => parentRoute,
})
```


## 내보낸 훅, 컴포넌트, 유틸리티

여러분의 라우터 타입이 `Link`, `useNavigate`, `useParams` 같은 최상위 내보내기와 함께 작동하려면, 이 타입들이 타입스크립트 모듈 경계를 통과하고 라이브러리에 직접 등록되어야 합니다. 이를 위해 내보낸 `Register` 인터페이스에 선언 병합(declaration merging)을 사용합니다.

```ts
const router = createRouter({
  // ...
})

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

이렇게 라우터를 모듈에 등록하면, 내보낸 훅, 컴포넌트, 유틸리티를 여러분의 라우터의 정확한 타입과 함께 사용할 수 있습니다.


## 컴포넌트 컨텍스트 문제 해결하기

컴포넌트 컨텍스트는 React와 같은 프레임워크에서 컴포넌트에 의존성을 제공하는 유용한 도구입니다. 하지만 이 컨텍스트가 컴포넌트 계층 구조를 따라 이동하면서 타입이 변경되면, TypeScript가 이러한 변화를 추론하는 것이 불가능해집니다. 이를 해결하기 위해 컨텍스트 기반 훅과 컴포넌트는 사용되는 방식과 위치에 대한 힌트를 제공해야 합니다.

```tsx
export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})

function PostsComponent() {
  // 각 라우트는 TanStack Router의 내장 훅 대부분에 대해 타입 안전한 버전을 제공합니다.
  const params = Route.useParams()
  const search = Route.useSearch()

  // 일부 훅은 현재 라우트뿐만 아니라 전체 라우터의 컨텍스트를 필요로 합니다. 
  // 타입 안전성을 보장하려면, 라우트 계층 구조에서 상대적 위치를 알려주는 `from` 파라미터를 전달해야 합니다.
  const navigate = useNavigate({ from: Route.fullPath })
  // ... 기타 코드
}
```

컨텍스트 힌트가 필요한 모든 훅과 컴포넌트는 `from` 파라미터를 가지고 있습니다. 이 파라미터에 렌더링 중인 라우트의 ID나 경로를 전달할 수 있습니다.

> 🧠 빠른 팁: 컴포넌트가 코드 분할(code-split)된 경우, [getRouteApi 함수](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)를 사용하여 `Route.fullPath`를 전달하지 않고도 타입 안전한 `useParams()`와 `useSearch()` 훅에 접근할 수 있습니다.


### 라우트를 모르면 어떻게 되나요? 공유 컴포넌트라면요?

`from` 속성은 선택사항입니다. 이 속성을 전달하지 않으면, 라우터가 사용 가능한 타입을 최대한 추측하여 제공합니다. 일반적으로 라우터 내 모든 라우트의 타입을 합친 유니온 타입을 얻게 됩니다.


### 잘못된 `from` 경로를 전달하면 어떻게 되나요?

타입스크립트(TypeScript)에서는 문제없이 통과할 수 있는 `from` 경로를 전달할 수 있지만, 실제 런타임에서 렌더링 중인 라우트와 일치하지 않을 가능성이 있습니다. 이 경우, `from`을 지원하는 각 훅(hook)과 컴포넌트는 여러분의 기대와 실제 렌더링 중인 라우트가 일치하지 않는지 감지하고, 런타임 오류를 발생시킵니다.


### 라우트를 모르거나 공유 컴포넌트라서 `from`을 전달할 수 없는 경우에는 어떻게 해야 하나요?

여러 라우트에서 공유하는 컴포넌트를 렌더링하거나, 라우트 내에 있지 않은 컴포넌트를 렌더링하는 경우에는 `from` 옵션 대신 `strict: false`를 전달할 수 있습니다. 이렇게 하면 런타임 오류를 방지할 뿐만 아니라, 호출하는 훅에 대해 완화된(relaxed) 타입을 제공합니다. 예를 들어, 공유 컴포넌트에서 `useSearch`를 호출하는 경우를 생각해 보겠습니다.

```tsx
function MyComponent() {
  const search = useSearch({ strict: false });
}
```

이 경우, `search` 변수는 라우터 내 모든 라우트에서 가능한 검색 파라미터(search params)의 유니온 타입으로 타입이 지정됩니다.


## 라우터 컨텍스트

라우터 컨텍스트는 궁극적인 계층적 의존성 주입(dependency injection)으로 매우 유용합니다. 여러분은 라우터와 라우터가 렌더링하는 모든 라우트에 컨텍스트를 제공할 수 있습니다. 이 컨텍스트를 구축하면, TanStack Router는 라우트 계층 구조와 함께 컨텍스트를 병합하여 각 라우트가 부모 라우트의 컨텍스트에 접근할 수 있도록 합니다.

`createRootRouteWithContext` 팩토리는 특정 타입으로 초기화된 새로운 라우터를 생성합니다. 이는 라우터에 동일한 타입 계약을 충족해야 하는 요구사항을 만들며, 전체 라우트 트리에서 컨텍스트가 올바르게 타입화되도록 보장합니다.

```tsx
const rootRoute = createRootRouteWithContext()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // 모든 자식 라우트는 컨텍스트에서 `whateverYouWant`에 접근할 수 있음
])

const router = createRouter({
  routeTree,
  context: {
    // 이제 이 값을 전달해야 함
    whateverYouWant: true,
  },
})
```


## 성능 최적화 권장 사항

애플리케이션 규모가 커질수록 TypeScript 검사 시간은 자연스럽게 증가합니다. 애플리케이션 규모가 커졌을 때 TypeScript 검사 시간을 줄이기 위해 몇 가지 주의할 점이 있습니다.


### 필요한 타입만 추론하세요

클라이언트 측 데이터 캐시(TanStack Query 등)를 사용할 때 데이터를 미리 가져오는(prefetch) 것은 좋은 패턴입니다. 예를 들어, TanStack Query를 사용하면 `loader`에서 `queryClient.ensureQueryData`를 호출하는 라우트를 만들 수 있습니다.

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: ({ context: { queryClient }, params: { postId } }) =>
    queryClient.ensureQueryData(postQueryOptions(postId)),
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <>
}
```

이 코드는 작은 규모의 라우트 트리에서는 문제가 없어 보일 수 있고, 타입스크립트 성능 문제도 눈에 띄지 않을 수 있습니다. 하지만 이 경우, 라우트에서 사용되지 않음에도 불구하고 타입스크립트는 로더의 반환 타입을 추론해야 합니다. 만약 로더 데이터가 복잡한 타입이고, 이런 방식으로 미리 가져오는 라우트가 많다면, 에디터 성능이 느려질 수 있습니다. 이 경우, 간단히 변경하여 타입스크립트가 `Promise<void>`를 추론하도록 할 수 있습니다.

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: async ({ context: { queryClient }, params: { postId } }) => {
    await queryClient.ensureQueryData(postQueryOptions(postId))
  },
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <>
}
```

이렇게 하면 로더 데이터는 추론되지 않고, `useSuspenseQuery`를 처음 사용할 때 추론이 이루어집니다.


### 가능한 한 관련된 라우트로 범위를 좁히세요

다음과 같이 `Link`를 사용하는 경우를 생각해보세요.

```tsx
// 예시 코드
```

**이 예제는 타입스크립트 성능에 좋지 않습니다.** 그 이유는 `search`가 모든 라우트의 `search` 파라미터를 포함한 유니온 타입으로 해석되기 때문입니다. 타입스크립트는 `search` 프로퍼티에 전달된 값이 이 큰 유니온 타입과 일치하는지 확인해야 합니다. 애플리케이션이 커질수록, 이 확인 작업은 라우트와 검색 파라미터의 수에 비례해 선형적으로 증가합니다. 우리는 이 경우를 최적화하기 위해 최선을 다했지만(타입스크립트는 일반적으로 이 작업을 한 번 수행하고 캐시합니다), 이 큰 유니온 타입에 대한 초기 확인 작업은 비용이 많이 듭니다. 이는 `params`와 `useSearch`, `useParams`, `useNavigate` 등의 다른 API에도 동일하게 적용됩니다.

대신, `from`이나 `to`를 사용해 관련된 라우트로 범위를 좁히는 것이 좋습니다.

```tsx
// 예시 코드
```

`to`나 `from`에 유니온 타입을 전달해 관심 있는 라우트를 좁힐 수 있다는 점을 기억하세요.

```tsx
const from: '/posts/$postId/deep' | '/posts/' = '/posts/'
```

또한 `from`에 브랜치를 전달해 해당 브랜치의 하위 라우트에서만 `search`나 `params`를 해석하도록 할 수도 있습니다.

```tsx
const from = '/posts'
```

`/posts`는 동일한 `search`나 `params`를 공유하는 많은 하위 라우트를 가진 브랜치일 수 있습니다.


### `addChildren`의 객체 문법 사용을 고려해보세요

라우트는 `params`, `search`, `loaders`, `context` 등을 포함하는 경우가 많습니다. 이러한 값들은 외부 의존성을 참조할 수도 있고, 타입스크립트 추론에 부하를 줄 수 있습니다. 이런 경우, 라우트 트리를 생성할 때 튜플 대신 객체를 사용하는 것이 더 나은 성능을 보일 수 있습니다.

`createChildren`도 객체를 인자로 받을 수 있습니다. 복잡한 라우트와 외부 라이브러리를 사용하는 대규모 라우트 트리에서 객체를 사용하면 타입스크립트의 타입 체크 속도가 훨씬 빨라질 수 있습니다. 성능 향상은 프로젝트의 규모, 사용하는 외부 의존성, 그리고 해당 라이브러리의 타입 정의에 따라 달라집니다.

```tsx
const routeTree = rootRoute.addChildren({
  postsRoute: postsRoute.addChildren({ postRoute, postsIndexRoute }),
  indexRoute,
})
```

이 문법은 조금 더 장황하지만 타입스크립트의 성능이 더 좋습니다. 파일 기반 라우팅을 사용하면 라우트 트리가 자동으로 생성되기 때문에, 장황한 라우트 트리는 큰 문제가 되지 않습니다.


### 내부 타입을 좁히지 않고 사용하지 마세요

종종 외부로 노출된 타입을 재사용하고 싶을 수 있습니다. 예를 들어, 다음과 같이 `LinkProps`를 사용하고 싶을 수 있습니다.

```tsx
const props: LinkProps = {
  to: '/posts/',
}

return (
  
)
```

**이 방식은 TypeScript 성능에 매우 나쁜 영향을 미칩니다.** 문제는 `LinkProps`가 타입 인자를 받지 않기 때문에 매우 큰 타입이라는 점입니다. 이 타입은 모든 `search` 파라미터의 유니온 타입인 `search`를 포함하고, 모든 `params`의 유니온 타입인 `params`도 포함합니다. 이 객체를 `Link`와 병합할 때, 이 거대한 타입에 대한 구조적 비교를 수행하게 됩니다.

대신, `as const satisfies`를 사용하여 정확한 타입을 추론하고, `LinkProps`를 직접 사용하지 않도록 하면 큰 타입 검사를 피할 수 있습니다.

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps

return (
  
)
```

이제 `props`는 `LinkProps` 타입이 아니기 때문에, 타입이 훨씬 더 정확해져 검사 비용이 줄어듭니다. 또한 `LinkProps`를 좁혀서 타입 검사를 더 개선할 수도 있습니다.

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps

return (
  
)
```

이 방식은 좁혀진 `LinkProps` 타입에 대해 검사하기 때문에 더 빠릅니다.

또한, `LinkProps`를 특정 타입으로 좁혀서 컴포넌트의 프로퍼티나 함수의 인자로 사용할 수도 있습니다.

```tsx
export const myLinkProps = [
  {
    to: '/posts',
  },
  {
    to: '/posts/$postId',
    params: { postId: 'postId' },
  },
] as const satisfies ReadonlyArray

export type MyLinkProps = (typeof myLinkProps)[number]

const MyComponent = (props: { linkProps: MyLinkProps }) => {
  return 
}
```

이 방식은 컴포넌트에서 `LinkProps`를 직접 사용하는 것보다 빠릅니다. 왜냐하면 `MyLinkProps`는 훨씬 더 정확한 타입이기 때문입니다.

또 다른 해결책은 `LinkProps`를 사용하지 않고, 특정 라우트로 좁혀진 `Link` 컴포넌트를 렌더링하기 위해 제어의 역전(Inversion of Control)을 제공하는 것입니다. 렌더 프로퍼티(Render Props)는 컴포넌트 사용자에게 제어를 역전시키는 좋은 방법입니다.

```tsx
export interface MyComponentProps {
  readonly renderLink: () => React.ReactNode
}

const MyComponent = (props: MyComponentProps) => {
  return {props.renderLink()}
}

const Page = () => {
  return  } />
}
```

이 예제는 컴포넌트 사용자에게 네비게이션 위치를 제어를 역전시켰기 때문에 매우 빠릅니다. `Link`는 우리가 이동하려는 정확한 라우트로 좁혀져 있습니다.


