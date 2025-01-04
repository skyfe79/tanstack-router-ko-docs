# 미리 가져오기(Preloading)

TanStack Router에서 미리 가져오기는 사용자가 실제로 해당 라우트로 이동하기 전에 미리 로드하는 방법입니다. 이 기능은 사용자가 다음에 방문할 가능성이 높은 라우트에 유용합니다. 예를 들어, 여러분이 게시물 목록을 가지고 있고 사용자가 그 중 하나를 클릭할 가능성이 높다면, 해당 게시물 라우트를 미리 가져올 수 있습니다. 이렇게 하면 사용자가 클릭했을 때 바로 사용할 수 있게 됩니다.


## 지원되는 미리 가져오기(Preloading) 전략

- 의도 기반(Intent)
  - **"의도"** 기반 미리 가져오기는 `<Link>` 컴포넌트에 호버(hover) 및 터치 시작(touch start) 이벤트를 사용하여 목적지 라우트의 의존성을 미리 로드합니다.
  - 이 전략은 사용자가 다음에 방문할 가능성이 높은 라우트를 미리 로드할 때 유용합니다.
  
- 뷰포트 가시성(Viewport Visibility)
  - **"뷰포트"** 기반 미리 가져오기는 Intersection Observer API를 사용하여 `<Link>` 컴포넌트가 뷰포트에 들어올 때 목적지 라우트의 의존성을 미리 로드합니다.
  - 이 전략은 화면 아래나 보이지 않는 영역에 있는 라우트를 미리 로드할 때 유용합니다.
  
- 렌더링 기반(Render)
  - **"렌더링"** 기반 미리 가져오기는 `<Link>` 컴포넌트가 DOM에 렌더링되는 즉시 목적지 라우트의 의존성을 미리 로드합니다.
  - 이 전략은 항상 필요한 라우트를 미리 로드할 때 유용합니다.


## 미리 가져온 데이터는 메모리에 얼마나 오래 남아 있을까요?

미리 가져온 라우트 매칭은 몇 가지 중요한 주의사항과 함께 일시적으로 메모리에 캐시됩니다:

- **사용되지 않은 미리 가져온 데이터는 기본적으로 30초 후에 제거됩니다.** 이는 라우터의 `defaultPreloadMaxAge` 옵션을 설정하여 조정할 수 있습니다.
- **당연히, 라우트가 로드되면 미리 가져온 버전은 라우터의 일반적인 대기 중인 매칭 상태로 승격됩니다.**

미리 가져오기, 캐싱 및/또는 미리 가져온 데이터의 가비지 컬렉션에 대해 더 많은 제어가 필요하다면, [TanStack Query](https://tanstack.com/query)와 같은 외부 캐싱 라이브러리를 사용하는 것이 좋습니다.

애플리케이션에서 라우트를 미리 가져오는 가장 간단한 방법은 전체 라우터에 대해 `defaultPreload` 옵션을 `intent`로 설정하는 것입니다:

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreload: 'intent',
})
```

이렇게 하면 애플리케이션의 모든 `<Link>` 컴포넌트에 대해 기본적으로 `intent` 미리 가져오기가 활성화됩니다. 또한 개별 `<Link>` 컴포넌트에 `preload` 프로퍼티를 설정하여 기본 동작을 재정의할 수도 있습니다.


## 미리 가져오기 지연 시간

기본적으로, 사용자가 `<Link>` 컴포넌트에 마우스를 올리거나 터치한 후 **50ms**가 지나면 미리 가져오기가 시작됩니다. 이 지연 시간은 라우터의 `defaultPreloadDelay` 옵션을 설정하여 변경할 수 있습니다:

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadDelay: 100, // 100ms로 지연 시간 설정
})
```

또한, 개별 `<Link>` 컴포넌트에 `preloadDelay` 속성을 설정하여 기본 동작을 덮어쓸 수 있습니다. 이를 통해 각 링크마다 다른 지연 시간을 적용할 수 있습니다.


## 내장된 미리 가져오기 & `preloadStaleTime`

내장된 로더를 사용하는 경우, `routerOptions.defaultPreloadStaleTime` 또는 `routeOptions.preloadStaleTime`을 밀리초 단위로 설정하여 미리 가져온 데이터가 얼마 동안 신선한 상태로 유지될지 제어할 수 있습니다. **기본적으로 미리 가져온 데이터는 30초 동안 신선한 상태로 간주됩니다.**

이 설정을 변경하려면 라우터에 `defaultPreloadStaleTime` 옵션을 설정할 수 있습니다:

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 10_000, // 10초
})
```

또는 개별 라우트에 `routeOptions.preloadStaleTime` 옵션을 사용할 수도 있습니다:

```tsx
// src/routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => fetchPost(params.postId),
  // 미리 가져온 캐시가 10초 이상 지난 경우 다시 미리 가져오기
  preloadStaleTime: 10_000, // 10초
})
```


## 외부 라이브러리를 사용한 미리 가져오기(Preloading)

React Query와 같은 외부 캐싱 라이브러리를 통합할 때, 이러한 라이브러리는 자체적으로 데이터의 신선도를 판단하는 메커니즘을 가지고 있습니다. 이 경우, TanStack Router의 기본 미리 가져오기 및 stale-while-revalidate 로직을 재정의하고 싶을 수 있습니다. 이러한 라이브러리는 주로 `staleTime`과 같은 옵션을 사용해 데이터의 신선도를 제어합니다.

TanStack Router에서 미리 가져오기 동작을 커스터마이징하고 외부 라이브러리의 캐싱 전략을 최대한 활용하려면, 내장된 캐싱을 우회할 수 있습니다. 이를 위해 `routerOptions.defaultPreloadStaleTime` 또는 `routeOptions.preloadStaleTime`을 0으로 설정합니다. 이렇게 하면 모든 미리 가져오기가 내부적으로 stale로 표시되고, 로더가 항상 호출됩니다. 이를 통해 React Query와 같은 외부 라이브러리가 데이터 로딩과 캐싱을 관리할 수 있습니다.

예를 들어:

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  defaultPreloadStaleTime: 0,
})
```

이렇게 설정하면, 예를 들어 React Query의 `staleTime` 옵션을 사용해 미리 가져오기 데이터의 신선도를 제어할 수 있습니다.


## 수동으로 미리 가져오기

라우트를 수동으로 미리 가져오려면 라우터의 `preloadRoute` 메서드를 사용할 수 있습니다. 이 메서드는 표준 TanStack `NavigateOptions` 객체를 인자로 받고, 라우트가 미리 가져와지면 resolve되는 Promise를 반환합니다.

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    try {
      const matches = await router.preloadRoute({
        to: postRoute,
        params: { id: 1 },
      })
    } catch (err) {
      // 라우트 미리 가져오기 실패
    }
  }, [])

  return 
}
```

라우트의 JS 청크만 미리 가져오려면 라우터의 `loadRouteChunk` 메서드를 사용할 수 있습니다. 이 메서드는 라우트 객체를 인자로 받고, 라우트 청크가 로드되면 resolve되는 Promise를 반환합니다.

```tsx
function Component() {
  const router = useRouter()

  useEffect(() => {
    try {
      const postsRoute = router.routesByPath['/posts']
      await Promise.all([
        router.loadRouteChunk(router.routesByPath['/']),
        router.loadRouteChunk(postsRoute),
        router.loadRouteChunk(postsRoute.parentRoute),
      ])
    } catch (err) {
      // 라우트 청크 미리 가져오기 실패
    }
  }, [])

  return 
}
```


