# Table of Contents

- [RouteApi 타입](#routeapi-타입)
  - [`RouteApi` 속성과 메서드](#routeapi-속성과-메서드)
    - [`useMatch` 메서드](#usematch-메서드)
    - [`useRouteContext` 메서드](#useroutecontext-메서드)
    - [`useSearch` 메서드](#usesearch-메서드)
    - [`useParams` 메서드](#useparams-메서드)
    - [`useLoaderData` 메서드](#useloaderdata-메서드)
    - [`useLoaderDeps` 메서드](#useloaderdeps-메서드)
    - [`useNavigate` 메서드](#usenavigate-메서드)

# RouteApi 타입

`RouteApi`는 특정 라우트 ID와 등록된 라우트 타입에 미리 바인딩된 `useParams`, `useSearch`, `useRouteContext`, `useNavigate`, `useLoaderData`, `useLoaderDeps`와 같은 일반적인 훅의 타입 안전 버전을 제공하는 인스턴스를 설명합니다.


## `RouteApi` 속성과 메서드

`RouteApi`는 다음과 같은 속성과 메서드를 가지고 있습니다:


### `useMatch` 메서드

```tsx
useMatch(opts?: {
  select?: (match: TAllContext) => TSelected
}): TSelected
```

- `useMatch` 훅의 타입 안전 버전으로, `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩되어 있습니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: RouteMatch) => TSelected`
    - 이 함수가 제공되면, 라우트 매치와 함께 호출되며, 반환된 값이 `useMatch`에서 반환됩니다. 이 값은 또한 얕은 동등성 검사를 사용하여 부모 컴포넌트를 리렌더링할지 여부를 결정하는 데 사용됩니다.
  - `opts.structuralSharing`
    - 선택 사항
    - `boolean`
    - `select`에 의해 반환된 값에 대해 구조적 공유가 활성화되었는지 여부를 설정합니다.
    - 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참조하세요.
- 반환 값
  - `select` 함수가 제공된 경우, `select` 함수의 반환 값.
  - `select` 함수가 제공되지 않은 경우, `RouteMatch` 객체 또는 `opts.strict`가 `false`인 경우 완화된 버전의 `RouteMatch` 객체.


### `useRouteContext` 메서드

```tsx
useRouteContext(opts?: {
  select?: (search: TAllContext) => TSelected
}): TSelected
```

- `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩된 타입 안전 버전의 [`useRouteContext`](./useRouteContextHook.md) 훅입니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: RouteContext) => TSelected`
    - 이 함수가 제공되면, 라우트 매칭 정보와 함께 호출되며, 반환된 값이 `useRouteContext`에서 반환됩니다. 이 값은 또한 얕은 비교(shallow equality)를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.
- 반환값
  - `select` 함수가 제공된 경우, `select` 함수의 반환값입니다.
  - `select` 함수가 제공되지 않은 경우, `RouteContext` 객체 또는 `opts.strict`가 `false`인 경우 완화된 버전의 `RouteContext` 객체입니다.


### `useSearch` 메서드

```tsx
useSearch(opts?: {
  select?: (search: TFullSearchSchema) => TSelected
}): TSelected
```

- `useSearch` 훅의 타입 안전 버전으로, `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩되어 있습니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: TFullSearchSchema) => TSelected`
    - 이 함수가 제공되면, 라우트 매치와 함께 호출되며, 반환된 값이 `useSearch`에서 반환됩니다. 이 값은 또한 얕은 동등성 검사를 사용해 부모 컴포넌트를 리렌더링할지 결정하는 데 사용됩니다.
  - `opts.structuralSharing`
    - 선택 사항
    - `boolean`
    - `select`에 의해 반환된 값에 대해 구조적 공유가 활성화되었는지 여부를 설정합니다.
    - 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.
- 반환값
  - `select` 함수가 제공된 경우, `select` 함수의 반환값입니다.
  - `select` 함수가 제공되지 않은 경우, `TFullSearchSchema` 객체 또는 `opts.strict`가 `false`인 경우 완화된 버전의 `TFullSearchSchema` 객체입니다.


### `useParams` 메서드

```tsx
useParams(opts?: {
  select?: (params: TAllParams) => TSelected
}): TSelected
```

- `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩된 타입 안전 버전의 [`useParams`](./useParamsHook.md) 훅입니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: TAllParams) => TSelected`
    - 이 함수가 제공되면, 라우트 매칭 결과와 함께 호출되며, 반환된 값이 `useParams`에서 반환됩니다. 이 값은 또한 얕은 동등성 검사를 통해 부모 컴포넌트의 리렌더링 여부를 결정하는 데 사용됩니다.
  - `opts.structuralSharing`
    - 선택 사항
    - `boolean`
    - `select` 함수가 반환한 값에 대해 구조적 공유(structural sharing)가 활성화될지 여부를 설정합니다.
    - 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.
- 반환 값
  - `select` 함수가 제공된 경우, `select` 함수의 반환 값입니다.
  - `select` 함수가 제공되지 않은 경우, `TAllParams` 객체 또는 `opts.strict`가 `false`인 경우 완화된 버전의 `TAllParams` 객체입니다.


### `useLoaderData` 메서드

```tsx
useLoaderData(opts?: {
  select?: (search: TLoaderData) => TSelected
}): TSelected
```

- `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩된 타입 안전 버전의 [`useLoaderData`](./useLoaderDataHook.md) 훅입니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: TLoaderData) => TSelected`
    - 이 함수가 제공되면, 라우트 매치와 함께 호출되며, 반환된 값이 `useLoaderData`에서 반환됩니다. 이 값은 또한 얕은 동등성 검사를 사용하여 부모 컴포넌트를 리렌더링할지 여부를 결정하는 데 사용됩니다.
  - `opts.structuralSharing`
    - 선택 사항
    - `boolean`
    - `select`에 의해 반환된 값에 대해 구조적 공유가 활성화되었는지 여부를 설정합니다.
    - 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참조하세요.
- 반환 값
  - `select` 함수가 제공된 경우, `select` 함수의 반환 값.
  - `select` 함수가 제공되지 않은 경우, `TLoaderData` 객체 또는 `opts.strict`가 `false`인 경우 완화된 버전의 `TLoaderData` 객체.


### `useLoaderDeps` 메서드

```tsx
useLoaderDeps(opts?: {
  select?: (search: TLoaderDeps) => TSelected
}): TSelected
```

- `useLoaderDeps` 훅의 타입 안전 버전으로, `RouteApi` 인스턴스가 생성된 라우트 ID에 미리 바인딩되어 있습니다.
- 옵션
  - `opts.select`
    - 선택 사항
    - `(match: TLoaderDeps) => TSelected`
    - 이 함수가 제공되면, 라우트 매칭 정보와 함께 호출되며, 반환된 값이 `useLoaderDeps`에서 반환됩니다.
  - `opts.structuralSharing`
    - 선택 사항
    - `boolean`
    - `select` 함수가 반환한 값에 대해 구조적 공유(structural sharing)가 활성화될지 여부를 설정합니다.
    - 자세한 내용은 [렌더링 최적화 가이드](../../guide/render-optimizations.md)를 참고하세요.
- 반환값
  - `select` 함수가 제공된 경우, `select` 함수의 반환값을 반환합니다.
  - `select` 함수가 제공되지 않은 경우, `TLoaderDeps` 객체를 반환합니다.


### `useNavigate` 메서드

```tsx
useNavigate(): // 네비게이션 함수
```

- `RouteApi` 인스턴스가 생성될 때 지정된 라우트 ID에 미리 바인딩된 타입 안전 버전의 [`useNavigate`](./useNavigateHook.md)입니다.


