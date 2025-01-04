# Table of Contents

- [링크 옵션](#링크-옵션)
  - [linkOptions props](#linkoptions-props)
    - [`...props`](#props)
  - [`linkOptions` 반환값](#linkoptions-반환값)
  - [예제](#예제)

# 링크 옵션

`linkOptions`는 `Link`, `navigate`, `redirect`와 함께 사용하기 위해 객체 리터럴의 타입을 검사하는 함수입니다.


## linkOptions props

`linkOptions`는 다음과 같은 옵션을 받습니다:


### `...props`

- 타입: `LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md) 참조

`...props`는 `LinkProps`와 `React.RefAttributes<HTMLAnchorElement>`를 합친 타입입니다. 이는 `Link` 컴포넌트에 전달할 수 있는 모든 속성을 포함합니다. `LinkProps`에 대한 자세한 내용은 [LinkProps 문서](./LinkPropsType.md)를 참조하세요.


## `linkOptions` 반환값

입력값으로부터 정확히 추론된 타입을 가진 객체 리터럴을 반환합니다.


## 예제

```tsx
const userLinkOptions = linkOptions({
  to: '/dashboard/users/user',
  search: {
    usersView: {
      sortBy: 'email',
      filterBy: 'filter',
    },
    userId: 0,
  },
})

function DashboardComponent() {
  return 
}
```


