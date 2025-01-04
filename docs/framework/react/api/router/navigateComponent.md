# Navigate 컴포넌트

`Navigate` 컴포넌트는 렌더링될 때 새로운 위치로 이동할 수 있는 컴포넌트입니다. 이 컴포넌트는 경로(pathname), 검색 파라미터(search params), 해시(hash), 그리고 위치 상태(location state)의 변경을 포함합니다. 성공적으로 렌더링되면, 내부적으로 `useEffect` 훅 안에서 네비게이션이 발생합니다.


## Navigate 컴포넌트의 props

`Navigate` 컴포넌트는 다음과 같은 props를 받습니다:


### `...options`

- 타입: [`NavigateOptions`](./NavigateOptionsType.md)

`...options`는 네비게이션 옵션을 나타내는 매개변수입니다. 이 매개변수는 `NavigateOptions` 타입을 사용하며, 네비게이션 동작을 세부적으로 제어할 수 있는 다양한 옵션을 제공합니다. 예를 들어, 페이지 이동 시 스크롤 위치를 조정하거나, 특정 상태를 유지하는 등의 설정을 할 수 있습니다. 

```typescript
// 예제: 네비게이션 옵션 사용
navigate('/new-page', { 
    state: { from: '/current-page' },
    replace: true 
});
```

위 예제에서 `state`는 이동할 페이지에 전달할 상태 정보를, `replace`는 현재 페이지를 히스토리 스택에 남기지 않고 대체할지 여부를 지정합니다. 이러한 옵션을 통해 네비게이션 동작을 더욱 세밀하게 조정할 수 있습니다.


## Navigate 반환값

- `null`


