# Outlet 컴포넌트

`Outlet` 컴포넌트는 부모 라우트의 다음 자식 라우트를 렌더링할 때 사용하는 컴포넌트입니다.


## Outlet 컴포넌트의 props

`Outlet` 컴포넌트는 어떤 props도 받지 않습니다.


## Outlet 반환값

- **일치하는 경우**: 자식 라우트 매치의 `component`/`errorComponent`/`pendingComponent`/`notFoundComponent`를 반환합니다.
- **일치하지 않는 경우**: `null`을 반환합니다.


