# 히스토리 타입

TanStack Router를 사용하기 위해 `@tanstack/history` API 자체를 알 필요는 없지만, 어떻게 동작하는지 이해하는 것이 좋습니다. 내부적으로 TanStack Router는 라우팅 히스토리를 관리하기 위해 `history` 추상화를 필요로 하고 사용합니다.

히스토리 인스턴스를 직접 생성하지 않으면, 라우터가 초기화될 때 브라우저 기반의 인스턴스가 자동으로 생성됩니다. 특별한 히스토리 API 타입이 필요하다면, `@tanstack/history` 패키지를 사용하여 직접 생성할 수 있습니다:

- `createBrowserHistory`: 기본 히스토리 타입입니다.
- `createHashHistory`: 해시를 사용하여 히스토리를 추적하는 타입입니다.
- `createMemoryHistory`: 히스토리를 메모리에 저장하는 타입입니다.

히스토리 인스턴스를 생성한 후, `Router` 생성자에 전달할 수 있습니다:

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 초기 URL을 전달합니다
})

const router = createRouter({ routeTree, history: memoryHistory })
```


## 브라우저 라우팅

`createBrowserHistory`는 기본적으로 사용되는 히스토리 타입입니다. 이는 브라우저의 히스토리 API를 활용하여 브라우저 히스토리를 관리합니다.


## 해시 라우팅(Hash Routing)

서버가 HTTP 요청에 대해 index.html로의 리라이트(rewrite)를 지원하지 않는 환경(서버가 없는 경우 등)에서 해시 라우팅을 사용하면 도움이 됩니다.

```ts
import { createHashHistory, createRouter } from '@tanstack/react-router'

const hashHistory = createHashHistory()

const router = createRouter({ routeTree, history: hashHistory })
```


## 메모리 라우팅(Memory Routing)

메모리 라우팅은 브라우저가 아닌 환경에서 사용하거나, 컴포넌트가 URL과 상호작용하지 않도록 할 때 유용합니다.

```ts
import { createMemoryHistory, createRouter } from '@tanstack/react-router'

const memoryHistory = createMemoryHistory({
  initialEntries: ['/'], // 초기 URL을 전달합니다
})

const router = createRouter({ routeTree, history: memoryHistory })
```

서버 사이드 렌더링(SSR)을 위해 서버에서 사용하는 방법은 [SSR 가이드](./ssr.md#server-history)를 참고하세요.


