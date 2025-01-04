# Table of Contents

- [개발자 경험에 대한 결정](#개발자-경험에-대한-결정)
  - [TanStack Router의 탄생 배경](#tanstack-router의-탄생-배경)
  - [TanStack Router는 어떻게 이를 달성할까요?](#tanstack-router는-어떻게-이를-달성할까요)
  - [1. 라우터 설정이 왜 이렇게 구성되었나요?](#1-라우터-설정이-왜-이렇게-구성되었나요)
  - [2. 타입 추론을 위한 Router 인스턴스 선언](#2-타입-추론을-위한-router-인스턴스-선언)
  - [3. 파일 기반 라우팅이 선호되는 이유는 무엇인가요?](#3-파일-기반-라우팅이-선호되는-이유는-무엇인가요)

# 개발자 경험에 대한 결정

사람들이 처음 TanStack Router를 사용할 때, 다음과 같은 주제를 중심으로 많은 질문을 하곤 합니다:

> 왜 이렇게 해야 하나요?

> 왜 이렇게 구현했나요? 저렇게 하지 않고?

> 저는 이렇게 하는 데 익숙한데, 왜 바꿔야 하나요?

이 모든 질문은 타당합니다. 대부분의 사람들은 서로 매우 비슷한 라우팅 라이브러리를 사용하는 데 익숙합니다. 모두 비슷한 API, 비슷한 개념, 비슷한 방식으로 동작하죠.

하지만 TanStack Router는 다릅니다. 평범한 라우팅 라이브러리가 아니에요. 평범한 상태 관리 라이브러리도 아니고, 평범한 어떤 것도 아닙니다.


## TanStack Router의 탄생 배경

TanStack Router는 [Nozzle.io](https://nozzle.io)의 필요에 의해 탄생했습니다. Nozzle.io는 복잡한 대시보드를 구동하기 위해 **타입 안정성(type-safety)**을 유지하면서도 최고 수준의 `URL Search Parameters` 경험을 제공하는 클라이언트 측 라우팅 솔루션이 필요했습니다.

이러한 요구사항을 바탕으로, TanStack Router는 처음부터 설계의 모든 측면을 꼼꼼히 고려하여 타입 안정성과 개발자 경험을 최고 수준으로 유지할 수 있도록 만들었습니다.


## TanStack Router는 어떻게 이를 달성할까요?

> TypeScript! TypeScript! TypeScript!

TanStack Router의 모든 측면은 가능한 한 타입 안전하게 설계되었습니다. 이를 위해 TypeScript의 타입 시스템을 최대한 활용합니다. 매우 고급이고 복잡한 타입, 타입 추론, 그리고 다른 기능들을 사용하여 개발자 경험을 최대한 매끄럽게 만듭니다.

하지만 이를 달성하기 위해, 라우팅 세계의 일반적인 관행과는 다른 몇 가지 결정을 내려야 했습니다.

1. [**라우트 설정 보일러플레이트?**](#1-why-is-the-routers-configuration-done-this-way): TypeScript가 라우트의 타입을 최대한 추론할 수 있도록 라우트를 정의해야 합니다.
2. [**라우터 인스턴스를 위한 TypeScript 모듈 선언?**](#2-declaring-the-router-instance-for-type-inference): TypeScript의 모듈 선언을 사용하여 `Router` 인스턴스를 애플리케이션의 나머지 부분에 전달해야 합니다.
3. [**코드 기반 라우팅보다 파일 기반 라우팅을 권장하는 이유?**](#3-why-is-file-based-routing-the-preferred-way-to-define-routes): 파일 기반 라우팅을 라우트 정의의 선호 방법으로 권장합니다.

> 요약; TanStack Router를 사용하는 개발자 경험의 모든 설계 결정은 여러분이 라우트 설정의 제어, 유연성, 유지보수성을 희생하지 않으면서도 최고 수준의 타입 안전성을 누릴 수 있도록 이루어졌습니다.


## 1. 라우터 설정이 왜 이렇게 구성되었나요?

TypeScript의 타입 추론 기능을 최대한 활용하려면, **제네릭(Generics)**이 가장 큰 도움이 됩니다. TanStack Router는 가능한 한 라우트의 타입을 추론할 수 있도록 모든 곳에서 제네릭을 사용합니다.

이 말은, 여러분이 라우트를 정의할 때 TypeScript가 라우트의 타입을 최대한 추론할 수 있도록 해야 한다는 의미입니다.

> JSX를 사용해 라우트를 정의할 수 있나요?

JSX를 사용해 라우트를 정의하는 것은 **불가능**합니다. TypeScript가 라우터 설정 타입을 추론할 수 없기 때문입니다.

```tsx
// ⛔️ 이 방식은 불가능합니다.
function App() {
  return (
    
      
      
      {/* ... */}
    
    // ^? 이 설정에서는 TypeScript가 라우트를 추론할 수 없습니다.
  )
}
```

이 방식은 `<Link>` 컴포넌트의 `to` prop을 수동으로 타이핑해야 하며, 런타임에서야 오류를 발견할 수 있기 때문에 적합하지 않습니다.

> 중첩된 객체 트리로 라우트를 정의할 수는 없을까요?

```tsx
// ⛔️ 이 파일은 계속해서 커질 것입니다...
const router = createRouter({
  routes: {
    posts: {
      component: PostsPage, // /posts
      children: {
        $postId: {
          component: PostIdPage, // /posts/$postId
        },
      },
    },
    // ...
  },
})
```

언뜻 보기에는 좋은 아이디어처럼 보입니다. 전체 라우트 계층 구조를 한눈에 파악할 수 있으니까요. 하지만 이 방식은 대규모 애플리케이션에는 적합하지 않은 몇 가지 큰 단점이 있습니다:

- **확장성이 떨어짐**: 애플리케이션이 커질수록 트리도 커지고 관리하기 어려워집니다. 모든 것이 하나의 파일에 정의되어 있기 때문에 유지보수가 매우 힘들어질 수 있습니다.
- **코드 분할에 적합하지 않음**: 각 컴포넌트를 수동으로 코드 분할한 후 라우트의 `component` 속성에 전달해야 하므로, 라우트 설정 파일이 점점 더 복잡해집니다.

이 문제는 라우터의 중첩된 컨텍스트, 로더, 검색 파라미터 검증 등의 기능을 사용할수록 더 심각해집니다.

> 그렇다면 라우트를 정의하는 가장 좋은 방법은 무엇인가요?

가장 좋은 방법은 라우트 설정을 라우트 트리 외부로 추상화한 후, 이 설정들을 하나의 통합된 라우트 트리로 조합하는 것입니다. 이렇게 만들어진 라우트 트리를 `createRouter` 함수에 전달하면 됩니다.

[코드 기반 라우팅](./guide/code-based-routing.md)에 대해 더 알아보고, 이 방식으로 라우트를 정의하는 방법을 확인할 수 있습니다.

> [!TIP]
> 코드 기반 라우팅이 조금 번거롭다고 느껴지나요? [파일 기반 라우팅](#3-why-is-file-based-routing-the-preferred-way-to-define-routes)이 왜 선호되는지 알아보세요.


## 2. 타입 추론을 위한 Router 인스턴스 선언

> 왜 `Router`를 선언해야 할까요?

> 이 선언 작업이 너무 복잡해요...

라우트를 트리 구조로 구성하고 제네릭을 올바르게 설정한 후 `createRouter`를 사용해 Router 인스턴스에 전달했다면, 이 정보를 애플리케이션의 나머지 부분에 전달해야 합니다.

이를 위해 고려한 두 가지 접근 방식이 있습니다:

1. **임포트**: `Router` 인스턴스를 생성한 파일에서 임포트하여 컴포넌트에서 직접 사용할 수 있습니다.

```tsx
import { router } from '@/src/app'
export const PostsIdLink = () => {
  return (
    <Link to="/posts/$postId" params={{ postId: '123' }}>
      Go to post 123
    </Link>
  )
}
```

이 방식의 단점은 `Router` 인스턴스를 사용하려는 모든 파일에서 임포트해야 한다는 점입니다. 이는 번들 크기를 증가시키고 관리가 번거로워질 수 있으며, 애플리케이션이 커지고 라우터의 더 많은 기능을 사용할수록 더 악화됩니다.

2. **모듈 선언**: TypeScript의 모듈 선언을 사용해 `Router` 인스턴스를 모듈로 선언하면, 애플리케이션 어디서나 타입 추론을 위해 사용할 수 있습니다. 이를 임포트할 필요가 없습니다.

이 작업은 애플리케이션에서 한 번만 수행하면 됩니다.

```tsx
// src/app.tsx
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

그러면 애플리케이션 어디서나 자동 완성 기능을 활용할 수 있습니다.

```tsx
export const PostsIdLink = () => {
  return (
    <Link
      to="/posts/$postId"
      // ^? TypeScript가 이를 자동으로 완성해줍니다
      params={{ postId: '123' }} // 이것도 마찬가지입니다!
    >
      Go to post 123
    </Link>
  )
}
```

우리는 **모듈 선언** 방식을 선택했습니다. 이 방식이 가장 확장성 있고 유지보수하기 쉬우며, 오버헤드와 보일러플레이트가 가장 적기 때문입니다.


## 3. 파일 기반 라우팅이 선호되는 이유는 무엇인가요?

> 왜 문서에서 파일 기반 라우팅을 권장하나요?

> 저는 라우트를 하나의 파일에 정의하는 데 익숙한데, 왜 바꿔야 하나요?

TanStack Router 문서를 보면 **파일 기반 라우팅**을 권장하는 것을 금방 알 수 있습니다. 이는 파일 기반 라우팅이 라우트를 정의하는 가장 확장 가능하고 유지보수하기 쉬운 방법이라고 판단했기 때문입니다.

> [!TIP]
> 계속하기 전에 [코드 기반 라우팅](./guide/code-based-routing.md)과 [파일 기반 라우팅](./guide/file-based-routing.md)에 대한 이해가 필요합니다.

앞서 언급했듯이, TanStack Router는 높은 수준의 타입 안전성과 유지보수성이 필요한 복잡한 애플리케이션을 위해 설계되었습니다. 이를 달성하기 위해 라우터 설정은 TypeScript가 가능한 한 많은 라우트 타입을 추론할 수 있도록 정밀하게 구성되었습니다.

TanStack Router를 사용한 `기본` 애플리케이션 설정에서 중요한 차이점은 라우트 설정에 `getParentRoute` 함수를 제공해야 한다는 점입니다. 이 함수는 현재 라우트의 부모 라우트를 반환합니다.

```tsx
import { createRoute } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})
```

이 단계에서 `postsIndexRoute`는 라우트 트리에서의 위치를 인식하고, 부모 라우트가 반환하는 `context`, `path params`, `search params`의 타입을 올바르게 추론할 수 있습니다. `getParentRoute` 함수를 잘못 정의하면 부모 라우트의 속성이 자식 라우트에서 올바르게 추론되지 않습니다.

따라서 이는 라우트 설정의 중요한 부분이며, 잘못 설정하면 실패 지점이 됩니다.

하지만 이는 기본 애플리케이션 설정의 일부일 뿐입니다. TanStack Router는 모든 라우트(루트 라우트 포함)를 **_라우트 트리_**로 묶어서 `createRouter` 함수에 전달한 후, 모듈에서 `Router` 인스턴스를 선언하여 타입 추론을 수행해야 합니다. 이 역시 라우트 설정의 중요한 부분이며, 잘못 설정하면 실패 지점이 됩니다.

> 🤯 약 40-50개의 라우트가 있는 애플리케이션에서 이 라우트 트리가 별도의 파일에 있다면, 쉽게 700줄 이상으로 늘어날 수 있습니다.

```tsx
const routeTree = rootRoute.addChildren([
  postsRoute.addChildren([postsIndexRoute, postsIdRoute]),
])
```

이 복잡성은 중첩된 컨텍스트, 로더, 검색 파라미터 검증 등 라우터의 더 많은 기능을 사용할수록 증가합니다. 따라서 라우트를 하나의 파일에 정의하는 것은 더 이상 실용적이지 않습니다. 결국 사용자들은 여러 파일에 걸쳐 라우트를 정의하는 `반 일관적인` 방식을 구축하게 됩니다. 이는 라우트 설정에서 불일치와 오류를 초래할 수 있습니다.

마지막으로 코드 분할 문제가 있습니다. 애플리케이션이 커질수록 초기 번들 크기를 줄이기 위해 컴포넌트를 코드 분할하고 싶을 것입니다. 하지만 라우트를 하나의 파일이나 여러 파일에 정의할 때 이를 관리하는 것은 약간 골치 아픈 일이 될 수 있습니다.

```tsx
import { createRoute, lazyRouteComponent } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
  component: lazyRouteComponent(() => import('../page-components/posts/index')),
})
```

이 모든 보일러플레이트는 최고 수준의 타입 추론 경험을 제공하기 위해 필수적이지만, 압도적일 수 있고 라우트 설정에서 불일치와 오류를 초래할 수 있습니다.

... 그리고 이 예제 설정은 단일 코드 분할 라우트를 렌더링하기 위한 것입니다. 40-50개의 라우트에 대해 이 작업을 해야 한다고 상상해보세요. 아직 `context`, `loaders`, `search param validation` 및 라우터의 다른 기능을 건드리지도 않았다는 것을 기억하세요 🤕.

> 그렇다면 왜 파일 기반 라우팅이 선호되나요?

TanStack Router의 파일 기반 라우팅은 이러한 모든 문제를 해결하기 위해 설계되었습니다. 이 방식은 라우트를 예측 가능하고 관리 및 유지보수가 쉬운 방식으로 정의할 수 있게 해주며, 애플리케이션이 성장함에 따라 확장 가능합니다.

파일 기반 라우팅 접근 방식은 TanStack Router CLI에 의해 구동됩니다. 이 CLI는 코드 기반 라우팅을 사용할 때 발생하는 라우트 설정의 문제점을 해결하기 위해 3가지 필수 작업을 수행합니다:

1. **라우트 설정 보일러플레이트**: 라우트 설정을 위한 보일러플레이트를 생성합니다.
2. **라우트 트리 연결**: 라우트 설정을 하나의 통합된 라우트 트리로 연결합니다. 또한 백그라운드에서 `getParentRoute` 함수를 올바르게 업데이트하여 라우트와 부모 라우트를 매칭시킵니다.
3. **코드 분할**: 컴포넌트를 자동으로 코드 분할하고, 올바른 지연 로딩(lazy import)으로 라우트 설정을 업데이트합니다.

이전 예제의 라우트 설정이 파일 기반 라우팅으로 어떻게 보이는지 살펴보겠습니다.

```tsx
// src/routes/posts/index.lazy.ts
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/posts/')({
  component: () => 'Posts index component goes here!!!',
})
```

이게 전부입니다! `getParentRoute` 함수를 정의하거나, 라우트 트리를 연결하거나, 컴포넌트를 코드 분할하는 것에 대해 걱정할 필요가 없습니다. CLI가 이 모든 것을 처리해줍니다.

TanStack Router CLI는 라우트 설정에 대한 여러분의 통제권을 빼앗지 않습니다. 이는 가능한 한 유연하게 설계되어, 애플리케이션에 적합한 방식으로 라우트를 정의할 수 있도록 하면서도 라우트 설정의 보일러플레이트와 복잡성을 줄여줍니다.

TanStack Router에서 파일 기반 라우팅과 코드 분할이 어떻게 작동하는지 더 깊이 이해하려면 [파일 기반 라우팅](./guide/file-based-routing.md) 및 [코드 분할](./guide/code-splitting.md) 가이드를 확인하세요.


