# useLinkProps 훅

`useLinkProps` 훅은 객체를 인자로 받고, `React.AnchorHTMLAttributes<HTMLAnchorElement>` 타입의 props 객체를 반환합니다. 이 props는 앵커 엘리먼트에 안전하게 적용할 수 있으며, 새로운 위치로 네비게이션할 수 있는 링크를 생성하는 데 사용됩니다. 여기에는 경로명(pathname), 검색 파라미터(search params), 해시(hash), 그리고 위치 상태(location state)의 변경이 포함됩니다.


## useLinkProps 옵션

```tsx
type UseLinkPropsOptions = ActiveLinkOptions &
  React.AnchorHTMLAttributes
```

- [`ActiveLinkOptions`](./ActiveLinkOptionsType.md)
- `useLinkProps` 옵션은 [`LinkProps`](./LinkPropsType.md) 객체를 생성하는 데 사용됩니다.
- 이 옵션은 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 타입을 확장하므로, `useLinkProps` 훅에 전달된 추가 속성들은 [`LinkProps`](./LinkPropsType.md) 객체와 병합됩니다.


## useLinkProps 반환값

- `React.AnchorHTMLAttributes<HTMLAnchorElement>` 타입의 객체를 반환합니다. 이 객체는 앵커 엘리먼트에 적용하여 새로운 위치로 이동할 수 있는 링크를 생성하는 데 사용할 수 있습니다.


