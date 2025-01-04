# Table of Contents

- [useLocation 훅](#uselocation-훅)
  - [useLocation 옵션](#uselocation-옵션)
    - [`opts.select` 옵션](#optsselect-옵션)
  - [useLocation 반환값](#uselocation-반환값)
  - [예제](#예제)

# useLocation 훅

`useLocation` 메서드는 현재 [`location`](./ParsedLocationType.md) 객체를 반환하는 훅입니다. 이 훅은 현재 위치가 변경될 때마다 특정 부수 효과(side effect)를 수행하고 싶을 때 유용합니다.

```javascript
import { useLocation } from 'react-router-dom';

function MyComponent() {
  const location = useLocation();

  // 위치가 변경될 때마다 실행될 로직
  useEffect(() => {
    console.log('Location changed:', location);
  }, [location]);

  return <div>Current Path: {location.pathname}</div>;
}
```

위 예제에서 `useLocation` 훅을 사용해 현재 위치 정보를 가져오고, `useEffect`를 통해 위치가 변경될 때마다 콘솔에 로그를 출력합니다. 이렇게 하면 네비게이션에 따른 동적 처리가 가능해집니다.


## useLocation 옵션

`useLocation` 훅은 선택적으로 `options` 객체를 인자로 받을 수 있습니다.


### `opts.select` 옵션

- 타입: `(state: ParsedLocationType) => TSelected`
- 선택 사항
- 이 함수를 제공하면, [`location`](./ParsedLocationType.md) 객체와 함께 호출되며, 반환된 값은 `useLocation`에서 반환됩니다.

```typescript
// 예제 코드
const selectedLocation = useLocation({
  select: (location) => {
    // location 객체에서 필요한 정보를 선택하여 반환
    return location.pathname;
  }
});
```

이 옵션을 사용하면, `location` 객체에서 필요한 부분만 선택적으로 가져올 수 있습니다. 예를 들어, `pathname`이나 `search`와 같은 특정 속성만 반환하도록 설정할 수 있습니다.


## useLocation 반환값

- 현재 [`location`](./ParsedLocationType.md) 객체를 반환합니다. 만약 `select` 함수가 제공된 경우, `TSelected` 타입의 값을 반환합니다.


## 예제

```tsx
import { useLocation } from '@tanstack/react-router'

function Component() {
  const location = useLocation()
  //    ^ ParsedLocation 타입

  // 또는

  const pathname = useLocation({
    select: (location) => location.pathname,
  })
  //    ^ string 타입

  // ...
}
```

- `useLocation` 훅을 사용하여 현재 위치 정보를 가져올 수 있습니다.
- 첫 번째 예제에서는 전체 위치 객체를 반환하며, 이 객체는 `ParsedLocation` 타입입니다.
- 두 번째 예제에서는 `select` 함수를 사용해 위치 객체에서 `pathname`만 추출합니다. 이 경우 반환값은 `string` 타입입니다.


