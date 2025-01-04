# 라우트 타입 등록

이 타입은 라우터 인스턴스에 라우트 트리를 등록하는 데 사용됩니다. 이를 통해 `@tanstack/react-router` 패키지의 최상위 내보내기를 포함한 TanStack Router의 완전한 타입 안전성을 활용할 수 있습니다.

```tsx
export type Register = {
  // router: [여기에 라우터 타입을 추가하세요]
}
```

라우터 인스턴스에 라우트 트리를 등록하려면, 선언 병합(declaration merging)을 사용하여 라우터 인스턴스의 타입을 `router` 속성 아래에 추가하세요:


## 예제

```tsx
const router = createRouter({
  // ...
})

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```


