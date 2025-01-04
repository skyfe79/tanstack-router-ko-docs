# Table of Contents

- [링크 옵션 재사용](#링크-옵션-재사용)
    - [`linkOptions` 함수를 사용하여 재사용 가능한 옵션 만들기](#linkoptions-함수를-사용하여-재사용-가능한-옵션-만들기)
    - [`linkOptions` 배열](#linkoptions-배열)

# 링크 옵션 재사용

`Link`, `redirect`, `navigate`에 전달하려는 옵션을 재사용하고 싶을 수 있습니다. 이 경우, `Link`에 전달되는 옵션을 객체 리터럴로 표현하는 것이 좋은 방법일 수 있습니다.

```tsx
const dashboardLinkOptions = {
  to: '/dashboard',
  search: { search: '' },
}

function DashboardComponent() {
  return 
}
```

여기에는 몇 가지 문제가 있습니다. `dashboardLinkOptions.to`는 기본적으로 `string`으로 추론되며, 이는 `Link`, `navigate`, `redirect`에 전달될 때 모든 라우트로 해석됩니다. (이 문제는 `as const`를 사용하여 해결할 수 있습니다.) 또 다른 문제는 `dashboardLinkOptions`가 `Link`에 전달되기 전까지 타입 검사를 통과하는지 알 수 없다는 점입니다. 잘못된 네비게이션 옵션을 쉽게 만들 수 있으며, 옵션이 `Link`에 전달될 때만 타입 오류를 알게 됩니다.


### `linkOptions` 함수를 사용하여 재사용 가능한 옵션 만들기

`linkOptions`는 객체 리터럴의 타입을 검사하고, 입력된 값을 그대로 반환하는 함수입니다. 이 함수는 `Link`를 사용하기 전에 옵션에 대한 타입 안전성을 제공하여 유지보수와 재사용성을 높여줍니다. 위의 예제를 `linkOptions`를 사용하여 작성하면 다음과 같습니다:

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

function DashboardComponent() {
  return 
}
```

이렇게 하면 `dashboardLinkOptions`의 타입을 미리 검사할 수 있으며, 이 옵션을 어디서든 재사용할 수 있습니다.

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

export const Route = createFileRoute('/dashboard')({
  component: DashboardComponent,
  validateSearch: (input) => ({ search: input.search }),
  beforeLoad: () => {
    // 리다이렉트에서 사용 가능
    throw redirect(dashboardLinkOptions)
  },
})

function DashboardComponent() {
  const navigate = useNavigate()

  return (
    
      {/** 네비게이션에서 사용 가능 */}
       navigate(dashboardLinkOptions)} />

      {/** Link에서 사용 가능 */}
      
    
  )
}
```

이 예제에서 `dashboardLinkOptions`는 리다이렉트, 네비게이션, 그리고 `Link` 컴포넌트에서 모두 사용할 수 있습니다. 이를 통해 코드의 재사용성과 타입 안전성을 동시에 확보할 수 있습니다.


### `linkOptions` 배열

네비게이션 바를 만들 때 배열을 순회하며 구성할 수 있습니다. 이때 `linkOptions`를 사용하면 `Link` 컴포넌트의 props로 사용될 객체 리터럴 배열의 타입을 검사할 수 있습니다.

```tsx
const options = linkOptions([
  {
    to: '/dashboard',
    label: 'Summary',
    activeOptions: { exact: true },
  },
  {
    to: '/dashboard/invoices',
    label: 'Invoices',
  },
  {
    to: '/dashboard/users',
    label: 'Users',
  },
])

function DashboardComponent() {
  return (
    <>
      
        Dashboard
      

      
        {options.map((option) => {
          return (
            <Link
              {...option}
              key={option.to}
              activeProps={{ className: `font-bold` }}
              className="p-2"
            >
              {option.label}
            
          )
        })}
      
      

      
    
  )
}
```

`linkOptions`의 입력은 추론되어 반환됩니다. 예를 들어 `label`은 `Link` 컴포넌트의 props에 존재하지 않지만, 이렇게 사용할 수 있습니다.


