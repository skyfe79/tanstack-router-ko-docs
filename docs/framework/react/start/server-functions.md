# Server Functions





## 서버 함수란 무엇인가요?

서버 함수는 거의 모든 곳(클라이언트에서도)에서 호출할 수 있지만, **오직** 서버에서만 실행되는 로직을 정의할 수 있게 해줍니다. 사실, 이는 API 라우트와 크게 다르지 않지만 몇 가지 주요 차이점이 있습니다:

- HTTP를 통해 공개적으로 접근할 수 없습니다.
- 로더, 훅, 컴포넌트 등 애플리케이션의 여러 곳에서 호출할 수 있습니다.

하지만 일반 API 라우트와 다음과 같은 공통점도 있습니다:

- 요청 컨텍스트에 접근할 수 있어 헤더를 읽거나 쿠키를 설정하는 등의 작업이 가능합니다.
- 환경 변수와 같은 민감한 정보에 접근할 수 있으며, 이를 클라이언트에 노출하지 않습니다.
- 데이터베이스에서 데이터를 가져오거나 이메일을 보내는 등 모든 종류의 서버 측 로직을 수행할 수 있습니다.
- 원시 값, JSON 직렬화 가능한 객체, 심지어 raw Response 객체까지 어떤 값이든 반환할 수 있습니다.
- 리다이렉트나 notFound와 같은 오류를 던질 수 있으며, 이는 라우터에 의해 자동으로 처리됩니다.

> 서버 함수와 "React 서버 함수"는 어떻게 다른가요?
>
> - TanStack 서버 함수는 특정 프론트엔드 프레임워크에 종속되지 않으며, 어떤 프론트엔드 프레임워크와도 함께 사용할 수 있습니다. 프레임워크 없이도 사용 가능합니다.
> - TanStack 서버 함수는 표준 HTTP 요청을 기반으로 하며, 직렬 실행 병목 현상 없이 원하는 만큼 자주 호출할 수 있습니다.


## 서버 함수는 어떻게 동작하나요?

서버 함수는 애플리케이션 어디에서나 정의할 수 있지만, 파일의 최상위 레벨에서 정의되어야 합니다. 이 함수는 로더, 훅 등을 포함한 애플리케이션 전체에서 호출할 수 있습니다. 전통적으로 이 패턴은 원격 프로시저 호출(RPC)로 알려져 있지만, 이러한 함수의 동형성(isomorphic nature) 때문에 우리는 이를 서버 함수라고 부릅니다.

- **서버 번들**에서는 서버 함수의 로직이 그대로 유지됩니다. 이미 올바른 위치에 있기 때문에 별도의 작업이 필요하지 않습니다.
- **클라이언트**에서는 서버 함수가 제거됩니다. 서버 함수는 서버에만 존재합니다. 클라이언트에서 서버 함수를 호출하면, 서버로 `fetch` 요청을 보내 서버 함수를 실행하고, 그 결과를 클라이언트로 다시 보내는 방식으로 대체됩니다.


## 서버 함수 미들웨어

서버 함수는 미들웨어를 사용해 로직, 컨텍스트, 공통 작업, 사전 조건 등을 공유할 수 있습니다. 서버 함수 미들웨어에 대해 더 자세히 알고 싶다면 [미들웨어 가이드](./middleware.md)를 참고하세요.


## 서버 함수 정의하기

> TanStack Start의 서버 함수 디자인과 구현 과정에서 영감과 지침을 제공해준 [tRPC](https://trpc.io/) 팀에게 감사의 말씀을 전합니다. API 라우트를 위해 tRPC를 사용하는 것을 너무나 좋아하고 추천하기 때문에, 서버 함수도 동일한 퍼스트클래스 기능과 개발자 경험을 제공하도록 설계했습니다. 감사합니다!

서버 함수는 `@tanstack/start` 패키지의 `createServerFn` 함수를 사용하여 정의합니다. 이 함수는 HTTP 메서드를 지정하기 위한 선택적 `options` 인자를 받으며, 체이닝을 통해 서버 함수의 본문, 입력 검증, 미들웨어 등을 정의할 수 있습니다. 다음은 간단한 예제입니다:

```tsx
// getServerTime.ts
import { createServerFn } from '@tanstack/start'

export const getServerTime = createServerFn().handler(async () => {
  // 1초 대기
  await new Promise((resolve) => setTimeout(resolve, 1000))
  // 현재 시간 반환
  return new Date().toISOString()
})
```


## 서버 함수를 어디에서 호출할 수 있나요?

- 서버 사이드 코드에서
- 클라이언트 사이드 코드에서
- 다른 서버 함수에서

> [!WARNING]
> 서버 함수는 API 라우트에서 호출할 수 없습니다. 서버 함수와 API 라우트 간에 비즈니스 로직을 공유해야 한다면, 공통 로직을 유틸리티 함수로 추출하여 양쪽에서 import할 수 있도록 해야 합니다.


## 파라미터 받기

서버 함수는 다양한 타입의 단일 파라미터를 받을 수 있습니다:

- 기본 자바스크립트 타입
  - `string`
  - `number`
  - `boolean`
  - `null`
  - `Array`
  - `Object`
- FormData
- ReadableStream (위 타입 중 하나)
- Promise (위 타입 중 하나)

다음은 간단한 문자열 파라미터를 받는 서버 함수 예제입니다:

```tsx
import { createServerFn } from '@tanstack/start'

export const greet = createServerFn({
  method: 'GET',
})
  .validator((data: string) => data)  // 데이터 유효성 검사
  .handler(async (ctx) => {
    return `Hello, ${ctx.data}!`  // 파라미터를 사용한 응답
  })

greet({
  data: 'John',  // 파라미터 전달
})
```


## 런타임 입력 검증 및 타입 안전성

서버 함수는 런타임에 입력 데이터를 검증하고 타입 안전성을 추가하도록 설정할 수 있습니다. 이는 서버 함수를 실행하기 전에 입력이 올바른 타입인지 확인하고, 더 친절한 오류 메시지를 제공하는 데 유용합니다.

이 기능은 `validator` 메서드를 통해 구현됩니다. 이 메서드는 서버 함수에 전달된 모든 입력을 받아들입니다. 이 함수에서 반환한 값(및 타입)은 실제 서버 함수 핸들러에 전달될 입력이 됩니다.

또한, Zod와 같은 외부 검증 도구를 사용하고 싶다면, 검증기가 원활하게 통합됩니다.

```javascript
// 예제 코드
const serverFunction = (input) => {
  // 서버 함수 로직
};

const validator = (input) => {
  if (typeof input !== 'string') {
    throw new Error('입력은 문자열이어야 합니다.');
  }
  return input;
};

const validatedServerFunction = validator(serverFunction);
```

이렇게 하면 입력이 올바른 타입인지 확인하고, 그렇지 않을 경우 적절한 오류 메시지를 반환할 수 있습니다.


### 기본 유효성 검사

다음은 입력 매개변수를 검증하는 간단한 서버 함수 예제입니다:

```tsx
import { createServerFn } from '@tanstack/start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    // person이 객체가 아니거나 null인 경우 에러 발생
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    // person 객체에 name 속성이 없거나, name이 문자열이 아닌 경우 에러 발생
    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    // 검증된 person 객체 반환
    return person as Person
  })
  .handler(async ({ data }) => {
    // 검증된 데이터를 사용하여 응답 반환
    return `Hello, ${data.name}!`
  })
```

이 코드는 `Person` 타입의 객체를 검증하고, 검증된 데이터를 사용하여 간단한 인사 메시지를 반환합니다. `validator` 함수는 입력 데이터의 유효성을 검사하고, `handler` 함수는 검증된 데이터를 처리합니다.


### 검증 라이브러리 사용하기

Zod와 같은 검증 라이브러리는 다음과 같이 사용할 수 있습니다.

```tsx
import { createServerFn } from '@tanstack/start'
import { z } from 'zod'

// Person 스키마 정의
const Person = z.object({
  name: z.string(),
})

// greet 함수 생성
export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown) => {
    return Person.parse(person) // 데이터 검증
  })
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!` // 검증된 데이터 사용
  })

// greet 함수 호출
greet({
  data: {
    name: 'John',
  },
})
```

이 예제에서는 Zod 라이브러리를 사용하여 데이터를 검증하고, 검증된 데이터를 기반으로 응답을 반환합니다. `Person` 스키마는 `name` 필드가 문자열 타입인지 확인합니다. `greet` 함수는 이 스키마를 사용하여 입력 데이터를 검증하고, 검증이 성공하면 이름을 포함한 인사말을 반환합니다.


## 타입 안전성

서버 함수는 네트워크 경계를 넘나들기 때문에, 전달되는 데이터가 올바른 타입인지 확인하고 런타임에 검증하는 것이 중요합니다. 특히 사용자 입력을 다룰 때는 예측할 수 없는 경우가 많으므로 더욱 신경 써야 합니다. 개발자가 I/O 데이터를 검증하도록 하기 위해, 타입은 검증에 의존합니다. `validator` 함수의 반환 타입은 서버 함수 핸들러의 입력이 됩니다.

```tsx
import { createServerFn } from '@tanstack/start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    return person as Person
  })
  .handler(
    async ({
      data, // Person
    }) => {
      return `Hello, ${data.name}!`
    },
  )

function test() {
  greet({ data: { name: 'John' } }) // 정상 동작
  greet({ data: { name: 123 } }) // 오류: '{ name: number; }' 타입의 인자는 'Person' 타입의 매개변수에 할당할 수 없습니다.
}
```


## 타입 추론

서버 함수는 `validator`에 입력된 값과 `handler` 함수의 반환 값을 기반으로 입력 및 출력 타입을 추론합니다. 실제로 여러분이 정의한 `validator`는 입력 데이터를 변환하는 경우에 유용하게 사용할 수 있는 별도의 입력/출력 타입을 가질 수도 있습니다.

이를 설명하기 위해 `zod` 검증 라이브러리를 사용한 예제를 살펴보겠습니다:

```tsx
import { createServerFn } from '@tanstack/start'
import { z } from 'zod'

// 트랜잭션 스키마 정의
const transactionSchema = z.object({
  amount: z.string().transform((val) => parseInt(val, 10)), // 문자열을 숫자로 변환
})

// 서버 함수 생성
const createTransaction = createServerFn()
  .validator(transactionSchema) // 검증기 설정
  .handler(({ data }) => {
    return data.amount // 숫자 반환
  })

// 서버 함수 호출
createTransaction({
  data: {
    amount: '123', // 문자열 입력
  },
})
```

이 예제에서 `transactionSchema`는 문자열로 입력된 `amount`를 숫자로 변환합니다. `createTransaction` 함수는 문자열을 입력으로 받아 숫자를 반환합니다. 이렇게 타입 추론이 자동으로 이루어지므로, 여러분은 별도로 타입을 명시하지 않아도 됩니다.


## 검증 없는 타입 추론

네트워크 I/O 데이터를 검증하기 위해 검증 라이브러리를 사용하는 것을 적극 권장하지만, 어떤 이유로든 데이터를 검증하지 않고도 타입 안전성을 유지하고 싶을 수 있습니다. 이를 위해 `validator`로 타입 정보를 제공하는 아이덴티티 함수를 사용하여 서버 함수에 입력 및 출력 타입을 올바르게 지정할 수 있습니다.

```tsx
import { createServerFn } from '@tanstack/start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((d: Person) => d) // 타입 정보를 제공하는 아이덴티티 함수
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!`
  })

greet({
  data: {
    name: 'John',
  },
})
```

이 예제에서는 `validator` 함수가 입력 데이터를 `Person` 타입으로 강제 변환합니다. 이를 통해 데이터 검증 없이도 타입 안전성을 보장할 수 있습니다.


## JSON 파라미터

서버 함수는 JSON으로 직렬화 가능한 객체를 파라미터로 받을 수 있습니다. 이 기능은 복잡한 데이터 구조를 서버로 전달할 때 유용합니다:

```tsx
import { createServerFn } from '@tanstack/start'

type Person = {
  name: string
  age: number
}

export const greet = createServerFn({ method: 'GET' })
  .validator((data: Person) => data)  // 데이터 유효성 검사
  .handler(async ({ data }) => {
    return `안녕하세요, ${data.name}님! 당신은 ${data.age}살입니다.`
  })

greet({
  data: {
    name: 'John',
    age: 34,
  },
})
```

위 예제에서는 `Person` 타입의 객체를 서버 함수에 전달하고, 서버에서 이를 처리하여 응답을 반환합니다.


## FormData 파라미터

서버 함수는 `FormData` 객체를 파라미터로 받을 수 있습니다.

```tsx
import { createServerFn } from '@tanstack/start'

export const greetUser = createServerFn()
  .validator((data) => {
    // 데이터가 FormData 인스턴스인지 확인
    if (!(data instanceof FormData)) {
      throw new Error('유효하지 않은 폼 데이터')
    }
    const name = data.get('name')
    const age = data.get('age')

    // 이름과 나이가 모두 있는지 확인
    if (!name || !age) {
      throw new Error('이름과 나이는 필수 입력 항목입니다')
    }

    return {
      name: name.toString(),
      age: parseInt(age.toString(), 10),
    }
  })
  .handler(async ({ data: { name, age } }) => {
    return `안녕하세요, ${name}님! 당신은 ${age}살입니다.`
  })

// 사용 예시
function Test() {
  return (
    <form
      onSubmit={async (event) => {
        event.preventDefault()
        const formData = new FormData(event.currentTarget)
        const response = await greetUser({ data: formData })
        console.log(response)
      }}
    >
      
      
      제출
    
  )
}
```

이 코드는 폼 데이터를 서버로 전송하고, 서버에서 이를 검증한 후 응답을 반환하는 예제입니다. 폼 제출 시 `FormData` 객체를 생성하고, 이를 서버 함수에 전달하여 처리합니다.


## 서버 함수 컨텍스트

서버 함수는 단일 매개변수를 받는 것 외에도, `vinxi/http`의 유틸리티를 사용해 서버 함수 내에서 서버 요청 컨텍스트에 접근할 수 있습니다. 내부적으로 Vinxi는 크로스 플랫폼 HTTP 요청을 처리하기 위해 `unjs`의 `h3` 패키지를 사용합니다.

다음과 같은 작업을 위해 다양한 컨텍스트 함수를 사용할 수 있습니다:

- 요청 컨텍스트 접근
- 헤더 접근 및 설정
- 세션/쿠키 접근 및 설정
- 응답 상태 코드 및 상태 메시지 설정
- 멀티파트 폼 데이터 처리
- 커스텀 서버 컨텍스트 속성 읽기 및 설정

사용 가능한 모든 컨텍스트 함수 목록은 [h3 메서드](https://h3.unjs.io/utils/request)를 참고하거나 [Vinxi 소스 코드](https://github.com/nksaraf/vinxi/blob/main/packages/vinxi/runtime/http.js#L232-L320)를 확인하세요.

기본적인 예제를 살펴보겠습니다:


## 요청 컨텍스트에 접근하기

Vinxi의 `getWebRequest` 함수를 사용하여 서버 함수 내부에서 요청 자체에 접근해 보겠습니다:

```tsx
import { createServerFn } from '@tanstack/start'
import { getWebRequest } from 'vinxi/http'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    const request = getWebRequest()

    console.log(request.method) // GET

    console.log(request.headers.get('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```

이 코드는 `getWebRequest` 함수를 사용하여 현재 요청 객체를 가져옵니다. 요청 메서드와 사용자 에이전트 정보를 콘솔에 출력합니다.


## 헤더 접근하기

Vinxi의 `getHeaders` 함수를 사용하면 서버 함수 내에서 모든 헤더에 접근할 수 있습니다:

```tsx
import { createServerFn } from '@tanstack/start'
import { getHeaders } from 'vinxi/http'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeaders())
    // {
    //   "accept": "*/*",
    //   "accept-encoding": "gzip, deflate, br",
    //   "accept-language": "en-US,en;q=0.9",
    //   "connection": "keep-alive",
    //   "host": "localhost:3000",
    //   ...
    // }
  },
)
```

또한 `getHeader` 함수를 사용하면 개별 헤더에 접근할 수 있습니다:

```tsx
import { createServerFn } from '@tanstack/start'
import { getHeader } from 'vinxi/http'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeader('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```


## 반환 값

서버 함수는 여러 가지 타입의 값을 반환할 수 있습니다:

- 기본 타입(Primitives)
- JSON으로 직렬화 가능한 객체
- `redirect` 에러 (throw로 던질 수도 있음)
- `notFound` 에러 (throw로 던질 수도 있음)
- Raw Response 객체


## 기본 타입과 JSON 반환하기

서버 함수에서 기본 타입이나 JSON으로 직렬화 가능한 객체를 반환하려면, 단순히 값을 반환하면 됩니다.

```tsx
import { createServerFn } from '@tanstack/start'

// 서버 시간을 반환하는 함수
export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    return new Date().toISOString()
  },
)

// 서버 데이터를 반환하는 함수
export const getServerData = createServerFn({ method: 'GET' }).handler(
  async () => {
    return {
      message: 'Hello, World!',
    }
  },
)
```

기본적으로 서버 함수는 반환된 값이 `Response` 객체가 아닌 경우, 기본 타입이나 JSON으로 직렬화 가능한 객체로 간주합니다.


## 커스텀 헤더로 응답하기

Vinxi의 `setHeader` 함수를 사용하면 커스텀 헤더로 응답할 수 있습니다:

```tsx
import { createServerFn } from '@tanstack/start'
import { setHeader } from 'vinxi/http'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setHeader('X-Custom-Header', 'value')  // 커스텀 헤더 설정
    return new Date().toISOString()  // 현재 시간을 ISO 형식으로 반환
  },
)
```


## 커스텀 상태 코드로 응답하기

Vinxi의 `setResponseStatus` 함수를 사용하면 커스텀 상태 코드로 응답할 수 있습니다.

```tsx
import { createServerFn } from '@tanstack/start'
import { setResponseStatus } from 'vinxi/http'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setResponseStatus(201) // 상태 코드를 201로 설정
    return new Date().toISOString() // 현재 시간을 ISO 형식으로 반환
  },
)
```

위 예제에서는 `setResponseStatus` 함수를 사용해 응답 상태 코드를 201로 설정하고, 현재 시간을 ISO 형식으로 반환합니다. 이를 통해 서버에서 특정 상태 코드와 함께 데이터를 보낼 수 있습니다.


## Raw Response 객체 반환하기

Raw Response 객체를 반환하려면, 서버 함수에서 Response 객체를 직접 반환하면 됩니다.

```tsx
import { createServerFn } from '@tanstack/start'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    // s3에서 파일을 읽어옴
    return fetch('https://example.com/time.txt')
  },
)
```

위 예제에서는 `fetch`를 사용해 외부 URL에서 데이터를 가져와 그대로 반환합니다. 이렇게 하면 서버 함수가 직접 Response 객체를 반환하게 됩니다.


## 에러 던지기

특별한 `redirect`와 `notFound` 에러 외에도, 서버 함수는 어떤 커스텀 에러든 던질 수 있습니다. 이 에러들은 직렬화되어 클라이언트로 JSON 응답과 함께 500 상태 코드로 전송됩니다.

```tsx
import { createServerFn } from '@tanstack/start'

export const doStuff = createServerFn({ method: 'GET' }).handler(async () => {
  throw new Error('문제가 발생했습니다!')
})

// 사용 예시
function Test() {
  try {
    await doStuff()
  } catch (error) {
    console.error(error)
    // {
    //   message: "문제가 발생했습니다!",
    //   stack: "Error: 문제가 발생했습니다!\n    at doStuff (file:///path/to/file.ts:3:3)"
    // }
  }
}
```


## 라우트 생명주기 내에서 서버 함수 호출하기

서버 함수는 라우트의 `loader`, `beforeLoad` 또는 다른 라우터가 제어하는 API에서 일반적으로 호출할 수 있습니다. 이러한 API는 서버 함수에서 발생한 오류, 리다이렉트, notFound를 자동으로 처리할 수 있도록 설계되었습니다.

```tsx
import { getServerTime } from './getServerTime'

export const Route = createFileRoute('/time')({
  loader: async () => {
    const time = await getServerTime()

    return {
      time,
    }
  },
})
```


## 훅과 컴포넌트에서 서버 함수 호출하기

서버 함수는 `redirect`나 `notFound`를 발생시킬 수 있습니다. 이 오류들을 반드시 처리해야 하는 것은 아니지만, 적절히 처리하는 것이 좋습니다. 이를 더 쉽게 하기 위해 `@tanstack/start` 패키지는 서버 함수를 컴포넌트와 훅에 바인딩할 수 있는 `useServerFn` 훅을 제공합니다:

```tsx
import { useServerFn } from '@tanstack/start'
import { useQuery } from '@tanstack/react-query'
import { getServerTime } from './getServerTime'

export function Time() {
  const getTime = useServerFn(getServerTime)

  const timeQuery = useQuery({
    queryKey: 'time',
    queryFn: () => getTime(),
  })
}
```


## 서버 함수를 다른 곳에서 호출할 때 주의사항

서버 함수를 사용할 때, 리다이렉트(redirects)와 notFound 오류는 다음 상황에서만 자동으로 처리됩니다:

- 라우트 생명주기(Route lifecycles) 내에서 호출할 때
- useServerFn 훅을 사용하는 컴포넌트에서 호출할 때

이 외의 다른 위치에서 서버 함수를 호출할 경우, 여러분이 직접 이러한 상황을 처리해야 합니다.


## 리다이렉트

서버 함수에서 `redirect` 에러를 던져 사용자를 다른 URL로 리다이렉트할 수 있습니다. 이 기능은 인증, 권한 부여 또는 사용자를 다른 페이지로 이동시켜야 하는 상황에서 유용합니다.

- SSR(서버 사이드 렌더링) 중에는 302 응답과 함께 새로운 위치를 클라이언트로 보내 리다이렉트를 처리합니다.
- 클라이언트에서는 라우터가 자동으로 리다이렉트를 처리합니다. 이는 라우트 생명주기 내부나 `useServerFn` 훅을 사용하는 컴포넌트에서 호출된 경우에만 적용됩니다. 다른 곳에서 서버 함수를 호출하면 리다이렉트가 자동으로 처리되지 않습니다.

리다이렉트를 던지려면 `@tanstack/react-router` 패키지에서 내보낸 `redirect` 함수를 사용할 수 있습니다:

```tsx
import { redirect } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

export const doStuff = createServerFn({ method: 'GET' }).handler(async () => {
  // 사용자를 홈페이지로 리다이렉트
  throw redirect({
    to: '/',
  })
})
```

리다이렉트는 `router.navigate`, `useNavigate()`, `<Link>` 컴포넌트와 동일한 옵션을 모두 사용할 수 있습니다. 따라서 다음과 같은 것들을 자유롭게 전달할 수 있습니다:

- 경로 파라미터(Path Params)
- 검색 파라미터(Search Params)
- 해시(Hash)

또한 리다이렉트는 `status` 옵션을 통해 응답 상태 코드를 설정할 수도 있습니다:

```tsx
import { redirect } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

export const doStuff = createServerFn({ method: 'GET' }).handler(async () => {
  // 사용자를 홈페이지로 301 상태 코드와 함께 리다이렉트
  throw redirect({
    to: '/',
    status: 301,
  })
})
```

`href`를 사용해 외부 대상으로 리다이렉트할 수도 있습니다:

```tsx
import { redirect } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

export const auth = createServerFn({ method: 'GET' }).handler(async () => {
  // 사용자를 인증 제공자로 리다이렉트
  throw redirect({
    href: 'https://authprovider.com/login',
  })
})
```

> ⚠️ 서버 함수 내부에서 Vinxi의 `sendRedirect` 함수를 사용해 소프트 리다이렉트를 보내지 마세요. 이는 `Location` 헤더를 사용해 리다이렉트를 보내며, 클라이언트에서 전체 페이지 하드 네비게이션을 강제로 실행합니다.


## 리다이렉트 헤더 설정

리다이렉트 시 커스텀 헤더를 설정하려면 `headers` 옵션을 전달하면 됩니다.

```tsx
import { redirect } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

export const doStuff = createServerFn({ method: 'GET' }).handler(async () => {
  // 사용자를 홈페이지로 리다이렉트하며 커스텀 헤더를 설정
  throw redirect({
    to: '/',
    headers: {
      'X-Custom-Header': 'value',
    },
  })
})
```


## Not Found

`loader`나 `beforeLoad` 같은 라우트 생명주기에서 서버 함수를 호출할 때, 요청한 리소스를 찾을 수 없음을 라우터에게 알리기 위해 특별한 `notFound` 에러를 던질 수 있습니다. 이는 단순히 404 상태 코드를 반환하는 것보다 더 유용합니다. 커스텀 404 페이지를 렌더링하거나 에러를 원하는 방식으로 처리할 수 있기 때문입니다. 만약 라우트 생명주기 외부에서 사용되는 서버 함수에서 `notFound`를 던지면, 자동으로 처리되지 않습니다.

`notFound`를 던지려면 `@tanstack/react-router` 패키지에서 내보낸 `notFound` 함수를 사용하면 됩니다:

```tsx
import { createServerFn, notFound } from '@tanstack/start'

const getStuff = createServerFn({ method: 'GET' }).handler(async () => {
  // 랜덤으로 notFound 에러 반환
  if (Math.random() < 0.5) {
    throw notFound()
  }

  // 또는 데이터 반환
  return {
    stuff: 'stuff',
  }
})

export const Route = createFileRoute('/stuff')({
  loader: async () => {
    const stuff = await getStuff()

    return {
      stuff,
    }
  },
})
```

`notFound` 에러는 TanStack Router의 핵심 기능 중 하나입니다.


## 에러 처리

서버 함수가 (리다이렉트나 notFound가 아닌) 에러를 던지면, 이 에러는 직렬화되어 500 상태 코드와 함께 클라이언트로 JSON 응답으로 전송됩니다. 이는 디버깅에 유용하지만, 이러한 에러를 더 사용자 친화적인 방식으로 처리하고 싶을 수 있습니다. 여러분은 에러를 잡아내고, 라우트 라이프사이클, 컴포넌트, 또는 훅에서 일반적으로 처리하는 방식으로 이를 다룰 수 있습니다.

```tsx
import { createServerFn } from '@tanstack/start'

export const doStuff = createServerFn({ method: 'GET' }).handler(async () => {
  undefined.foo()
})

export const Route = createFileRoute('/stuff')({
  loader: async () => {
    try {
      await doStuff()
    } catch (error) {
      // 에러 처리:
      // error === {
      //   message: "undefined의 'foo' 속성을 읽을 수 없습니다",
      //   stack: "TypeError: undefined의 'foo' 속성을 읽을 수 없습니다\n    at doStuff (file:///path/to/file.ts:3:3)"
    }
  },
})
```


## JavaScript 없이 서버 함수 실행하기

JavaScript가 비활성화된 상태에서 서버 함수를 실행하는 방법은 단 하나뿐입니다. 바로 폼을 제출하는 것입니다.

이를 위해 페이지에 `form` 엘리먼트를 추가하고, [HTML 속성 `action`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/action)을 사용합니다.

> 여기서 중요한 점은 **HTML** 속성 `action`을 언급했다는 것입니다. 이 속성은 다른 모든 HTML 속성과 마찬가지로 문자열만을 허용합니다.
>
> React 19에서는 [`action`에 함수를 전달하는 기능을 추가](https://react.dev/reference/react-dom/components/form#form)했지만, 이는 React 전용 기능이며 HTML 표준의 일부가 아닙니다.

`action` 속성은 폼이 제출될 때 브라우저가 폼 데이터를 어디로 보낼지 지정합니다. 이 경우, 폼 데이터를 서버 함수로 보내고자 합니다.

이를 위해 서버 함수의 `url` 속성을 활용할 수 있습니다:

```ts
const yourFn = createServerFn()
  .validator((formData) => {
    const name = formData.get('name')

    if (!name) {
      throw new Error('이름은 필수입니다')
    }

    return name
  })
  .handler(async ({ data: name }) => {
    console.log(name) // 'John'
  })

console.info(yourFn.url)
```

그리고 이 URL을 폼의 `action` 속성에 전달합니다:

```tsx
function Component() {
  return (
    <form action={yourFn.url} method="POST">
      <input type="text" name="name" />
      <button type="submit">클릭하세요!</button>
    </form>
  )
}
```

폼이 제출되면 서버 함수가 실행됩니다.


### 자바스크립트 없이 서버 함수에 인자 전달하기

폼을 제출할 때 서버 함수에 인자를 전달하려면, `input` 엘리먼트와 `name` 속성을 사용하여 [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData)에 인자를 첨부할 수 있습니다. 이 데이터는 서버 함수로 전달됩니다.

```tsx
const yourFn = createServerFn()
  .validator((formData) => {
    // 폼 데이터가 FormData 타입인지 확인
    if (!(formData instanceof FormData)) {
      throw new Error('유효하지 않은 폼 데이터')
    }

    // 'age' 필드 값 가져오기
    const age = formData.get('age')

    // 'age' 필드가 비어있는지 확인
    if (!age) {
      throw new Error('나이는 필수 입력 항목입니다')
    }

    // 'age' 값을 문자열로 반환
    return age.toString()
  })
  .handler(async ({ data: formData }) => {
    // 'age' 값은 '123'이 됨
    const age = formData.get('age')
    // 추가 로직 처리
  })

function Component() {
  return (
    // 폼 데이터 타입을 `multipart/form-data`로 설정
    <form action={yourFn} method="POST" encType="multipart/form-data">
      <input type="text" name="age" defaultValue="123" />
      <button type="submit">클릭하세요!</button>
    </form>
  )
}
```

폼이 제출되면, 서버 함수는 폼의 데이터를 인자로 받아 실행됩니다.


### 자바스크립트 없이 서버 함수 반환 값 처리

자바스크립트가 활성화되었는지 여부와 상관없이, 서버 함수는 클라이언트에서 보낸 HTTP 요청에 대한 응답을 반환합니다.

자바스크립트가 활성화된 경우, 이 응답은 클라이언트의 자바스크립트 코드에서 서버 함수의 반환 값으로 접근할 수 있습니다.

```ts
const yourFn = createServerFn().handler(async () => {
  return 'Hello, world!'
})

// 자바스크립트가 비활성화된 경우 `.then`을 사용할 수 없음
yourFn().then(console.log)
```

하지만 자바스크립트가 비활성화된 경우, 클라이언트의 자바스크립트 코드에서 서버 함수의 반환 값에 접근할 방법이 없습니다.

대신, 서버 함수는 클라이언트에게 특정 방식으로 네비게이션하라는 응답을 제공할 수 있습니다.

TanStack Router의 `loader`와 결합하면, 자바스크립트가 비활성화된 상태에서도 싱글 페이지 애플리케이션과 유사한 경험을 제공할 수 있습니다. 이는 `loader`를 통해 새로운 데이터를 전달하면서 현재 페이지를 다시 로드하도록 브라우저에 지시함으로써 가능합니다:

```tsx
import * as fs from 'fs'
import { createFileRoute } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/start'

const filePath = 'count.txt'

async function readCount() {
  return parseInt(
    await fs.promises.readFile(filePath, 'utf-8').catch(() => '0'),
  )
}

const getCount = createServerFn({
  method: 'GET',
}).handler(() => {
  return readCount()
})

const updateCount = createServerFn({ method: 'POST' })
  .validator((formData) => {
    if (!(formData instanceof FormData)) {
      throw new Error('Invalid form data')
    }

    const addBy = formData.get('addBy')

    if (!addBy) {
      throw new Error('addBy is required')
    }

    return parseInt(addBy.toString())
  })
  .handler(async ({ data: addByAmount }) => {
    const count = await readCount()
    await fs.promises.writeFile(filePath, `${count + addByAmount}`)
    // 페이지를 다시 로드하여 loader를 다시 트리거
    return new Response('ok', { status: 301, headers: { Location: '/' } })
  })

export const Route = createFileRoute('/')({
  component: Home,
  loader: async () => await getCount(),
})

function Home() {
  const state = Route.useLoaderData()

  return (
    
      <form
        action={updateCount.url}
        method="POST"
        encType="multipart/form-data"
      >
        
        Add
      
      {state}
    
  )
}
```

이 코드는 자바스크립트가 비활성화된 환경에서도 페이지를 다시 로드하여 데이터를 업데이트하고, 사용자에게 새로운 데이터를 보여주는 방식을 보여줍니다.


## 서버 함수는 어떻게 컴파일될까?

서버 함수는 클라이언트 번들에서 추출되어 별도의 서버 번들로 이동합니다. 서버에서는 이 함수를 그대로 실행하고, 결과를 클라이언트로 보냅니다. 클라이언트에서는 서버 함수가 서버로 요청을 프록시(proxy)하여, 서버에서 함수를 실행하고 결과를 다시 클라이언트로 보내는 방식으로 동작합니다. 이 모든 과정은 `fetch`를 통해 이루어집니다.

이 과정은 다음과 같이 진행됩니다:

1. 파일에서 `createServerFn`이 발견되면, 내부 함수에 `use server` 지시어가 있는지 확인합니다.
2. `use server` 지시어가 없으면, 함수 상단에 이를 추가합니다.
3. 클라이언트에서는 내부 함수를 클라이언트 번들에서 추출하여 별도의 서버 번들로 이동시킵니다.
4. 클라이언트 측의 서버 함수는 추출된 함수를 실행하기 위해 서버로 요청을 보내는 프록시 함수로 대체됩니다.
5. 서버에서는 서버 함수를 추출하지 않고, 그대로 실행합니다.
6. 추출이 완료된 후, 각 번들은 사용되지 않는 코드를 제거하기 위해 데드 코드 제거(dead-code elimination) 과정을 적용합니다.

이렇게 하면 클라이언트와 서버 간의 효율적인 함수 실행이 가능해집니다.


