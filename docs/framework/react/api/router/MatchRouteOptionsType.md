# MatchRouteOptions 타입

`MatchRouteOptions` 타입은 라우트를 매칭할 때 사용할 수 있는 옵션을 정의합니다.

```tsx
interface MatchRouteOptions {
  pending?: boolean
  caseSensitive?: boolean
  includeSearch?: boolean
  fuzzy?: boolean
}
```

- **pending**: 라우트가 아직 완료되지 않았는지 여부를 나타냅니다.
- **caseSensitive**: 대소문자를 구분하여 매칭할지 결정합니다.
- **includeSearch**: 검색 파라미터를 포함하여 매칭할지 설정합니다.
- **fuzzy**: 퍼지 매칭을 사용할지 여부를 지정합니다.


## MatchRouteOptions 속성

`MatchRouteOptions` 타입은 다음과 같은 속성을 가지고 있습니다:


### `pending` 속성

- 타입: `boolean`
- 선택 사항
- `true`로 설정하면 현재 위치가 아닌 보류 중인 위치와 매칭됩니다.


### `caseSensitive` 속성

- 타입: `boolean`
- 선택 사항
- `true`로 설정하면 현재 위치와 대소문자를 구분하여 매칭합니다.


### `includeSearch` 속성

- 타입: `boolean`
- 선택 사항
- `true`로 설정하면, 현재 위치의 검색 파라미터를 깊은 포함 검사로 매칭합니다. 예를 들어, `{ a: 1 }`은 현재 위치가 `{ a: 1, b: 2 }`인 경우에도 매칭됩니다.


### `fuzzy` 속성

- 타입: `boolean`
- 선택 사항
- `true`로 설정하면 현재 위치와 퍼지 매칭(fuzzy match)을 수행합니다. 예를 들어 `/posts`는 `/posts/123`과 같은 현재 위치와 매칭됩니다.


