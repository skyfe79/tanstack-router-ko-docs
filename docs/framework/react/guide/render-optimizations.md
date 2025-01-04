# 렌더링 최적화

TanStack Router는 여러분의 컴포넌트가 필요할 때만 리렌더링되도록 보장하기 위해 다양한 최적화 기능을 제공합니다. 이러한 최적화에는 다음이 포함됩니다:


## 구조적 공유(Structural Sharing)

TanStack Router는 리렌더링 사이에 가능한 많은 참조를 유지하기 위해 **구조적 공유**라는 기술을 사용합니다. 이 기법은 특히 URL에 저장된 검색 파라미터와 같은 상태에 유용합니다.

예를 들어, `foo`와 `bar` 두 개의 검색 파라미터를 가진 `details` 라우트가 있다고 가정해 보겠습니다. 이 라우트는 다음과 같이 접근할 수 있습니다:

```tsx
const search = Route.useSearch()
```

만약 `/details?foo=f1&bar=b1`에서 `/details?foo=f1&bar=b2`로 네비게이션할 때 `bar`만 변경된다면, `search.foo`는 참조적으로 안정적으로 유지되고 `search.bar`만 교체됩니다.


## 세밀한 선택자

`useRouterState`, `useSearch`와 같은 다양한 훅을 사용해 라우터 상태에 접근하고 구독할 수 있습니다. 특정 컴포넌트가 검색 파라미터의 일부와 같은 라우터 상태의 특정 부분이 변경될 때만 리렌더링되도록 하려면, `select` 속성을 사용해 부분 구독을 설정할 수 있습니다.

```tsx
// `bar`가 변경될 때는 컴포넌트가 리렌더링되지 않음
const foo = Route.useSearch({ select: ({ foo }) => foo })
```


### 세밀한 선택자와 구조적 공유

`select` 함수는 라우터 상태에 대해 다양한 계산을 수행할 수 있으며, 객체와 같은 다양한 타입의 값을 반환할 수 있습니다. 예를 들어:

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
})
```

이 코드는 동작하지만, `select`가 호출될 때마다 새로운 객체를 반환하기 때문에 컴포넌트가 매번 리렌더링됩니다.

이러한 리렌더링 문제를 피하려면 앞서 설명한 **구조적 공유(structural sharing)**를 사용할 수 있습니다. 기본적으로 구조적 공유는 이전 버전과의 호환성을 유지하기 위해 비활성화되어 있지만, v2에서는 변경될 수 있습니다.

세밀한 선택자에 대해 구조적 공유를 활성화하려면 두 가지 방법이 있습니다:


#### 라우터 옵션에서 기본적으로 활성화하기:

```tsx
const router = createRouter({
  routeTree,
  defaultStructuralSharing: true,
})
```

여러분은 라우터를 생성할 때 `defaultStructuralSharing` 옵션을 `true`로 설정하여 기본적으로 구조적 공유를 활성화할 수 있습니다. 이렇게 하면 라우터가 자동으로 구조적 공유를 사용하게 됩니다.


#### 훅 사용 시 다음과 같이 활성화할 수 있습니다:

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
  structuralSharing: true,
})
```

> [!중요]
> 구조적 공유는 JSON 호환 데이터에서만 작동합니다. 즉, 구조적 공유가 활성화된 상태에서는 `select`를 사용해 클래스 인스턴스와 같은 항목을 반환할 수 없습니다.

TanStack Router의 타입 안전 설계에 따라, 다음과 같은 코드를 작성하면 TypeScript가 오류를 발생시킵니다:

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      date: new Date(),
    }
  },
  structuralSharing: true,
})
```

만약 라우터 옵션에서 구조적 공유가 기본적으로 활성화되어 있다면, `structuralSharing: false`를 설정해 이 오류를 방지할 수 있습니다.


