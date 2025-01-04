# RouteMask 타입

`RouteMask` 타입은 [`ToOptions`](./ToOptionsType.md) 타입을 확장하며, 라우트 마스크를 생성하는 데 필요한 추가 속성을 가지고 있습니다.


## RouteMask 속성

`RouteMask` 타입은 다음과 같은 속성을 가진 객체를 받습니다:


### `...ToOptions`

- 타입: [`ToOptions`](./ToOptionsType.md)
- 필수 여부: 필수
- 설명: 라우트 마스크를 구성하는 데 사용될 옵션들


### `options.routeTree`

- 타입: `TRouteTree`
- 필수 여부: 필수
- 설명: 이 라우트 마스크가 지원할 라우트 트리

```typescript
interface TRouteTree {
  // 라우트 트리 구조 정의
}
```


### `options.unmaskOnReload`

- 타입: `boolean`
- 옵션
- `true`로 설정하면 페이지를 다시 로드할 때 라우트 마스크가 제거됩니다.


