# Table of Contents

- [SSR (서버 사이드 렌더링)](#ssr-서버-사이드-렌더링)
  - [비스트리밍 SSR (Non-Streaming SSR)](#비스트리밍-ssr-non-streaming-ssr)
    - [라우터 생성](#라우터-생성)
    - [서버 히스토리](#서버-히스토리)
    - [서버에서 라우터의 중요 데이터 로딩하기](#서버에서-라우터의-중요-데이터-로딩하기)
  - [자동 로더 데이터 탈수/수화](#자동-로더-데이터-탈수수화)
    - [서버에서 애플리케이션 렌더링하기](#서버에서-애플리케이션-렌더링하기)
    - [404 에러 처리하기](#404-에러-처리하기)
    - [모든 것을 한데 모아보기!](#모든-것을-한데-모아보기)
  - [클라이언트에서 애플리케이션 렌더링하기](#클라이언트에서-애플리케이션-렌더링하기)
  - [스트리밍 SSR](#스트리밍-ssr)
  - [스트리밍 데이터 탈수/수화(Streaming Dehydration/Hydration)](#스트리밍-데이터-탈수수화streaming-dehydrationhydration)
  - [데이터 변환기(Data Transformers)](#데이터-변환기data-transformers)

# SSR (서버 사이드 렌더링)

서버 사이드 렌더링(SSR)은 서버에서 컴포넌트를 렌더링하고 HTML 마크업을 클라이언트로 전송하는 과정입니다. 클라이언트는 이 마크업을 받아 완전히 상호작용 가능한 컴포넌트로 변환합니다.

SSR에는 일반적으로 두 가지 방식이 있습니다:

- **논스트리밍 SSR**
  - 전체 페이지를 서버에서 렌더링하고, 애플리케이션이 클라이언트에서 상호작용을 위해 필요한 직렬화된 데이터를 포함한 HTML을 한 번에 클라이언트로 전송합니다.
  
- **스트리밍 SSR**
  - 페이지의 중요한 첫 번째 페인트(First Paint)를 서버에서 렌더링하고, 애플리케이션이 클라이언트에서 상호작용을 위해 필요한 직렬화된 데이터를 포함한 HTML을 한 번에 클라이언트로 전송합니다.
  - 나머지 페이지는 서버에서 렌더링되는 대로 클라이언트로 스트리밍됩니다.

이 가이드에서는 TanStack Router를 사용해 두 가지 SSR 방식을 구현하는 방법을 설명합니다!


## 비스트리밍 SSR (Non-Streaming SSR)

비스트리밍 서버 사이드 렌더링(SSR)은 서버에서 전체 애플리케이션 페이지의 마크업을 렌더링하고 완성된 HTML 마크업(및 데이터)을 클라이언트로 전송하는 전통적인 방식입니다. 클라이언트는 이 마크업을 다시 완전히 상호작용 가능한 애플리케이션으로 **하이드레이션(hydration)**합니다.

TanStack Router를 사용해 비스트리밍 SSR을 구현하려면 다음 유틸리티가 필요합니다:

- `@tanstack/start/server`에서 제공하는 `StartServer`
  - 예시: `<StartServer router={router} />`
  - 서버 진입점에서 이 컴포넌트를 렌더링하면 애플리케이션을 렌더링하고, 애플리케이션 수준의 하이드레이션/디하이드레이션을 자동으로 처리합니다. 또한 `Router`의 `Wrap` 컴포넌트 옵션을 구현합니다.
  
- `@tanstack/start`에서 제공하는 `StartClient`
  - 예시: `<StartClient router={router} />`
  - 클라이언트 진입점에서 이 컴포넌트를 렌더링하면 애플리케이션을 렌더링하고, `Router`의 `Wrap` 컴포넌트 옵션을 자동으로 구현합니다.

이 방식은 서버에서 모든 작업을 완료한 후 클라이언트로 전송하므로, 초기 로딩 시간이 길어질 수 있습니다. 하지만 클라이언트 측에서 추가적인 렌더링 작업 없이 바로 상호작용 가능한 상태로 전환할 수 있다는 장점이 있습니다.


### 라우터 생성

여러분의 라우터는 서버와 클라이언트 양쪽에서 존재할 것이기 때문에, 두 환경에서 일관되게 라우터를 생성하는 것이 중요합니다. 가장 쉬운 방법은 `createRouter` 함수를 공유 파일에 노출시켜 서버와 클라이언트 진입 파일에서 모두 가져와 사용할 수 있도록 하는 것입니다.

- `src/router.tsx`

```tsx
import * as React from 'react'
import { createRouter as createTanstackRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

export function createRouter() {
  return createTanstackRouter({ routeTree })
}

declare module '@tanstack/react-router' {
  interface Register {
    router: ReturnType
  }
}
```

이제 이 함수를 서버와 클라이언트 진입 파일에서 가져와 라우터를 생성할 수 있습니다.

- `src/entry-server.tsx`

```tsx
import { createRouter } from './router'

export async function render(req, res) {
  const router = createRouter()
}
```

- `src/entry-client.tsx`

```tsx
import { createRouter } from './router'

const router = createRouter()
```


### 서버 히스토리

클라이언트에서는 라우터가 기본적으로 `createBrowserHistory` 인스턴스를 사용합니다. 이는 클라이언트에서 사용하기에 적합한 히스토리 타입입니다. 하지만 서버에서는 `createMemoryHistory` 인스턴스를 사용해야 합니다. 왜냐하면 `createBrowserHistory`는 `window` 객체를 사용하는데, 이 객체는 서버에 존재하지 않기 때문입니다.

> 🧠 메모리 히스토리를 초기화할 때, 렌더링되는 서버 URL로 설정해야 합니다.

- `src/entry-server.tsx`

```tsx
const router = createRouter()

const memoryHistory = createMemoryHistory({
  initialEntries: [opts.url],
})
```

메모리 히스토리 인스턴스를 생성한 후, 라우터가 이를 사용하도록 업데이트할 수 있습니다.

- `src/entry-server.tsx`

```tsx
router.update({
  history: memoryHistory,
})
```


### 서버에서 라우터의 중요 데이터 로딩하기

서버에서 애플리케이션을 렌더링하려면, 라우터가 라우트 로더를 통해 필요한 중요 데이터를 로드했는지 확인해야 합니다. 이를 위해 애플리케이션을 렌더링하기 전에 `await router.load()`를 사용할 수 있습니다. 이 코드는 현재 URL에 매칭되는 모든 라우트의 `loader` 함수를 병렬로 실행하고, 그 결과를 기다립니다.

- `src/entry-server.tsx`

```tsx
await router.load()
```


## 자동 로더 데이터 탈수/수화

TanStack Router는 여러분이 이 가이드에 설명된 표준 SSR 단계를 완료하는 한, 라우트에서 가져온 로더 데이터를 자동으로 탈수 및 수화합니다.

⚠️ 지연된 데이터 스트리밍을 사용하는 경우, 이 가이드의 끝부분에 있는 [SSR 스트리밍 및 스트림 변환](#streaming-ssr) 패턴을 구현했는지 확인해야 합니다.

데이터 로딩과 데이터 스트리밍을 활용하는 방법에 대한 자세한 내용은 [데이터 로딩](./data-loading.md) 및 [데이터 스트리밍](../data-streaming) 가이드를 참고하세요.


### 서버에서 애플리케이션 렌더링하기

현재 URL에 대한 모든 중요한 데이터를 로드한 라우터 인스턴스를 가지고 있다면, 서버에서 애플리케이션을 렌더링할 수 있습니다:

```tsx
// src/entry-server.tsx

const html = ReactDOMServer.renderToString()
```

이 코드는 React 애플리케이션을 서버에서 문자열 형태로 렌더링하는 기본적인 예제입니다. `ReactDOMServer.renderToString()` 함수는 React 컴포넌트를 HTML 문자열로 변환하여 서버 사이드 렌더링(SSR)을 가능하게 합니다. 이렇게 생성된 HTML은 클라이언트로 전송되어 초기 페이지 로딩 속도를 향상시킬 수 있습니다.


### 404 에러 처리하기

`router`는 렌더링 과정에서 404 에러가 발생했는지 확인할 수 있는 `hasNotFoundMatch` 메서드를 제공합니다. 이 메서드를 사용해 404 에러가 발생했는지 확인하고, 그에 따라 응답 상태 코드를 설정할 수 있습니다:

```tsx
// src/entry-server.tsx
if (router.hasNotFoundMatch()) statusCode = 404
```


### 모든 것을 한데 모아보기!

지금까지 설명한 개념들을 모두 활용한 서버 진입 파일의 완전한 예제를 살펴보겠습니다.

```tsx
// src/entry-server.tsx
import * as React from 'react'
import ReactDOMServer from 'react-dom/server'
import { createMemoryHistory } from '@tanstack/react-router'
import { StartServer } from '@tanstack/start/server'
import { createRouter } from './router'

export async function render(url, response) {
  const router = createRouter()

  const memoryHistory = createMemoryHistory({
    initialEntries: [url],
  })

  router.update({
    history: memoryHistory,
  })

  await router.load()

  const appHtml = ReactDOMServer.renderToString()

  response.statusCode = router.hasNotFoundMatch() ? 404 : 200
  response.setHeader('Content-Type', 'text/html')
  response.end(`${appHtml}`)
}
```

이 코드는 서버 측에서 라우터를 초기화하고, 주어진 URL에 따라 메모리 히스토리를 생성한 후, 라우터를 업데이트하고, 필요한 데이터를 로드한 뒤, React 앱을 HTML 문자열로 렌더링합니다. 마지막으로, 응답 상태 코드와 콘텐츠 타입을 설정하고, 렌더링된 HTML을 클라이언트에게 전송합니다.


## 클라이언트에서 애플리케이션 렌더링하기

클라이언트 측에서는 훨씬 간단합니다.

1. 라우터 인스턴스를 생성합니다.
2. `<StartClient />` 컴포넌트를 사용해 애플리케이션을 렌더링합니다.

```tsx
// src/entry-client.tsx

import * as React from 'react'
import ReactDOM from 'react-dom/client'

import { StartClient } from '@tanstack/start'
import { createRouter } from './router'

const router = createRouter()

ReactDOM.hydrateRoot(document, )
```

이 설정을 통해 애플리케이션은 서버에서 렌더링된 후 클라이언트에서 하이드레이션됩니다!


## 스트리밍 SSR

스트리밍 SSR은 가장 최신 형태의 SSR(서버 사이드 렌더링)로, 서버에서 렌더링된 HTML 마크업을 클라이언트에게 지속적이고 점진적으로 전송하는 과정입니다. 이는 기존의 SSR과 개념적으로 약간 다릅니다. 스트리밍 SSR은 초기 렌더링 시 중요한 첫 번째 페인트를 위한 마크업과 데이터를 클라이언트에게 전송한 후, 우선순위가 낮거나 응답 시간이 더 오래 걸리는 마크업과 데이터를 동일한 요청 내에서 클라이언트에게 스트리밍할 수 있습니다.

이 패턴은 데이터를 가져오는 데 시간이 오래 걸리거나 높은 지연 시간이 필요한 페이지에 유용합니다. 예를 들어, 서드파티 API에서 데이터를 가져와야 하는 페이지가 있다면, 중요한 초기 마크업과 데이터를 먼저 클라이언트에게 전송한 후, 덜 중요한 서드파티 데이터를 해결되는 대로 스트리밍할 수 있습니다.

**이 스트리밍 패턴은 `renderToPipeableStream`을 사용하는 한 자동으로 동작합니다.**


## 스트리밍 데이터 탈수/수화(Streaming Dehydration/Hydration)

스트리밍 데이터 탈수/수화는 마크업을 넘어서는 고급 패턴입니다. 서버에서 클라이언트로 지원 데이터를 탈수(Dehydration)하고 스트리밍한 뒤, 도착 시 다시 수화(Hydration)할 수 있습니다. 이 패턴은 서버에서 초기 마크업을 렌더링하는 데 사용된 기본 데이터를 추가로 활용하거나 관리해야 하는 애플리케이션에 유용합니다.


## 데이터 변환기(Data Transformers)

SSR(Server Side Rendering)을 사용할 때, 서버와 클라이언트 간에 전달되는 데이터는 네트워크 경계를 넘기 전에 직렬화(serialize)되어야 합니다. 기본적으로 TanStack Router는 `JSON.stringify`와 `JSON.parse`를 넘어 몇 가지 기본 타입을 지원하는 매우 가벼운 직렬화기를 사용합니다.

기본적으로 다음 타입들이 지원됩니다:

- `Date`
- `undefined`

만약 기본적으로 지원되어야 할 다른 타입이 있다고 생각되면, TanStack Router 저장소에 이슈를 열어주세요.

`Map`, `Set`, `BigInt` 등과 같은 더 복잡한 데이터 타입을 사용한다면, 타입 정의가 정확하고 데이터가 올바르게 직렬화 및 역직렬화되도록 커스텀 직렬화기를 사용해야 할 수 있습니다. 이때 `createRouter`의 `transformer` 옵션이 유용합니다.

데이터 변환기 API는 네트워크를 통해 통신할 때 이러한 데이터 타입을 투명하게 사용할 수 있도록 커스텀 직렬화기를 사용할 수 있게 해줍니다.

다음 예제는 [SuperJSON](https://github.com/blitz-js/superjson)을 사용하는 방법을 보여줍니다. 하지만 [`Router Transformer`](../api/router/RouterOptionsType.md#transformer-property)를 구현한 어떤 것이든 사용할 수 있습니다.

```tsx
import { SuperJSON } from 'superjson'

const router = createRouter({
  transformer: SuperJSON,
})
```

이렇게 하면 TanStack Router는 이제 네트워크를 통해 데이터를 직렬화할 때 SuperJSON을 적절히 사용하게 됩니다.


