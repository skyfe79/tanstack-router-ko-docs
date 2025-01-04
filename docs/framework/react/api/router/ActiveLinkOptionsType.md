# Table of Contents

- [ActiveLinkOptions 타입](#activelinkoptions-타입)
  - [ActiveLinkOptions 속성](#activelinkoptions-속성)
    - [`activeProps`](#activeprops)
    - [`inactiveProps`](#inactiveprops)

# ActiveLinkOptions 타입

`ActiveLinkOptions` 타입은 [`LinkOptions`](./LinkOptionsType.md) 타입을 확장하며, 링크가 활성 상태일 때 스타일을 지정하는 데 사용할 수 있는 추가 옵션을 포함합니다.

```tsx
type ActiveLinkOptions = LinkOptions & {
  activeProps?:
    | React.AnchorHTMLAttributes
    | (() => React.AnchorHTMLAttributes)
  inactiveProps?:
    | React.AnchorHTMLAttributes
    | (() => React.AnchorHTMLAttributes)
}
```

- `activeProps`: 링크가 활성 상태일 때 적용할 속성을 정의합니다. 이는 `React.AnchorHTMLAttributes` 객체이거나, 해당 객체를 반환하는 함수일 수 있습니다.
- `inactiveProps`: 링크가 비활성 상태일 때 적용할 속성을 정의합니다. 이 역시 `React.AnchorHTMLAttributes` 객체이거나, 해당 객체를 반환하는 함수일 수 있습니다.


## ActiveLinkOptions 속성

`ActiveLinkOptions` 객체는 다음과 같은 속성을 포함합니다:


### `activeProps`

- `React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 선택 사항
- 링크가 활성 상태일 때 앵커 엘리먼트에 적용될 속성들


### `inactiveProps`

- 타입: `React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 선택 사항
- 링크가 비활성 상태일 때 앵커 엘리먼트에 적용될 속성들


