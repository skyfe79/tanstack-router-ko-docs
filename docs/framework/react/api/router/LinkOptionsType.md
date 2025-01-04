# LinkOptions 타입

`LinkOptions` 타입은 [`NavigateOptions`](./NavigateOptionsType.md) 타입을 확장하며, 실제 앵커 엘리먼트 속성을 처리할 때 TanStack Router에서 사용할 수 있는 추가 옵션을 포함합니다.

```tsx
type LinkOptions = NavigateOptions & {
  target?: HTMLAnchorElement['target']
  activeOptions?: ActiveOptions
  preload?: false | 'intent'
  preloadDelay?: number
  disabled?: boolean
}
```

- `target`: 앵커 엘리먼트의 `target` 속성을 지정합니다. 예를 들어, `_blank`를 사용하면 새 탭에서 링크를 열 수 있습니다.
- `activeOptions`: 활성 상태와 관련된 옵션을 설정합니다.
- `preload`: 링크를 미리 로드할지 여부를 결정합니다. `false`로 설정하면 미리 로드하지 않고, `'intent'`로 설정하면 사용자가 링크 위에 마우스를 올렸을 때 미리 로드합니다.
- `preloadDelay`: 미리 로드를 시작하기 전의 지연 시간을 밀리초 단위로 지정합니다.
- `disabled`: 링크를 비활성화할지 여부를 결정합니다. `true`로 설정하면 링크가 비활성화됩니다.


## LinkOptions 속성

`LinkOptions` 객체는 다음과 같은 속성을 포함합니다:


### `target`

- 타입: `HTMLAnchorElement['target']`
- 선택 사항
- 표준 앵커 태그의 target 속성

```typescript
// 예제: target 속성 사용
const link = document.createElement('a');
link.href = 'https://example.com';
link.target = '_blank';  // 새 탭에서 열기
link.textContent = '새 창에서 열기';
document.body.appendChild(link);
```

`target` 속성은 앵커 태그(`<a>`)에서 링크를 열 위치를 지정합니다. 주로 `_blank`, `_self`, `_parent`, `_top` 등의 값을 사용하며, 각각 새 창, 현재 창, 부모 창, 최상위 창에서 링크를 엽니다.


### `activeOptions`

- 타입: `ActiveOptions`
- 선택 사항
- 링크가 활성 상태인지 여부를 결정하는 데 사용될 옵션들


### `preload`

- 타입: `false | 'intent' | 'viewport' | 'render'`
- 선택 사항
- 이 값을 설정하면 링크의 미리 가져오기(preloading) 전략이 해당 값으로 설정됩니다.
- 자세한 내용은 [미리 가져오기 가이드](../../guide/preloading.md)를 참고하세요.


### `preloadDelay`

- 타입: `number`
- 옵션: 선택 사항
- 설명: 인텐트(intent) 미리 가져오기를 지정된 밀리초만큼 지연시킵니다. 이 지연 시간 전에 인텐트가 종료되면, 미리 가져오기가 취소됩니다.


### `disabled`

- 타입: `boolean`
- 선택 사항
- true로 설정하면, 링크를 `href` 속성 없이 렌더링합니다.

```jsx
// 예제: disabled 속성 사용
<Link disabled={true}>
  비활성화된 링크
</Link>
```

위 예제에서 `disabled`가 `true`로 설정되면, 링크는 `href` 속성 없이 렌더링됩니다. 이는 링크가 클릭되지 않도록 만듭니다.


