# Table of Contents

- [Middleware](#middleware)
  - [미들웨어란 무엇인가?](#미들웨어란-무엇인가)
  - [미들웨어로 할 수 있는 일들](#미들웨어로-할-수-있는-일들)
  - [미들웨어 정의하기](#미들웨어-정의하기)
  - [미들웨어 메서드](#미들웨어-메서드)
  - [`middleware` 메서드](#middleware-메서드)
  - [`validator` 메서드](#validator-메서드)
  - [`server` 메서드](#server-메서드)
  - [`next`에서 필요한 결과 반환하기](#next에서-필요한-결과-반환하기)
  - [`next`를 통해 다음 미들웨어에 컨텍스트 제공하기](#next를-통해-다음-미들웨어에-컨텍스트-제공하기)
  - [클라이언트 사이드 로직](#클라이언트-사이드-로직)
  - [클라이언트 측 데이터 검증](#클라이언트-측-데이터-검증)
  - [`client` 메서드](#client-메서드)
  - [클라이언트 컨텍스트를 서버로 전송하기](#클라이언트-컨텍스트를-서버로-전송하기)
  - [클라이언트에서 보낸 컨텍스트 보안](#클라이언트에서-보낸-컨텍스트-보안)
  - [서버 컨텍스트를 클라이언트로 전송하기](#서버-컨텍스트를-클라이언트로-전송하기)
  - [서버 응답 읽기/수정하기](#서버-응답-읽기수정하기)
  - [클라이언트 요청 수정하기](#클라이언트-요청-수정하기)
  - [미들웨어 사용하기](#미들웨어-사용하기)
  - [전역 미들웨어](#전역-미들웨어)
    - [전역 미들웨어 타입 안전성](#전역-미들웨어-타입-안전성)
  - [미들웨어 실행 순서](#미들웨어-실행-순서)
  - [환경 기반 트리 쉐이킹](#환경-기반-트리-쉐이킹)

# Middleware





## 미들웨어란 무엇인가?

미들웨어는 `createServerFn`으로 생성한 서버 함수의 동작을 커스텀할 수 있게 해줍니다. 공유된 검증, 컨텍스트 등 다양한 기능을 추가할 수 있습니다. 미들웨어는 다른 미들웨어에 의존할 수도 있어, 계층적이고 순차적으로 실행되는 작업 체인을 만들 수 있습니다.


## 미들웨어로 할 수 있는 일들

- **인증(Authentication)**: 서버 함수를 실행하기 전에 사용자의 신원을 확인합니다.
- **권한 부여(Authorization)**: 사용자가 서버 함수를 실행할 수 있는 권한이 있는지 확인합니다.
- **로깅(Logging)**: 요청, 응답, 오류를 기록합니다.
- **관측 가능성(Observability)**: 메트릭, 트레이스, 로그를 수집합니다.
- **컨텍스트 제공(Provide Context)**: 다른 미들웨어나 서버 함수에서 사용할 수 있도록 요청 객체에 데이터를 첨부합니다.
- **오류 처리(Error Handling)**: 일관된 방식으로 오류를 처리합니다.
- 그리고 더 많은 것들이 가능합니다! 여러분의 상상력에 따라 다양한 기능을 구현할 수 있습니다!


## 미들웨어 정의하기

미들웨어는 `createMiddleware` 함수를 사용하여 정의합니다. 이 함수는 `Middleware` 객체를 반환하며, 이 객체는 `middleware`, `validator`, `server`, `client`와 같은 메서드를 통해 추가적으로 커스터마이징할 수 있습니다.

```tsx
import { createMiddleware } from '@tanstack/start'

const loggingMiddleware = createMiddleware().server(async ({ next, data }) => {
  console.log('요청 받음:', data)
  const result = await next()
  console.log('응답 처리 완료:', result)
  return result
})
```


## 미들웨어 메서드

미들웨어를 커스터마이징할 수 있는 여러 메서드가 제공됩니다. TypeScript를 사용한다면(그렇게 되길 바랍니다), 이러한 메서드의 순서는 타입 시스템에 의해 강제되어 최대한의 타입 추론과 안전성을 보장합니다.

- `middleware`: 미들웨어 체인에 미들웨어를 추가합니다.
- `validator`: 데이터 객체를 이 미들웨어와 중첩된 미들웨어에 전달하기 전에 수정합니다.
- `server`: 서버 측 로직을 정의합니다. 이 로직은 중첩된 미들웨어와 최종적으로 서버 함수가 실행되기 전에 실행되며, 결과를 다음 미들웨어에 제공합니다.
- `client`: 클라이언트 측 로직을 정의합니다. 이 로직은 중첩된 미들웨어와 최종적으로 클라이언트 측 RPC 함수(또는 서버 측 함수)가 실행되기 전에 실행되며, 결과를 다음 미들웨어에 제공합니다.


## `middleware` 메서드

`middleware` 메서드는 현재 미들웨어가 실행되기 **전에** 실행될 미들웨어 체인에 의존성을 추가하는 데 사용됩니다. 미들웨어 객체 배열을 인자로 전달하여 `middleware` 메서드를 호출하면 됩니다.

```tsx
const loggingMiddleware = createMiddleware().middleware([
  authMiddleware,
  loggingMiddleware,
])
```

부모 미들웨어로부터 타입 안전성과 페이로드 검증 기능도 상속받습니다!


## `validator` 메서드

`validator` 메서드는 데이터 객체가 이 미들웨어, 중첩된 미들웨어, 그리고 최종적으로 서버 함수에 전달되기 전에 데이터 객체를 수정하는 데 사용됩니다. 이 메서드는 데이터 객체를 받아서 검증된(그리고 선택적으로 수정된) 데이터 객체를 반환하는 함수를 인자로 받아야 합니다. 보통 `zod`와 같은 검증 라이브러리를 사용하여 이를 수행합니다. 다음은 예제입니다:

```tsx
import { z } from 'zod'

const mySchema = z.object({
  workspaceId: z.string(),
})

const workspaceMiddleware = createMiddleware()
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```


## `server` 메서드

`server` 메서드는 **서버 측** 로직을 정의하는 데 사용됩니다. 이 메서드는 중첩된 미들웨어와 최종적으로 서버 함수를 실행하기 전과 후에 실행될 로직을 정의합니다. 이 메서드는 다음과 같은 속성을 가진 객체를 인자로 받습니다:

- `next`: 호출 시 체인 내의 다음 미들웨어를 실행하는 함수입니다.
- `data`: 서버 함수에 전달된 데이터 객체입니다.
- `context`: 부모 미들웨어에서 전달된 데이터를 저장하는 객체입니다. 이 객체는 자식 미들웨어에 전달될 추가 데이터로 확장할 수 있습니다.

```javascript
server({
  next: () => {
    // 다음 미들웨어 실행
  },
  data: {
    // 서버 함수에 전달된 데이터
  },
  context: {
    // 부모 미들웨어에서 전달된 데이터
  }
});
```


## `next`에서 필요한 결과 반환하기

`next` 함수는 미들웨어 체인에서 다음 미들웨어를 실행하는 데 사용됩니다. **체인이 계속 실행되려면 `next` 함수의 결과를 `await`하고 반환(또는 직접 반환)해야 합니다.**

```tsx
const loggingMiddleware = createMiddleware().server(async ({ next }) => {
  console.log('요청 받음')
  const result = await next()
  console.log('응답 처리 완료')
  return result
})
```


## `next`를 통해 다음 미들웨어에 컨텍스트 제공하기

`next` 함수는 선택적으로 `context` 속성을 가진 객체를 인자로 받을 수 있습니다. 이 `context` 값에 전달한 모든 속성은 부모 컨텍스트와 병합되어 다음 미들웨어에 제공됩니다.

```tsx
const awesomeMiddleware = createMiddleware().server(({ next }) => {
  return next({
    context: {
      isAwesome: Math.random() > 0.5,
    },
  })
})

const loggingMiddleware = createMiddleware().server(
  async ({ next, context }) => {
    console.log('Is awesome?', context.isAwesome)
    return next()
  },
)
```

위 예제에서 `awesomeMiddleware`는 `isAwesome` 속성을 컨텍스트에 추가합니다. 이 컨텍스트는 `loggingMiddleware`에서 접근할 수 있습니다. `loggingMiddleware`는 `context.isAwesome` 값을 콘솔에 출력한 후 다음 미들웨어로 제어를 넘깁니다.


## 클라이언트 사이드 로직

서버 함수가 주로 서버 사이드에서 실행되는 작업이더라도, 클라이언트에서 보내는 RPC 요청과 관련된 클라이언트 사이드 로직이 여전히 많이 존재합니다. 이는 클라이언트 사이드에서 실행될 미들웨어를 정의할 수 있음을 의미합니다. 이 미들웨어는 중첩된 미들웨어와 최종적으로 RPC 함수 및 클라이언트로의 응답 주변에서 실행됩니다.


## 클라이언트 측 데이터 검증

기본적으로 미들웨어 검증은 클라이언트 번들 크기를 작게 유지하기 위해 서버에서만 수행됩니다. 하지만 `createMiddleware` 함수에 `validateClient: true` 옵션을 전달하여 클라이언트 측에서도 데이터를 검증할 수 있습니다. 이렇게 하면 데이터가 서버로 전송되기 전에 클라이언트 측에서 검증되므로, 불필요한 통신을 줄일 수 있습니다.

> 왜 클라이언트 측 검증 스키마를 따로 설정할 수 없나요?
>
> 클라이언트 측 검증 스키마는 서버 측 스키마에서 파생됩니다. 클라이언트 측 검증 스키마는 데이터가 서버로 전송되기 전에 검증하는 데 사용되기 때문입니다. 만약 클라이언트 측 스키마가 서버 측 스키마와 다르다면, 서버는 예상치 못한 데이터를 받게 되어 예기치 않은 동작이 발생할 수 있습니다.

```tsx
const workspaceMiddleware = createMiddleware({ validateClient: true })
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```


## `client` 메서드

클라이언트 미들웨어 로직은 `Middleware` 객체의 `client` 메서드를 사용해 정의합니다. 이 메서드는 클라이언트 측 로직을 정의하는 데 사용되며, 중첩된 미들웨어와 최종적으로 클라이언트 측 RPC 함수(또는 SSR을 수행하거나 다른 서버 함수에서 이 함수를 호출하는 경우 서버 측 함수) 전후에 실행됩니다.

**클라이언트 측 미들웨어 로직은 `server` 메서드로 생성된 로직과 많은 API를 공유하지만, 클라이언트 측에서 실행됩니다.** 여기에는 다음이 포함됩니다:

- 체인을 계속 진행하려면 `next` 함수를 호출해야 합니다.
- `next` 함수를 통해 다음 클라이언트 미들웨어에 컨텍스트를 제공할 수 있습니다.
- 다음 클라이언트 미들웨어에 전달되기 전에 데이터 객체를 수정할 수 있습니다.

`server` 함수와 마찬가지로, 이 메서드도 다음과 같은 속성을 가진 객체를 받습니다:

- `next`: 호출 시 체인의 다음 클라이언트 미들웨어를 실행하는 함수
- `data`: 클라이언트 함수에 전달된 데이터 객체
- `context`: 부모 미들웨어에서 전달된 데이터를 저장하는 객체. 자식 미들웨어에 전달될 추가 데이터로 확장할 수 있습니다.

```tsx
const loggingMiddleware = createMiddleware().client(async ({ next }) => {
  console.log('요청 전송됨')
  const result = await next()
  console.log('응답 수신됨')
  return result
})
```


## 클라이언트 컨텍스트를 서버로 전송하기

**클라이언트 컨텍스트는 기본적으로 서버로 전송되지 않습니다.** 이는 의도치 않게 큰 데이터를 서버로 보낼 수 있기 때문입니다. 클라이언트 컨텍스트를 서버로 전송해야 한다면, `next` 함수를 호출할 때 `sendContext` 속성과 함께 전송할 데이터를 포함한 객체를 전달해야 합니다. `sendContext`에 전달된 모든 속성은 병합되고 직렬화되어 서버로 전송되며, 중첩된 서버 미들웨어의 일반 컨텍스트 객체에서 사용할 수 있습니다.

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        // 워크스페이스 ID를 서버로 전송
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 클라이언트에서 전송된 워크스페이스 ID를 사용
    console.log('Workspace ID:', context.workspaceId)
    return next()
  })
```


## 클라이언트에서 보낸 컨텍스트 보안

위 예제에서 클라이언트에서 보낸 컨텍스트는 타입 안전성을 갖추고 있지만, 런타임에서 검증이 필수는 아닙니다. 만약 동적으로 생성된 사용자 데이터를 컨텍스트를 통해 전달한다면, 보안 문제가 발생할 수 있습니다. 따라서 **클라이언트에서 서버로 동적 데이터를 컨텍스트를 통해 보낼 때는, 서버 측 미들웨어에서 사용하기 전에 반드시 검증해야 합니다.** 다음은 예제입니다:

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 사용하기 전에 워크스페이스 ID 검증
    const workspaceId = zodValidator(z.number()).parse(context.workspaceId)
    console.log('Workspace ID:', workspaceId)
    return next()
  })
```


## 서버 컨텍스트를 클라이언트로 전송하기

클라이언트 컨텍스트를 서버로 전송하는 것과 마찬가지로, `next` 함수를 호출할 때 `sendContext` 속성과 객체를 사용하여 서버 컨텍스트를 클라이언트로 전송할 수 있습니다. `sendContext`에 전달된 모든 속성은 병합되고 직렬화되어 응답과 함께 클라이언트로 전송되며, 중첩된 클라이언트 미들웨어의 일반 컨텍스트 객체에서 사용할 수 있습니다.

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    const result = next()
    // 와! 서버에서 시간을 받았어요!
    console.log('서버에서 받은 시간:', result.context.timeFromServer)
  })
  .server(async ({ next }) => {
    return next({
      sendContext: {
        // 현재 시간을 클라이언트로 전송
        timeFromServer: new Date(),
      },
    })
  })
```


## 서버 응답 읽기/수정하기

`server` 메서드를 사용하는 미들웨어는 서버 함수와 동일한 컨텍스트에서 실행됩니다. 따라서 요청 헤더, 상태 코드 등을 읽고 수정하기 위해 [서버 함수 컨텍스트 유틸리티](./server-functions#server-function-context)를 그대로 활용할 수 있습니다.


## 클라이언트 요청 수정하기

`client` 메서드를 사용하는 미들웨어는 서버 함수와 **완전히 다른 클라이언트 측 컨텍스트**에서 실행됩니다. 따라서 요청을 읽고 수정하는 데 동일한 유틸리티를 사용할 수 없습니다. 하지만 `next` 함수를 호출할 때 추가 속성을 반환하여 요청을 수정할 수 있습니다. 현재 지원되는 속성은 다음과 같습니다:

- `headers`: 요청에 추가할 헤더를 포함하는 객체입니다.

다음은 이 미들웨어를 사용하여 모든 요청에 `Authorization` 헤더를 추가하는 예제입니다:

```tsx
import { getToken } from 'my-auth-library'

const authMiddleware = createMiddleware().client(async ({ next }) => {
  return next({
    headers: {
      Authorization: `Bearer ${getToken()}`,
    },
  })
})
```


## 미들웨어 사용하기

미들웨어는 두 가지 방식으로 사용할 수 있습니다:

- **전역 미들웨어(Global Middleware)**: 모든 요청에 대해 실행되어야 하는 미들웨어입니다.
- **서버 함수 미들웨어(Server Function Middleware)**: 특정 서버 함수에 대해서만 실행되어야 하는 미들웨어입니다.


## 전역 미들웨어

전역 미들웨어는 `registerGlobalMiddleware` 함수를 사용해 등록합니다. 이 함수는 전역 미들웨어 배열에 추가할 미들웨어 배열을 인자로 받습니다. 현재는 전역 미들웨어를 등록한 후 제거할 수 있는 방법이 없습니다. 만약 이 기능이 필요하다면, GitHub에 이슈를 열어 알려주세요.

전역 미들웨어를 등록하는 예제는 다음과 같습니다:

```tsx
import { registerGlobalMiddleware } from '@tanstack/start'

registerGlobalMiddleware({
  middleware: [authMiddleware, loggingMiddleware],
})
```


### 전역 미들웨어 타입 안전성

전역 미들웨어 타입은 서버 함수 자체와 **분리**되어 있습니다. 이는 전역 미들웨어가 서버 함수나 다른 서버 함수 전용 미들웨어에 추가적인 컨텍스트를 제공하더라도, 해당 타입이 자동으로 서버 함수나 다른 서버 함수 전용 미들웨어로 전달되지 않음을 의미합니다.

```tsx
// globalMiddleware.ts
registerGlobalMiddleware({
  middleware: [authMiddleware],
})

// serverFunction.ts
const authMiddleware = createMiddleware().server(({ next, context }) => {
  console.log(context.user) // <-- 이 부분은 타입이 지정되지 않습니다!
  // ...
})
```

이 문제를 해결하려면, 참조하려는 전역 미들웨어를 서버 함수의 미들웨어 배열에 추가하세요. **전역 미들웨어는 단일 항목(전역 인스턴스)으로 중복 제거되며, 서버 함수는 올바른 타입을 받게 됩니다.**

다음은 이 방법이 어떻게 동작하는지 보여주는 예제입니다:

```tsx
import { authMiddleware } from './authMiddleware'

const fn = createServerFn()
  .middleware([authMiddleware])
  .handler(async ({ context }) => {
    console.log(context.user)
    // ...
  })
```


## 미들웨어 실행 순서

미들웨어는 의존성 순서대로 실행됩니다. 전역 미들웨어부터 시작하여 서버 함수 미들웨어 순으로 실행됩니다. 다음 예제는 아래 순서대로 로그를 출력합니다:

- `globalMiddleware1`
- `globalMiddleware2`
- `a`
- `b`
- `c`
- `d`

```tsx
const globalMiddleware1 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware1')
  return next()
})

const globalMiddleware2 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware2')
  return next()
})

registerGlobalMiddleware({
  middleware: [globalMiddleware1, globalMiddleware2],
})

const a = createMiddleware().server(async ({ next }) => {
  console.log('a')
  return next()
})

const b = createMiddleware()
  .middleware([a])
  .server(async ({ next }) => {
    console.log('b')
    return next()
  })

const c = createMiddleware()
  .middleware()
  .server(async ({ next }) => {
    console.log('c')
    return next()
  })

const d = createMiddleware()
  .middleware([b, c])
  .server(async () => {
    console.log('d')
  })

const fn = createServerFn()
  .middleware([d])
  .server(async () => {
    console.log('fn')
  })
```


## 환경 기반 트리 쉐이킹

미들웨어 기능은 각 번들에 대해 환경에 따라 트리 쉐이킹이 적용됩니다.

- 서버에서는 트리 쉐이킹이 적용되지 않습니다. 따라서 미들웨어에서 사용된 모든 코드가 서버 번들에 포함됩니다.
- 클라이언트에서는 서버 전용 코드가 클라이언트 번들에서 제거됩니다. 이는 `server` 메서드에서 사용된 코드가 항상 클라이언트 번들에서 제거됨을 의미합니다. `validateClient`가 `true`로 설정된 경우, 클라이언트 측 검증 코드가 클라이언트 번들에 포함됩니다. 그렇지 않으면 `data` 검증 코드도 제거됩니다.


