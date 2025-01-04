# Table of Contents

- [아웃렛(Outlets)](#아웃렛outlets)
  - [`Outlet` 컴포넌트](#outlet-컴포넌트)

# 아웃렛(Outlets)

중첩 라우팅은 라우트가 다른 라우트 안에 중첩될 수 있음을 의미합니다. 이때, 중첩된 콘텐츠를 어디에 렌더링할지 어떻게 지정할까요?


## `Outlet` 컴포넌트

`Outlet` 컴포넌트는 다음으로 매칭될 가능성이 있는 자식 라우트를 렌더링하는 데 사용됩니다. `<Outlet />`은 어떤 인자도 받지 않으며, 라우트의 컴포넌트 트리 내 어디에서든 렌더링할 수 있습니다. 만약 매칭되는 자식 라우트가 없다면, `<Outlet />`은 `null`을 렌더링합니다.

> [!TIP]
> 라우트의 `component`가 정의되지 않은 경우, 자동으로 `<Outlet />`을 렌더링합니다.

이를 잘 보여주는 예로, 애플리케이션의 루트 라우트를 설정하는 경우를 들 수 있습니다. 루트 라우트에 제목을 렌더링하는 컴포넌트를 추가하고, 최상위 라우트들이 렌더링될 수 있도록 `<Outlet />`을 배치해 보겠습니다.

```tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: RootComponent,
})

function RootComponent() {
  return (
    <div>
      <h1>My App</h1>
      {/* 여기에 자식 라우트들이 렌더링됩니다 */}
      <Outlet />
    </div>
  )
}
```

이 예제에서 `RootComponent`는 제목을 표시하고, 그 아래에 `<Outlet />`을 통해 자식 라우트들이 렌더링될 공간을 마련합니다. 이렇게 하면 루트 라우트와 그 자식 라우트들이 자연스럽게 연결되어 화면에 표시됩니다.


