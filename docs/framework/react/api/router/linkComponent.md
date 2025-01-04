# Link 컴포넌트

`Link` 컴포넌트는 새로운 위치로 이동할 수 있는 링크를 생성하는 데 사용됩니다. 이 컴포넌트는 경로(pathname), 검색 파라미터(search params), 해시(hash), 그리고 위치 상태(location state)를 변경할 수 있습니다.


## Link 컴포넌트 속성

`Link` 컴포넌트는 다음과 같은 속성(props)을 받습니다:


### `...props`

- 타입: `LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md)

`...props`는 `LinkProps`와 `React.RefAttributes<HTMLAnchorElement>`를 합친 타입입니다. 이는 `Link` 컴포넌트에 전달할 수 있는 모든 속성을 포함합니다. `LinkProps`에 대한 자세한 내용은 [LinkProps 문서](./LinkPropsType.md)를 참고하세요.


## 링크 반환

새로운 위치로 이동할 수 있는 앵커 엘리먼트입니다.


## 예제

```tsx
import { Link } from '@tanstack/react-router'

function Component() {
  return (
    <Link
      to="/somewhere/$somewhereId"
      params={{ somewhereId: 'baz' }}
      search={(prev) => ({ ...prev, foo: 'bar' })}
    >
      Click me
    </Link>
  )
}
```

이 예제는 `@tanstack/react-router` 라이브러리의 `Link` 컴포넌트를 사용하여 네비게이션 링크를 만드는 방법을 보여줍니다. `to` 속성은 이동할 경로를 지정하고, `params`는 경로에 필요한 파라미터를 전달합니다. `search` 속성은 쿼리 파라미터를 추가하거나 수정하는 데 사용됩니다. 이 경우, `foo`라는 쿼리 파라미터에 `bar` 값을 설정합니다.


