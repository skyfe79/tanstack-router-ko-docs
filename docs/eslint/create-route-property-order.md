# Table of Contents

- [타입 추론에 민감한 속성 순서: createRoute 함수들](#타입-추론에-민감한-속성-순서-createroute-함수들)
  - [규칙 상세 설명](#규칙-상세-설명)
  - [속성](#속성)

# 타입 추론에 민감한 속성 순서: createRoute 함수들

다음 함수들은 타입 추론에 영향을 받기 때문에 전달되는 객체의 속성 순서가 중요합니다:

- `createRoute`
- `createFileRoute`
- `createRootRoute`
- `createRootRouteWithContext`

올바른 속성 순서는 다음과 같습니다:

1. `params`, `validateSearch`
2. `loaderDeps`, `search.middlewares`
3. `context`
4. `beforeLoad`
5. `loader`
6. `onEnter`, `onStay`, `onLeave`, `meta`, `links`, `scripts`, `headers`

이 외의 속성들은 타입 추론에 영향을 받지 않으므로 순서에 민감하지 않습니다.


## 규칙 상세 설명

이 규칙에 위배되는 **잘못된** 코드 예제:

```tsx
/* eslint "@tanstack/router/create-route-property-order": "warn" */
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/foo/bar/$id')({
  loader: async ({context}) => {
    await context.queryClient.ensureQueryData(getQueryOptions(context.hello)),
  },
  beforeLoad: () => ({hello: 'world'})
})
```

이 규칙을 준수하는 **올바른** 코드 예제:

```tsx
/* eslint "@tanstack/router/create-route-property-order": "warn" */
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/foo/bar/$id')({
  beforeLoad: () => ({hello: 'world'}),
  loader: async ({context}) => {
    await context.queryClient.ensureQueryData(getQueryOptions(context.hello)),
  }
})
```


## 속성

- [x] ✅ 권장
- [x] 🔧 수정 가능


