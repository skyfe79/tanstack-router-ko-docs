# defer 함수

> [!주의]
> 더 이상 `defer`를 직접 호출할 필요가 없습니다. 이제 Promise는 자동으로 처리됩니다.

`defer` 함수는 Promise를 감싸서 **지연된 상태 객체(deferred state object)**를 생성합니다. 이 객체를 사용하면 Promise의 상태를 확인할 수 있습니다. 이렇게 생성된 지연된 Promise는 [`useAwaited`](./useAwaitedHook.md) 훅이나 [`<Await>`](./awaitComponent.md) 컴포넌트에 전달하여, Promise가 해결(resolved)되거나 거부(rejected)될 때까지 대기(suspend)할 수 있습니다.

`defer` 함수는 하나의 인자를 받습니다. 바로 지연된 상태 객체로 감싸고자 하는 `promise`입니다.

```javascript
const deferredPromise = defer(myPromise);
```


## 지연 옵션(defer options)

- 타입: `Promise<T>`
- 필수 여부: 필수
- 설명: 지연 상태 객체로 감싸는 Promise입니다.


## defer 반환값

- [`useAwaited`](./useAwaitedHook.md) 훅이나 [`<Await>`](./awaitComponent.md) 컴포넌트에 전달할 수 있는 Promise를 반환합니다.


## 예제

```tsx
import { defer } from '@tanstack/react-router'

const route = createRoute({
  loader: () => {
    const deferredPromise = defer(fetch('/api/data'))
    return { deferredPromise }
  },
  component: MyComponent,
})

function MyComponent() {
  const { deferredPromise } = Route.useLoaderData()

  const data = useAwaited({ promise: deferredPromise })

  // 또는

  return (
    <div>
      {JSON.stringify(data)}
    </div>
  )
}
```

이 예제에서는 `@tanstack/react-router` 라이브러리를 사용하여 라우트를 생성하고, 비동기 데이터를 처리하는 방법을 보여줍니다. `defer` 함수를 통해 데이터를 비동기적으로 가져오고, `useAwaited` 훅을 사용하여 데이터를 기다린 후 화면에 표시합니다.


