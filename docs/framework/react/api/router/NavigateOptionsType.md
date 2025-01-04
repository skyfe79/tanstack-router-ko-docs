# NavigateOptions 타입

`NavigateOptions` 타입은 TanStack Router에서 네비게이션 동작을 정의할 때 사용할 수 있는 옵션들을 설명합니다.

```tsx
type NavigateOptions = ToOptions & {
  replace?: boolean
  resetScroll?: boolean
  hashScrollIntoView?: boolean | ScrollIntoViewOptions
  ignoreBlocker?: boolean
  reloadDocument?: boolean
  href?: string
}
```

이 타입은 `ToOptions`를 확장하며, 다음과 같은 추가 옵션들을 포함합니다:

- `replace`: 현재 히스토리 항목을 대체할지 여부를 결정합니다.
- `resetScroll`: 스크롤 위치를 초기화할지 여부를 설정합니다.
- `hashScrollIntoView`: 해시에 해당하는 엘리먼트로 스크롤할지 여부를 지정합니다. `ScrollIntoViewOptions` 객체를 통해 세부 설정이 가능합니다.
- `ignoreBlocker`: 네비게이션 블로커를 무시할지 여부를 결정합니다.
- `reloadDocument`: 문서를 새로고침할지 여부를 설정합니다.
- `href`: 이동할 URL을 직접 지정합니다.


## NavigateOptions 속성

`NavigateOptions` 객체는 다음과 같은 속성을 받습니다:


### `replace`

- **타입**: `boolean`
- **선택사항**
- **기본값**: `false`
- **설명**: `true`로 설정하면, `history.push` 대신 `history.replace`를 사용하여 브라우저 기록에 위치를 저장합니다.


### `resetScroll`

- **타입**: `boolean`
- **선택사항**
- **기본값**: `true`로 설정되며, 브라우저 히스토리에 위치가 커밋된 후 스크롤 위치가 0,0으로 초기화됩니다.
- **`false`로 설정할 경우**, 위치가 히스토리에 커밋된 후 스크롤 위치가 초기화되지 않습니다.


### `ignoreBlocker`

- **타입**: `boolean`
- **선택 사항**
- **기본값**: `false`
- **설명**: `true`로 설정하면, 네비게이션을 방해할 수 있는 모든 블로커를 무시합니다.


### `reloadDocument`

- **타입**: `boolean`
- **선택사항**
- **기본값**: `false`
- **설명**: `true`로 설정하면, 라우터 내부의 라우트로 네비게이션할 때 전통적인 SPA(싱글 페이지 애플리케이션) 방식의 네비게이션 대신 전체 페이지를 새로 로드합니다.


### `href`

- 타입: `string`
- 선택 사항
- `to` 대신 사용할 수 있으며, 완전히 구성된 href로 이동할 때 활용됩니다. 예를 들어 외부 대상으로 연결할 때 유용합니다.

- [`ToOptions`](./ToOptionsType.md)


