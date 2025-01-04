# Table of Contents

- [개발자 도구(Devtools)](#개발자-도구devtools)
  - [설치](#설치)
  - [Devtools 가져오기](#devtools-가져오기)
  - [개발 환경에서만 Devtools 임포트 및 사용하기](#개발-환경에서만-devtools-임포트-및-사용하기)
  - [`RouterProvider` 내부에서 사용하기](#routerprovider-내부에서-사용하기)
  - [라우터 인스턴스를 수동으로 전달하기](#라우터-인스턴스를-수동으로-전달하기)
  - [플로팅 모드](#플로팅-모드)
    - [옵션](#옵션)
  - [임베디드 모드](#임베디드-모드)

# 개발자 도구(Devtools)

> 이 검을 받아라... 아니, 개발자 도구를 받아라! 여정을 도와줄 것이다!

TanStack Router는 전용 개발자 도구를 제공합니다. 기쁜 마음으로 손을 흔들며 환호하세요! 🥳

TanStack Router를 시작할 때, 이 개발자 도구가 여러분 곁에 있으면 좋습니다. 이 도구는 TanStack Router의 내부 동작을 시각적으로 보여주며, 문제가 생겼을 때 디버깅 시간을 크게 줄여줄 것입니다.

> 현재는 **React에서만 개발자 도구를 지원**합니다. 다른 플랫폼에서도 사용할 수 있도록 도움을 주고 싶다면, 저희에게 알려주세요!


## 설치

devtools는 별도의 패키지로 설치해야 합니다:

```sh
npm install -D @tanstack/router-devtools
```

또는

```sh
pnpm add -D @tanstack/router-devtools
```

또는

```sh
yarn add -D @tanstack/router-devtools
```

또는

```sh
bun add -D @tanstack/router-devtools
```


## Devtools 가져오기

```js
import { TanStackRouterDevtools } from '@tanstack/router-devtools'
```


## 개발 환경에서만 Devtools 임포트 및 사용하기

이 기능을 사용하려면 `React.lazy`와 환경 변수를 활용해 프로덕션 환경에서는 더미 컴포넌트를 반환하도록 설정하면 됩니다.

```tsx
const TanStackRouterDevtools =
  process.env.NODE_ENV === 'production'
    ? () => null // 프로덕션 환경에서는 아무것도 렌더링하지 않음
    : React.lazy(() =>
        // 개발 환경에서 지연 로딩
        import('@tanstack/router-devtools').then((res) => ({
          default: res.TanStackRouterDevtools,
          // 임베디드 모드 사용 시
          // default: res.TanStackRouterDevtoolsPanel
        })),
      )
```

그런 다음 `TanStackRouterDevtools` 컴포넌트를 `Suspense`로 감싸줍니다.

```tsx
<Suspense fallback={null}>
  <TanStackRouterDevtools />
</Suspense>
```


## `RouterProvider` 내부에서 사용하기

개발자 도구를 가장 쉽게 사용하는 방법은 루트 라우트(또는 다른 라우트) 내부에 렌더링하는 것입니다. 이렇게 하면 개발자 도구가 자동으로 라우터 인스턴스에 연결됩니다.

```tsx
const rootRoute = createRootRoute({
  component: () => (
    <>
      {/* 개발자 도구를 여기에 렌더링 */}
    </>
  ),
})

const routeTree = rootRoute.addChildren([
  // ... 다른 라우트들
])

const router = createRouter({
  routeTree,
})

function App() {
  return (
    <RouterProvider router={router} />
  )
}
```

이 코드는 루트 라우트를 생성하고, 다른 라우트들을 추가한 후, 라우터를 초기화합니다. 마지막으로 `App` 컴포넌트에서 `RouterProvider`를 사용하여 라우터를 제공합니다.


## 라우터 인스턴스를 수동으로 전달하기

`RouterProvider` 내부에서 개발자 도구를 렌더링하는 것이 마음에 들지 않는다면, 개발자 도구의 `router` prop에 `Router` 컴포넌트에 전달하는 것과 동일한 `router` 인스턴스를 전달할 수 있습니다. 이를 통해 개발자 도구를 페이지 어디에나 배치할 수 있으며, 프로바이더 내부에만 제한되지 않습니다:

```tsx
function App() {
  return (
    <>
      {/* 개발자 도구를 원하는 위치에 배치 */}
      <DevTools router={router} />
      <RouterProvider router={router} />
    </>
  )
}
```

이 방식은 개발자 도구를 더 유연하게 배치할 수 있게 해줍니다. 예를 들어, 페이지의 상단이나 하단에 배치하거나, 특정 레이아웃 내부에 배치할 수 있습니다.


## 플로팅 모드

플로팅 모드는 개발자 도구를 앱 내 고정된 플로팅 엘리먼트로 마운트합니다. 화면 모서리에 토글 버튼을 제공해 개발자 도구를 보이거나 숨길 수 있습니다. 이 토글 상태는 localStorage에 저장되어 페이지를 새로고침해도 유지됩니다.

아래 코드를 여러분의 React 앱에서 가능한 한 상위에 배치하세요. 페이지의 루트에 가까울수록 더 잘 작동합니다!

```js
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

function App() {
  return (
    <>
      {/* 앱 내용 */}
      <TanStackRouterDevtools />
    </>
  )
}
```


### 옵션

개발자 도구를 스타일링하기 위해 사용할 수 있는 옵션입니다.

- `style: StyleObject`
  - 인라인 스타일로 컴포넌트를 스타일링할 때 사용하는 표준 React 스타일 객체
- `className: string`
  - 클래스를 사용해 컴포넌트를 스타일링할 때 사용하는 표준 React className 속성


## 임베디드 모드

임베디드 모드를 사용하면 개발자 도구를 여러분의 애플리케이션에 일반 컴포넌트로 삽입할 수 있습니다. 이후 원하는 대로 스타일을 적용할 수 있습니다!

```js
import { TanStackRouterDevtoolsPanel } from '@tanstack/router-devtools'

function App() {
  return (
    <>
      <TanStackRouterDevtoolsPanel
        router={router}
        style={styles}
        className={className}
      />
    </>
  )
}
```


