# HistoryState 인터페이스

`HistoryState` 인터페이스는 `history` 패키지에서 내보내는 인터페이스로, `history` 패키지와 `window.location` API와 함께 사용할 수 있는 상태 객체의 구조를 정의합니다.

이 인터페이스를 확장하여 애플리케이션 전체에서 상태 객체에 추가 속성을 정의할 수 있습니다.

```tsx
interface HistoryState {
  key: string // 특정 히스토리 항목의 고유 ID
}
```


