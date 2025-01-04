# Table of Contents

- [검색 파라미터 제거를 위한 미들웨어](#검색-파라미터-제거를-위한-미들웨어)
  - [stripSearchParams 속성](#stripsearchparams-속성)
  - [예제](#예제)

# 검색 파라미터 제거를 위한 미들웨어

`stripSearchParams`는 검색 파라미터를 제거할 수 있는 검색 미들웨어입니다.


## stripSearchParams 속성

`stripSearchParams`는 다음 중 하나를 입력값으로 받습니다:

- `true`: 검색 스키마에 필수 파라미터가 없는 경우, `true`를 사용하여 모든 검색 파라미터를 제거할 수 있습니다.
- 제거할 검색 파라미터의 키 목록. 이때 선택적 검색 파라미터의 키만 허용됩니다.
- 부분 입력 검색 스키마를 따르는 객체. 검색 파라미터는 이 객체의 값과 비교되며, 값이 깊은 비교(deeply equal)를 통해 일치하면 해당 파라미터가 제거됩니다. 이는 기본값으로 설정된 검색 파라미터를 제거할 때 특히 유용합니다.


## 예제

```tsx
import { z } from 'zod'
import { createFileRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const defaultValues = {
  one: 'abc',
  two: 'xyz',
}

const searchSchema = z.object({
  one: z.string().default(defaultValues.one),
  two: z.string().default(defaultValues.two),
})

export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 기본값 제거
    middlewares: [stripSearchParams(defaultValues)],
  },
})
```

```tsx
import { z } from 'zod'
import { createRootRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  hello: z.string().default('world'),
  requiredParam: z.string(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 항상 `hello` 제거
    middlewares: [stripSearchParams(['hello'])],
  },
})
```

```tsx
import { z } from 'zod'
import { createFileRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  one: z.string().default('abc'),
  two: z.string().default('xyz'),
})

export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 모든 검색 파라미터 제거
    middlewares: [stripSearchParams(true)],
  },
})
```


