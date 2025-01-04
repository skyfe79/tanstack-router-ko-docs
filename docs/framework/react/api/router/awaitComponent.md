# Table of Contents

- [Await 컴포넌트](#await-컴포넌트)
  - [Await 컴포넌트의 Props](#await-컴포넌트의-props)
    - [`props.promise` 속성](#propspromise-속성)
    - [`props.children` prop](#propschildren-prop)
  - [Await의 반환 동작](#await의-반환-동작)
  - [예제](#예제)

# Await 컴포넌트

`Await` 컴포넌트는 제공된 Promise가 해결(resolve)되거나 거부(reject)될 때까지 일시 중단(suspend)하는 컴포넌트입니다.  
이 기능은 React 18에서만 필요합니다.  
React 19를 사용 중이라면, `use()` 훅을 대신 사용할 수 있습니다.


## Await 컴포넌트의 Props

`Await` 컴포넌트는 다음과 같은 props를 받습니다:


### `props.promise` 속성

- 타입: `Promise<T>`
- 필수 여부: 필수
- 설명: 대기할 Promise 객체입니다.


### `props.children` prop

- 타입: `(result: T) => React.ReactNode`
- 필수 여부: 필수
- 설명: Promise가 해결된 값과 함께 호출될 함수입니다.


## Await의 반환 동작

- Promise가 거부(rejected)되면 오류를 발생시킨다.
- Promise가 대기 중(pending)이면 실행을 일시 중단하고 Promise를 던진다.
- Promise가 해결(resolved)되면 `props.children`을 렌더링 함수로 사용하여 지연된(deferred) Promise의 해결된 값을 반환한다.


## 예제

```tsx
import { Await } from '@tanstack/react-router'

function Component() {
  const { deferredPromise } = route.useLoaderData()

  return (
    <Await promise={deferredPromise}>
      {(data) => {JSON.stringify(data)}}
    </Await>
  )
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리에서 제공하는 `Await` 컴포넌트를 사용합니다. `route.useLoaderData()`를 통해 받아온 `deferredPromise`를 `Await` 컴포넌트에 전달합니다. `Await` 컴포넌트는 Promise가 해결될 때까지 기다린 후, 데이터를 받아와서 `JSON.stringify`를 통해 문자열로 변환하여 렌더링합니다.


