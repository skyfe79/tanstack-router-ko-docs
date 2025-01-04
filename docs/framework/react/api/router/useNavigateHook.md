# Table of Contents

- [useNavigate 훅](#usenavigate-훅)
  - [useNavigate 옵션](#usenavigate-옵션)
    - [`opts.from` 옵션](#optsfrom-옵션)
  - [useNavigate 반환값](#usenavigate-반환값)
  - [navigate 함수](#navigate-함수)
    - [navigate 함수 옵션](#navigate-함수-옵션)
    - [navigate 함수 반환값](#navigate-함수-반환값)
  - [예제](#예제)

# useNavigate 훅

`useNavigate` 훅은 새로운 위치로 이동할 수 있는 `navigate` 함수를 반환하는 훅입니다. 이 함수는 경로명(pathname), 검색 파라미터(search params), 해시(hash), 그리고 위치 상태(location state)를 변경하는 데 사용할 수 있습니다.


## useNavigate 옵션

`useNavigate` 훅은 하나의 인자를 받습니다. 이 인자는 `options` 객체입니다.


### `opts.from` 옵션

- 타입: `string`
- 선택 사항
- 설명: 네비게이션을 시작할 위치를 지정합니다. 현재 위치가 아닌 특정 위치에서 새로운 위치로 이동할 때 유용합니다.


## useNavigate 반환값

- 새로운 위치로 이동할 때 사용할 수 있는 `navigate` 함수를 반환합니다.


## navigate 함수

`navigate` 함수는 새로운 위치로 이동할 때 사용할 수 있는 함수입니다.


### navigate 함수 옵션

`navigate` 함수는 하나의 인자를 받습니다. 이 인자는 `options` 객체입니다.

- 타입: [`NavigateOptions`](./NavigateOptionsType.md)


### navigate 함수 반환값

- 네비게이션이 완료될 때 resolve되는 `Promise`를 반환합니다.


## 예제

```tsx
import { useNavigate } from '@tanstack/react-router'

function PostsPage() {
  const navigate = useNavigate({ from: '/posts' })
  const handleClick = () => navigate({ search: { page: 2 } })
  // ...
}

function Component() {
  const navigate = useNavigate()
  return (
    <>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
          })
        }
      >
        Posts
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            search: { page: 2 },
          })
        }
      >
        Posts (Page 2)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            hash: 'my-hash',
          })
        }
      >
        Posts (Hash)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            state: { from: 'home' },
          })
        }
      >
        Posts (State)
      </button>
    </>
  )
}
```

이 예제에서는 `useNavigate` 훅을 사용하여 네비게이션을 처리합니다. 각 버튼은 클릭 시 다른 옵션을 사용하여 `/posts` 경로로 이동합니다. 첫 번째 버튼은 기본 경로로 이동하고, 두 번째 버튼은 페이지 2로 이동하며, 세 번째 버튼은 해시를 포함한 경로로 이동합니다. 마지막 버튼은 상태 정보를 포함하여 이동합니다.


