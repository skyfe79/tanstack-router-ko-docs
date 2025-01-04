# useAwaited 훅

`useAwaited` 메서드는 제공된 Promise가 해결(resolve)되거나 거부(reject)될 때까지 실행을 일시 중단하는 훅입니다.


## useAwaited 옵션

`useAwaited` 훅은 하나의 인자를 받습니다. 이 인자는 `options` 객체입니다.


### `options.promise` 옵션

- 타입: `Promise<T>`
- 필수 여부: 필수
- 설명: 대기할 지연된 Promise입니다.


## useAwaited 반환값

- Promise가 거부(rejected)되면 오류를 발생시킵니다.
- Promise가 대기 중(pending)이면 일시 중단(suspends)됩니다. (Promise를 throw 함)
- 지연된(deferred) Promise가 해결(resolved)되면, 해결된 값을 반환합니다.


## 예제

```tsx
import { useAwaited } from '@tanstack/react-router'

function Component() {
  const { deferredPromise } = route.useLoaderData()

  const data = useAwaited({ promise: myDeferredPromise })
  // ...
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리의 `useAwaited` 훅을 사용하여 비동기 데이터를 처리하는 방법을 보여줍니다. `route.useLoaderData()`를 통해 얻은 `deferredPromise`를 `useAwaited` 훅에 전달하여 데이터를 가져옵니다. 이렇게 하면 비동기 작업이 완료될 때까지 기다린 후 데이터를 사용할 수 있습니다.


