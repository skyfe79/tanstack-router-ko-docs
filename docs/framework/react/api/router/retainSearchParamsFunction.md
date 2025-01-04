# 검색 파라미터 유지를 위한 미들웨어

`retainSearchParams`는 검색 파라미터를 유지할 수 있게 해주는 검색 미들웨어입니다.


## retainSearchParams props

`retainSearchParams`는 `true` 또는 유지할 검색 파라미터의 키 목록을 받습니다.  
`true`를 전달하면 모든 검색 파라미터가 유지됩니다.


## 예제

```tsx
import { z } from 'zod'
import { createRootRoute, retainSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

// 검색 파라미터 스키마 정의
const searchSchema = z.object({
  rootValue: z.string().optional(), // rootValue는 선택적 문자열
})

// 루트 라우트 생성
export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema), // 검색 파라미터 유효성 검사
  search: {
    middlewares: [retainSearchParams(['rootValue'])], // rootValue 파라미터 유지
  },
})
```

```tsx
import { z } from 'zod'
import { createFileRoute, retainSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

// 검색 파라미터 스키마 정의
const searchSchema = z.object({
  one: z.string().optional(), // one은 선택적 문자열
  two: z.string().optional(), // two는 선택적 문자열
})

// 파일 라우트 생성
export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema), // 검색 파라미터 유효성 검사
  search: {
    middlewares: [retainSearchParams(true)], // 모든 검색 파라미터 유지
  },
})
```


