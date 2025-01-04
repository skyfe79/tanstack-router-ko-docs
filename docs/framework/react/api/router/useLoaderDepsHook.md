# useLoaderDeps 훅

`useLoaderDeps` 훅은 특정 라우트의 `loader`를 트리거하는 데 사용되는 의존성을 담은 객체를 반환하는 훅입니다.


## useLoaderDepsHook 옵션

`useLoaderDepsHook` 훅은 `options` 객체를 인자로 받습니다.


### `opts.from` 옵션

- 타입: `string`
- 필수 여부: 필수
- 설명: 로더 의존성을 가져올 라우트 ID 또는 경로를 지정합니다.


### `opts.select` 옵션

- 타입: `(deps: TLoaderDeps) => TSelected`
- 선택 사항
- 이 함수가 제공되면, 로더 의존성 객체(`deps`)를 인자로 받아 호출됩니다. 반환된 값은 `useLoaderDeps`에서 반환됩니다.


### `opts.structuralSharing` 옵션

- 타입: `boolean`
- 선택 사항
- `select` 함수가 반환하는 값에 대해 **구조적 공유(structural sharing)**를 활성화할지 여부를 설정합니다.
- 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.


## useLoaderDeps 반환값

- 로더 의존성을 담은 객체를 반환합니다. 만약 `select` 함수가 제공된 경우, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useLoaderDeps } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  const deps = useLoaderDeps({ from: '/posts/$postId' })

  // 또는

  const routeDeps = routeApi.useLoaderDeps()

  // 또는

  const postId = useLoaderDeps({
    from: '/posts',
    select: (deps) => deps.view,
  })

  // ...
}
```


