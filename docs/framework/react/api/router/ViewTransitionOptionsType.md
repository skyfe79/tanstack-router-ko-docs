# Table of Contents

- [ViewTransitionOptions 타입](#viewtransitionoptions-타입)
  - [ViewTransitionOptions 속성](#viewtransitionoptions-속성)
    - [`types` 속성](#types-속성)

# ViewTransitionOptions 타입

`ViewTransitionOptions` 타입은 [뷰 트랜지션 타입](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)을 정의하는 데 사용됩니다.

```tsx
interface ViewTransitionOptions {
  types: Array
}
```


## ViewTransitionOptions 속성

`ViewTransitionOptions` 타입은 단일 속성을 가진 객체를 받습니다:


### `types` 속성

- 타입: `Array<string>`
- 필수 여부: 필수
- `document.startViewTransition({update, types})` 호출에 전달될 타입 배열

```javascript
// 예제 코드
const types = ['slide', 'fade'];
document.startViewTransition({
  update: () => {
    // DOM 업데이트 로직
  },
  types: types
});
```

이 속성은 `document.startViewTransition` 메서드에 전달될 트랜지션 타입을 정의합니다. 여러분은 이 배열을 통해 다양한 트랜지션 효과를 지정할 수 있습니다. 예를 들어, 'slide'나 'fade'와 같은 타입을 사용하여 화면 전환 효과를 적용할 수 있습니다.


