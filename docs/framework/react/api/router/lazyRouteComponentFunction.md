# lazyRouteComponent 함수

> [!중요]
> 파일 기반 라우팅을 사용한다면, `createLazyFileRoute` 함수를 사용하는 것을 권장합니다.

`lazyRouteComponent` 함수는 코드 분할된 라우트 컴포넌트를 생성할 때 사용됩니다. 이 컴포넌트는 `component.preload()` 메서드를 통해 미리 로드할 수 있습니다.


## lazyRouteComponent 옵션

`lazyRouteComponent` 함수는 두 개의 인자를 받습니다:


### `importer` 옵션

- 타입: `() => Promise<T>`
- 필수 여부: 필수
- 설명: 컴포넌트를 로드할 때 사용되는 객체를 반환하는 Promise를 반환하는 함수입니다.


### `exportName` 옵션

- 타입: `string`
- 선택 사항
- 불러온 객체에서 로드할 컴포넌트의 이름. 기본값은 `'default'`입니다.


## lazyRouteComponent 반환값

- `React.lazy` 컴포넌트를 반환하며, `component.preload()` 메서드를 사용해 미리 로드할 수 있습니다.


## 예제

```tsx
import { lazyRouteComponent } from '@tanstack/react-router'

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(() => import('./Post')), // 기본 내보내기
})

// 또는

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(
    () => import('./Post'),
    'PostByIdPageComponent', // 이름 있는 내보내기
  ),
})
```


