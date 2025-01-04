# Table of Contents

- [정적 라우트 데이터](#정적-라우트-데이터)
  - [예제](#예제)
  - [정적 데이터 강제 적용하기](#정적-데이터-강제-적용하기)
  - [선택적 정적 데이터](#선택적-정적-데이터)

# 정적 라우트 데이터

라우트를 생성할 때, 라우트 옵션에 `staticData` 속성을 선택적으로 지정할 수 있습니다. 이 객체는 라우트를 생성할 때 동기적으로 사용 가능한 모든 데이터를 포함할 수 있습니다.

이 데이터는 라우트 자체에서 접근할 수 있을 뿐만 아니라, `match.staticData` 속성을 통해 매치된 라우트에서도 접근할 수 있습니다.


## 예제

- `posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    customData: 'Hello!',
  },
})
```

이 데이터는 라우트에 접근할 수 있는 모든 곳에서 사용할 수 있습니다. 라우트와 매핑된 매치(match)에서도 접근이 가능합니다.

- `__root.tsx`

```tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => {
    const matches = useMatches()

    return (
      
        {matches.map((match) => {
          return {match.staticData.customData}
        })}
      
    )
  },
})
```


## 정적 데이터 강제 적용하기

라우트에 정적 데이터를 강제로 적용하려면, 선언 병합(declaration merging)을 사용하여 라우트의 정적 옵션에 타입을 추가할 수 있습니다.

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData: string
  }
}
```

이제 `customData` 속성 없이 라우트를 생성하려고 하면 타입 에러가 발생합니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    // 'StaticDataRouteOption' 타입에 필요한 'customData' 속성이 '{ customData: number; }' 타입에 없습니다.ts(2741)
  },
})
```

이 코드는 `customData` 속성이 누락되었을 때 타입 에러를 발생시켜, 정적 데이터를 강제로 적용할 수 있도록 도와줍니다.


## 선택적 정적 데이터

정적 데이터를 선택적으로 만들고 싶다면, 프로퍼티에 `?`를 추가하면 됩니다.

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData?: string
  }
}
```

`StaticDataRouteOption`에 필수 프로퍼티가 하나라도 있다면, 객체를 전달해야 합니다.


