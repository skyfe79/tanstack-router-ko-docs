# createRouteMask 함수

`createRouteMask` 함수는 `RouterOptions.routeMasks` 옵션에 전달할 수 있는 라우트 마스크 설정을 생성하는 헬퍼 함수입니다.


## createRouteMask 옵션

- 타입: [`RouteMask`](./RouteMaskType.md)
- 필수 여부: 필수
- 설명: 라우트 마스크를 구성하는 데 사용되는 옵션들


## createRouteMask 반환값

- `RouterOptions.routeMasks` 옵션에 전달할 수 있는 [`RouteMask`](./RouteMaskType.md) 타입의 객체를 반환합니다.


## 예제

```tsx
import { createRouteMask, createRouter } from '@tanstack/react-router'

// 사진 모달에서 사진으로 이동하는 라우트 마스크 생성
const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: true,
})

// 라우터 인스턴스 설정
const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```


