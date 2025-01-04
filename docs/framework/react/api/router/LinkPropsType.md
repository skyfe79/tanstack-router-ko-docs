# LinkProps 타입

`LinkProps` 타입은 [`ActiveLinkOptions`](./ActiveLinkOptionsType.md)와 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 타입을 확장하며, `Link` 컴포넌트에 특화된 추가적인 프로퍼티를 포함합니다.

```tsx
type LinkProps = ActiveLinkOptions &
  Omit<React.AnchorHTMLAttributes<HTMLAnchorElement>, 'children'> & {
    children?:
      | React.ReactNode
      | ((state: { isActive: boolean }) => React.ReactNode)
  }
```

- `ActiveLinkOptions`와 `React.AnchorHTMLAttributes<HTMLAnchorElement>`를 결합하여 `LinkProps` 타입을 정의합니다.
- `children` 프로퍼티는 `React.ReactNode` 또는 `{ isActive: boolean }` 상태를 인자로 받는 함수 형태로 정의됩니다.


## LinkProps 속성

- [`ActiveLinkOptions`](./ActiveLinkOptionsType.md)의 모든 속성
- `React.AnchorHTMLAttributes<HTMLAnchorElement>`의 모든 속성


#### `children`

- 타입: `React.ReactNode | ((state: { isActive: boolean }) => React.ReactNode)`
- 선택 사항
- 앵커 엘리먼트 안에 렌더링될 자식 요소입니다. 함수가 제공되면, 링크가 활성 상태인지 여부를 결정하는 데 사용할 수 있는 `isActive` 불리언 값을 포함한 객체와 함께 호출됩니다.


