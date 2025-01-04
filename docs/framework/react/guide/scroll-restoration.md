# 스크롤 복원

스크롤 복원은 사용자가 이전 페이지로 돌아갈 때 해당 페이지의 스크롤 위치를 복원하는 과정입니다. 일반적인 HTML 기반 웹사이트에서는 기본적으로 제공되는 기능이지만, SPA(Single Page Application)에서는 이를 구현하기가 까다로울 수 있습니다. 그 이유는 다음과 같습니다:

- SPA는 보통 `history.pushState` API를 사용해 네비게이션을 처리하기 때문에, 브라우저가 스크롤 위치를 자동으로 복원하지 못합니다.
- SPA는 비동기적으로 콘텐츠를 렌더링하는 경우가 많아, 브라우저가 페이지의 높이를 렌더링 전에 알 수 없습니다.
- SPA는 특정 레이아웃이나 기능을 구현하기 위해 중첩된 스크롤 가능한 컨테이너를 사용하기도 합니다.

뿐만 아니라, 애플리케이션 내에서 단순히 `body` 영역뿐만 아니라 여러 스크롤 가능한 영역이 존재하는 경우도 흔합니다. 예를 들어, 채팅 애플리케이션에서는 스크롤 가능한 사이드바와 채팅 영역이 각각 존재할 수 있습니다. 이 경우, 두 영역의 스크롤 위치를 독립적으로 복원해야 합니다.

이 문제를 해결하기 위해, TanStack Router는 스크롤 복원을 위한 컴포넌트와 훅을 제공합니다. 이를 통해 스크롤 위치를 모니터링하고, 캐싱하며, 복원하는 과정을 자동으로 처리할 수 있습니다.

TanStack Router의 스크롤 복원 기능은 다음과 같이 동작합니다:

- DOM에서 스크롤 이벤트를 모니터링합니다.
- 스크롤 가능한 영역을 스크롤 복원 캐시에 등록합니다.
- 적절한 라우터 이벤트를 감지해 스크롤 위치를 캐싱하거나 복원할 시점을 파악합니다.
- 각 스크롤 가능한 영역(`window`와 `body` 포함)의 스크롤 위치를 캐시에 저장합니다.
- 네비게이션이 성공적으로 완료된 후, DOM이 그려지기 전에 스크롤 위치를 복원합니다.

이 과정이 복잡해 보일 수 있지만, 실제로는 매우 간단합니다:

```tsx
import { ScrollRestoration } from '@tanstack/react-router'

function Root() {
  return (
    <>
      <ScrollRestoration />
    </>
  )
}
```

`ScrollRestoration` 컴포넌트를 애플리케이션의 루트에 렌더링하거나, `useScrollRestoration` 훅을 사용하기만 하면 나머지는 자동으로 처리됩니다!


## 커스텀 캐시 키

Remix의 스크롤 복원 API를 따라가면서, `getKey` 옵션을 사용하여 특정 스크롤 가능 영역의 스크롤 위치를 캐시하는 데 사용되는 키를 커스터마이징할 수 있습니다. 예를 들어, 사용자의 브라우저 기록과 상관없이 동일한 스크롤 위치를 강제로 사용하도록 설정할 수 있습니다.

`getKey` 옵션은 TanStack Router에서 관련 `Location` 상태를 받아들이며, 해당 상태에 대한 스크롤 측정값을 고유하게 식별할 수 있는 문자열을 반환해야 합니다.

기본 `getKey`는 `(location) => location.state.key!`로 설정되어 있으며, 여기서 `key`는 기록의 각 항목에 대해 생성된 고유 키입니다.


## 예제

경로(pathname)에 따라 스크롤을 동기화할 수 있습니다:

```tsx
import { ScrollRestoration } from '@tanstack/react-router'

function Root() {
  return (
    <>
      <ScrollRestoration getKey={(location) => location.pathname} />
    </>
  )
}
```

특정 경로에 대해서만 조건부로 동기화하고, 나머지 경로에는 키를 사용할 수도 있습니다:

```tsx
import { ScrollRestoration } from '@tanstack/react-router'

function Root() {
  return (
    <>
      <ScrollRestoration
        getKey={(location) => {
          const paths = ['/', '/chat']
          return paths.includes(location.pathname)
            ? location.pathname
            : location.state.key!
        }}
      />
    </>
  )
}
```


## 스크롤 복원 방지하기

때로는 스크롤 복원이 일어나지 않도록 막고 싶을 수 있습니다. 이를 위해 다음과 같은 API에서 제공하는 `resetScroll` 옵션을 활용할 수 있습니다:

- `<Link resetScroll={false}>`
- `navigate({ resetScroll: false })`
- `redirect({ resetScroll: false })`

`resetScroll`을 `false`로 설정하면, 다음 네비게이션에서 스크롤 위치가 복원되지 않습니다. (스택에 있는 기존 히스토리 이벤트로 이동하는 경우) 또는 스크롤이 맨 위로 초기화되지 않습니다. (스택에 새로운 히스토리 이벤트를 추가하는 경우)


## 수동 스크롤 복원

대부분의 경우, 스크롤 복원이 작동하도록 특별한 작업을 할 필요가 없습니다. 하지만 **가상화된 리스트**와 같은 경우에는 수동으로 스크롤 복원을 제어해야 할 때가 있습니다.

수동으로 스크롤 복원을 제어하려면 `useElementScrollRestoration` 훅과 `data-scroll-restoration-id` DOM 속성을 사용할 수 있습니다:

```tsx
function Component() {
  // 특정 엘리먼트에 대한 수동 스크롤 복원을 위해 고유한 ID가 필요합니다.
  // 이 ID는 앱 전체에서 가능한 한 고유해야 합니다.
  const scrollRestorationId = 'myVirtualizedContent'

  // 이 ID를 사용하여 해당 엘리먼트의 스크롤 항목을 가져옵니다.
  const scrollEntry = useElementScrollRestoration({
    id: scrollRestorationId,
  })

  // TanStack Virtual을 사용하여 콘텐츠를 가상화해 보겠습니다!
  const virtualizerParentRef = React.useRef(null)
  const virtualizer = useVirtualizer({
    count: 10000,
    getScrollElement: () => virtualizerParentRef.current,
    estimateSize: () => 100,
    // 스크롤 복원 항목의 scrollY를 가상화기의 초기 오프셋으로 전달합니다.
    initialOffset: scrollEntry?.scrollY,
  })

  return (
    <div
      ref={virtualizerParentRef}
      // 스크롤 복원 ID를 엘리먼트에 커스텀 속성으로 전달합니다.
      // 이 속성은 스크롤 복원 감시자에 의해 자동으로 감지됩니다.
      data-scroll-restoration-id={scrollRestorationId}
      className="flex-1 border rounded-lg overflow-auto relative"
    >
      ...
    </div>
  )
}
```

이 코드는 가상화된 리스트에서 스크롤 위치를 복원하는 방법을 보여줍니다. `useElementScrollRestoration` 훅을 사용하여 스크롤 위치를 관리하고, `data-scroll-restoration-id` 속성을 통해 해당 엘리먼트를 식별합니다. 이를 통해 사용자가 페이지를 이동했다가 돌아왔을 때 이전 스크롤 위치를 정확히 복원할 수 있습니다.


## 스크롤 동작 설정

페이지 간 네비게이션 시 스크롤 동작을 제어하려면 `scrollBehavior` 옵션을 사용할 수 있습니다. 이 옵션을 통해 페이지 전환 시 부드러운 스크롤 대신 즉각적인 이동을 설정할 수 있습니다. 스크롤 복원 동작의 전역 설정은 브라우저에서 지원하는 옵션과 동일하며, `smooth`, `instant`, `auto` 중 선택할 수 있습니다. 자세한 내용은 [MDN 문서](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView#behavior)를 참고하세요.

```tsx
import { ScrollRestoration } from '@tanstack/react-router'

function Root() {
  return (
    <>
      <ScrollRestoration />
    </>
  )
}
```


