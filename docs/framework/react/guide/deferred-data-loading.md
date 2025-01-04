# Table of Contents

- [지연된 데이터 로딩](#지연된-데이터-로딩)
  - [`Await`를 사용한 지연 데이터 로딩](#await를-사용한-지연-데이터-로딩)
  - [외부 라이브러리를 사용한 지연 데이터 로딩](#외부-라이브러리를-사용한-지연-데이터-로딩)
  - [캐싱과 무효화](#캐싱과-무효화)
  - [SSR & 스트리밍 지연 데이터](#ssr--스트리밍-지연-데이터)
  - [SSR 스트리밍 라이프사이클](#ssr-스트리밍-라이프사이클)

# 지연된 데이터 로딩

TanStack Router는 로더를 병렬로 실행하고 모든 로더가 완료될 때까지 기다린 후 다음 라우트를 렌더링하도록 설계되었습니다. 이 방식은 대부분의 경우 유용하지만, 때로는 나머지 데이터가 백그라운드에서 로딩되는 동안 사용자에게 더 빠르게 무언가를 보여주고 싶을 수 있습니다.

**지연된 데이터 로딩**은 라우터가 다음 위치의 중요한 데이터/마크업을 렌더링하는 동시에, 느리고 중요하지 않은 라우트 데이터가 백그라운드에서 처리되도록 하는 패턴입니다. 이 프로세스는 클라이언트와 서버 양쪽에서 작동하며(스트리밍을 통해), 애플리케이션의 **지각된 성능**을 향상시키는 훌륭한 방법입니다.

만약 [TanStack Query](https://react-query.tanstack.com)나 기타 데이터 페칭 라이브러리를 사용 중이라면, 지연된 데이터 로딩은 약간 다르게 동작합니다. 자세한 내용은 [외부 라이브러리와 함께하는 지연된 데이터 로딩](#deferred-data-loading-with-external-libraries) 섹션을 참고하세요.


## `Await`를 사용한 지연 데이터 로딩

느리거나 중요하지 않은 데이터를 지연시키려면, 로더 응답에서 **아직 처리되지 않은(unresolved)** Promise를 반환하면 됩니다.

```tsx
// src/routes/posts.$postId.tsx
import * as React from 'react'
import { createFileRoute, defer } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async () => {
    // 느린 데이터를 가져오지만, await하지 않음
    const slowDataPromise = fetchSlowData()

    // 빠르게 처리되는 데이터를 가져와서 await
    const fastData = await fetchFastData()

    return {
      fastData,
      deferredSlowData: slowDataPromise,
    }
  },
})
```

어떤 Promise라도 처리되면, 다음 라우트가 렌더링을 시작하면서 지연된 Promise는 계속 처리됩니다.

컴포넌트에서는 `Await` 컴포넌트를 사용하여 지연된 Promise를 처리하고 활용할 수 있습니다.

```tsx
// src/routes/posts.$postId.tsx
import * as React from 'react'
import { createFileRoute, Await } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const { deferredSlowData, fastData } = Route.useLoaderData()

  // fastData를 사용하여 작업 수행

  return (
    <React.Suspense fallback="Loading...">
      <Await promise={deferredSlowData}>
        {(data) => {
          return <div>{data}</div>
        }}
      </Await>
    </React.Suspense>
  )
}
```

> [!TIP]
> 컴포넌트가 코드 분할(code-split)된 경우, [getRouteApi 함수](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)를 사용하여 `Route` 설정을 임포트하지 않고도 타입이 지정된 `useLoaderData()` 훅에 접근할 수 있습니다.

`Await` 컴포넌트는 Promise를 처리하기 위해 가장 가까운 Suspense 경계를 트리거합니다. Promise가 처리되면, 컴포넌트의 `children`을 함수로 렌더링하며, 이때 처리된 데이터를 인자로 받습니다.

Promise가 거부되면, `Await` 컴포넌트는 직렬화된 오류를 던지며, 이는 가장 가까운 오류 경계에서 잡힐 수 있습니다.

> [!TIP]
> React 19에서는 `Await` 대신 `use()` 훅을 사용할 수 있습니다.


## 외부 라이브러리를 사용한 지연 데이터 로딩

라우트 정보를 가져오는 전략이 [TanStack Query](https://react-query.tanstack.com)와 같은 외부 라이브러리를 사용한 [외부 데이터 로딩](./external-data-loading.md)에 의존하는 경우, 지연 데이터 로딩은 약간 다르게 동작합니다. 이 라이브러리는 데이터 가져오기와 캐싱을 TanStack Router 외부에서 처리하기 때문입니다.

따라서 `defer`와 `Await`를 사용하는 대신, 라우트의 `loader`를 사용하여 데이터 가져오기를 시작한 후, 컴포넌트에서 라이브러리의 훅을 사용해 데이터에 접근할 수 있습니다.

```tsx
// src/routes/posts.$postId.tsx
import * as React from 'react'
import { createFileRoute } from '@tanstack/react-router'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ context: { queryClient } }) => {
    // 느린 데이터 가져오기를 시작하지만 기다리지 않음
    queryClient.prefetchQuery(slowDataOptions())

    // 빠르게 해결되는 데이터를 가져오고 기다림
    await queryClient.ensureQueryData(fastDataOptions())
  },
})
```

그런 다음 컴포넌트에서 라이브러리의 훅을 사용해 데이터에 접근할 수 있습니다:

```tsx
// src/routes/posts.$postId.tsx
import * as React from 'react'
import { createFileRoute } from '@tanstack/react-router'
import { useSuspenseQuery } from '@tanstack/react-query'
import { slowDataOptions, fastDataOptions } from '~/api/query-options'

export const Route = createFileRoute('/posts/$postId')({
  // ...
  component: PostIdComponent,
})

function PostIdComponent() {
  const fastData = useSuspenseQuery(fastDataOptions())

  // fastData를 사용해 작업 수행

  return (
    <div>
      Loading...
    </div>
  )
}

function SlowDataComponent() {
  const data = useSuspenseQuery(slowDataOptions())

  return <div>{data}</div>
}
```


## 캐싱과 무효화

스트리밍된 Promise는 연결된 로더 데이터와 동일한 생명주기를 따릅니다. 심지어 미리 로드할 수도 있습니다!


## SSR & 스트리밍 지연 데이터

**스트리밍을 사용하려면 이를 지원하는 서버가 필요하며, TanStack Router가 이를 올바르게 사용하도록 설정되어야 합니다.**

서버를 스트리밍에 맞게 설정하는 단계별 지침은 전체 [SSR 가이드](/docs/guide/server-streaming)를 참고하세요.


## SSR 스트리밍 라이프사이클

다음은 TanStack Router에서 지연된 데이터 스트리밍이 동작하는 방식에 대한 개요입니다:

- **서버**
  - 라우트 로더에서 반환된 Promise를 표시하고 추적합니다.
  - 모든 로더가 완료되면, 지연된 Promise를 직렬화하여 HTML에 포함시킵니다.
  - 라우트 렌더링을 시작합니다.
  - `<Await>` 컴포넌트와 함께 렌더링된 지연된 Promise는 서스펜스 경계를 트리거하여, 서버가 해당 지점까지 HTML을 스트리밍할 수 있게 합니다.

- **클라이언트**
  - 클라이언트는 서버로부터 초기 HTML을 받습니다.
  - `<Await>` 컴포넌트는 서버에서 데이터가 해결될 때까지 플레이스홀더 Promise로 대기 상태가 됩니다.

- **서버**
  - 지연된 Promise가 해결되면, 그 결과(또는 오류)를 직렬화하여 인라인 스크립트 태그를 통해 클라이언트로 스트리밍합니다.
  - 해결된 `<Await>` 컴포넌트와 서스펜스 경계가 완료되면, 결과 HTML과 함께 데이터를 클라이언트로 스트리밍합니다.

- **클라이언트**
  - `<Await>` 내부의 대기 중인 플레이스홀더 Promise는 스트리밍된 데이터/오류 응답으로 해결됩니다. 그 결과를 렌더링하거나, 오류를 가장 가까운 오류 경계로 전달합니다.

```javascript
// 예제 코드
<Await resolve={deferredData}>
  {(data) => <div>{data}</div>}
</Await>
```


