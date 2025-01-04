# Table of Contents

- [API 라우트](#api-라우트)
  - [파일 기반 라우트 규칙](#파일-기반-라우트-규칙)
  - [중첩 디렉터리 vs 파일 이름](#중첩-디렉터리-vs-파일-이름)
  - [엔트리 핸들러 설정하기](#엔트리-핸들러-설정하기)
  - [API 라우트 정의하기](#api-라우트-정의하기)
  - [동적 경로 파라미터](#동적-경로-파라미터)
  - [와일드카드/스플랫 파라미터](#와일드카드스플랫-파라미터)
  - [요청 본문 처리하기](#요청-본문-처리하기)
  - [JSON 응답하기](#json-응답하기)
  - [`json` 헬퍼 함수 사용하기](#json-헬퍼-함수-사용하기)
  - [상태 코드 응답하기](#상태-코드-응답하기)
  - [응답에 헤더 설정하기](#응답에-헤더-설정하기)

# API 라우트

API 라우트는 TanStack Start의 강력한 기능 중 하나로, 별도의 서버 없이 애플리케이션 내에서 서버 사이드 엔드포인트를 생성할 수 있게 해줍니다. API 라우트는 폼 제출 처리, 사용자 인증 등 다양한 작업에 유용합니다.

기본적으로 API 라우트는 프로젝트의 `./app/routes/api` 디렉토리에 정의되며, TanStack Start 서버에 의해 자동으로 처리됩니다.

> 🧠 기본적으로 API 라우트는 `/api`로 시작하며, 애플리케이션과 동일한 서버에서 제공됩니다. TanStack Start 설정에서 `apiBase`를 변경하여 이 기본 경로를 커스텀할 수 있습니다.

이 가이드에서 다루는 주제:

- [파일 라우트 규칙](#파일-라우트-규칙)
- [중첩 디렉토리 vs 파일 이름](#중첩-디렉토리-vs-파일-이름)
- [엔트리 핸들러 설정](#엔트리-핸들러-설정)
- [API 라우트 정의](#api-라우트-정의)
- [동적 경로 파라미터](#동적-경로-파라미터)
- [와일드카드/스플랫 파라미터](#와일드카드스플랫-파라미터)
- [요청 본문 처리](#요청-본문-처리)
- [JSON 응답](#json-응답)
- [`json` 헬퍼 함수 사용](#json-헬퍼-함수-사용)
- [상태 코드로 응답](#상태-코드로-응답)
- [응답 헤더 설정](#응답-헤더-설정)


## 파일 기반 라우트 규칙

TanStack Start의 API 라우트는 TanStack Router와 동일한 파일 기반 라우팅 규칙을 따릅니다. 이는 `routes` 디렉토리 내에서 `api`로 시작하는 파일(설정 가능)이 API 라우트로 처리된다는 것을 의미합니다. 몇 가지 예제를 살펴보겠습니다:

- `routes/api.users.ts`는 `/api/users`에 API 라우트를 생성합니다.
- `routes/api/users.ts`도 `/api/users`에 API 라우트를 생성합니다.
- `routes/api/users.index.ts`도 `/api/users`에 API 라우트를 생성합니다.
- `routes/api/users/$id.ts`는 `/api/users/$id`에 API 라우트를 생성합니다.
- `routes/api/users/$id/posts.ts`는 `/api/users/$id/posts`에 API 라우트를 생성합니다.
- `routes/api.users.$id.posts.ts`도 `/api/users/$id/posts`에 API 라우트를 생성합니다.
- `routes/api/file/$.ts`는 `/api/file/$`에 API 라우트를 생성합니다.

`api`로 시작하는 라우트 파일은 해당 API 라우트 경로에 대한 핸들러로 간주할 수 있습니다.

각 라우트는 단 하나의 핸들러 파일만 가질 수 있다는 점을 기억해야 합니다. 예를 들어, `/api/users` 요청 경로에 해당하는 `routes/api/users.ts` 파일이 있다면, 동일한 라우트로 해석되는 다른 파일을 가질 수 없습니다. 예를 들어:

- `routes/api/users.index.ts`
- `routes/api.users.ts`
- `routes/api.users.index.ts`

❗ 한 가지 더, API 라우트는 경로 없는/레이아웃 라우트나 병렬 라우트 개념이 없습니다. 따라서 다음과 같은 파일은:

- `routes/api/_layout/users.ts`는 `/api/_layout/users`로 해석되며, `/api/users`로 해석되지 않습니다.


## 중첩 디렉터리 vs 파일 이름

위 예제에서 여러분은 파일 이름 규칙이 유연하며, 디렉터리와 파일 이름을 자유롭게 조합할 수 있다는 것을 눈치챘을 겁니다. 이는 의도된 설계로, 여러분의 애플리케이션에 맞게 API 라우트를 조직화할 수 있도록 해줍니다. 이에 대해 더 자세히 알고 싶다면 [TanStack Router 파일 기반 라우팅 가이드](../guide/file-based-routing.md#s-or-s)를 참고하세요.


## 엔트리 핸들러 설정하기

TanStack Start 프로젝트에서는 API로 들어오는 요청을 처리하고 적절한 API 라우트 핸들러로 전달하기 위해 엔트리 핸들러가 필요합니다. 이를 위해 프로젝트에 `app/api.ts` 파일을 생성합니다:

```ts
// app/api.ts
import {
  createStartAPIHandler,
  defaultAPIFileRouteHandler,
} from '@tanstack/start/api'

export default createStartAPIHandler(defaultAPIFileRouteHandler)
```

이 파일은 들어오는 요청을 적절한 API 라우트 핸들러로 라우팅하는 데 사용될 API 핸들러를 생성하는 역할을 합니다. `defaultAPIFileRouteHandler`는 들어오는 요청에 따라 자동으로 적절한 API 라우트 핸들러를 로드하고 실행하는 헬퍼 함수입니다.


## API 라우트 정의하기

API 라우트는 `createAPIFileRoute` 함수를 호출하여 APIRoute 인스턴스를 내보냅니다. TanStack Router의 다른 파일 기반 라우트와 마찬가지로, 이 함수의 첫 번째 인자는 라우트의 경로입니다. 반환된 함수는 각 HTTP 메서드에 대한 라우트 핸들러를 정의하는 객체와 함께 다시 호출됩니다.

> [!TIP]
> 이미 개발 서버가 실행 중이라면, 새로운 API 라우트를 생성할 때 초기 핸들러가 자동으로 설정됩니다. 이후에는 필요에 따라 핸들러를 커스터마이징할 수 있습니다.

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/hello')({
  GET: async ({ request }) => {
    return new Response('Hello, World! from ' + request.url)
  },
})
```

각 HTTP 메서드 핸들러는 다음과 같은 속성을 가진 객체를 받습니다:

- `request`: 들어오는 요청 객체입니다. `Request` 객체에 대한 자세한 내용은 [MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/API/Request)에서 확인할 수 있습니다.
- `params`: 라우트의 동적 경로 매개변수를 포함하는 객체입니다. 예를 들어, 라우트 경로가 `/users/$id`이고 요청이 `/users/123`으로 이루어졌다면, `params`는 `{ id: '123' }`이 됩니다. 동적 경로 매개변수와 와일드카드 매개변수는 이 가이드의 뒷부분에서 다룰 예정입니다.

요청을 처리한 후에는 `Response` 객체나 `Promise<Response>`를 반환해야 합니다. 이는 새로운 `Response` 객체를 생성하고 핸들러에서 반환함으로써 수행할 수 있습니다. `Response` 객체에 대한 자세한 내용은 [MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/API/Response)에서 확인할 수 있습니다.


## 동적 경로 파라미터

API 라우트는 동적 경로 파라미터를 지원합니다. 이는 `$` 뒤에 파라미터 이름을 붙여 표시합니다. 예를 들어, `routes/api/users/$id.ts`라는 파일은 `/api/users/$id` 경로에 동적 `id` 파라미터를 받는 API 라우트를 생성합니다.

```ts
// routes/api/users/$id.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/users/$id')({
  GET: async ({ params }) => {
    const { id } = params
    return new Response(`User ID: ${id}`)
  },
})

// /api/users/123에 접속하면 응답 확인 가능
// User ID: 123
```

하나의 라우트에 여러 동적 경로 파라미터를 사용할 수도 있습니다. 예를 들어, `routes/api/users/$id/posts/$postId.ts`라는 파일은 `/api/users/$id/posts/$postId` 경로에 두 개의 동적 파라미터를 받는 API 라우트를 생성합니다.

```ts
// routes/api/users/$id/posts/$postId.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/users/$id/posts/$postId')({
  GET: async ({ params }) => {
    const { id, postId } = params
    return new Response(`User ID: ${id}, Post ID: ${postId}`)
  },
})

// /api/users/123/posts/456에 접속하면 응답 확인 가능
// User ID: 123, Post ID: 456
```


## 와일드카드/스플랫 파라미터

API 라우트는 경로 끝에 와일드카드 파라미터를 지원합니다. 이는 `$`로 표시되며, 뒤에 아무것도 붙지 않습니다. 예를 들어, `routes/api/file/$.ts`라는 파일은 `/api/file/$` 경로에 API 라우트를 생성하며, 와일드카드 파라미터를 받아들입니다.

```ts
// routes/api/file/$.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/file/$')({
  GET: async ({ params }) => {
    const { _splat } = params
    return new Response(`File: ${_splat}`)
  },
})

// /api/file/hello.txt로 접속하면 응답을 확인할 수 있습니다.
// File: hello.txt
```

이 예제에서 `_splat`은 와일드카드 파라미터로, `/api/file/hello.txt`와 같은 경로에서 `hello.txt` 값을 가져옵니다. 이 값을 활용해 동적으로 응답을 생성할 수 있습니다.


## 요청 본문 처리하기

POST 요청을 처리하려면 라우트 객체에 `POST` 핸들러를 추가하면 됩니다. 핸들러는 첫 번째 인자로 요청 객체를 받으며, `request.json()` 메서드를 사용해 요청 본문에 접근할 수 있습니다.

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/hello')({
  POST: async ({ request }) => {
    const body = await request.json()
    return new Response(`Hello, ${body.name}!`)
  },
})

// /api/hello로 POST 요청을 보내고 { "name": "Tanner" } 같은 JSON 본문을 포함시키면
// Hello, Tanner! 라는 응답을 받습니다.
```

이 방법은 `PUT`, `PATCH`, `DELETE` 같은 다른 HTTP 메서드에도 적용됩니다. 라우트 객체에 이러한 메서드에 대한 핸들러를 추가하고, 적절한 메서드를 사용해 요청 본문에 접근할 수 있습니다.

`request.json()` 메서드는 요청의 JSON 본문을 파싱한 결과를 반환하는 `Promise`를 반환한다는 점을 기억해야 합니다. 본문에 접근하려면 결과를 `await`로 기다려야 합니다.

이것은 API 라우트에서 POST 요청을 처리하는 일반적인 패턴입니다. `request.text()`나 `request.formData()` 같은 다른 메서드를 사용해 요청 본문에 접근할 수도 있습니다.


## JSON 응답하기

Response 객체를 사용해 JSON을 반환할 때는 다음과 같은 패턴이 자주 사용됩니다:

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/hello')({
  GET: async ({ request }) => {
    return new Response(JSON.stringify({ message: 'Hello, World!' }), {
      headers: {
        'Content-Type': 'application/json',
      },
    })
  },
})

// /api/hello로 접속하면 응답을 확인할 수 있습니다
// {"message":"Hello, World!"}
```

이 코드는 `/hello` 경로로 GET 요청이 들어오면 JSON 형식으로 `{"message":"Hello, World!"}`를 반환합니다. `Content-Type` 헤더를 `application/json`으로 설정해 클라이언트가 응답을 JSON으로 인식하도록 합니다.


## `json` 헬퍼 함수 사용하기

`json` 헬퍼 함수를 사용하면 `Content-Type` 헤더를 자동으로 `application/json`으로 설정하고, JSON 객체를 직렬화해 줍니다.

```ts
// routes/api/hello.ts
import { json } from '@tanstack/start'
import { createAPIFileRoute } from '@tanstack/start/api'

export const APIRoute = createAPIFileRoute('/hello')({
  GET: async ({ request }) => {
    return json({ message: 'Hello, World!' })
  },
})

// /api/hello로 접속하면 응답을 확인할 수 있습니다.
// {"message":"Hello, World!"}
```


## 상태 코드 응답하기

응답의 상태 코드를 설정하는 방법은 두 가지가 있습니다:

1. `Response` 생성자의 두 번째 인자로 상태 코드를 전달하는 방법

   ```ts
   // routes/api/hello.ts
   import { json } from '@tanstack/start'
   import { createAPIFileRoute } from '@tanstack/start/api'

   export const APIRoute = createAPIFileRoute('/users/$id')({
     GET: async ({ request, params }) => {
       const user = await findUser(params.id)
       if (!user) {
         return new Response('User not found', {
           status: 404,
         })
       }
       return json(user)
     },
   })
   ```

2. `vinxi/http`의 `setResponseStatus` 헬퍼 함수를 사용하는 방법

   ```ts
   // routes/api/hello.ts
   import { json } from '@tanstack/start'
   import { createAPIFileRoute } from '@tanstack/start/api'
   import { setResponseStatus } from 'vinxi/http'

   export const APIRoute = createAPIFileRoute('/users/$id')({
     GET: async ({ request, params }) => {
       const user = await findUser(params.id)
       if (!user) {
         setResponseStatus(404)
         return new Response('User not found')
       }
       return json(user)
     },
   })
   ```

이 예제에서는 사용자를 찾지 못했을 때 `404` 상태 코드를 반환합니다. 이 방법을 사용하면 유효한 HTTP 상태 코드를 자유롭게 설정할 수 있습니다.


## 응답에 헤더 설정하기

때로는 응답에 헤더를 설정해야 할 때가 있습니다. 이는 다음과 같은 방법으로 할 수 있습니다:

- `Response` 생성자의 두 번째 인자로 객체를 전달하는 방법

  ```ts
  // routes/api/hello.ts
  import { createAPIFileRoute } from '@tanstack/start/api'

  export const APIRoute = createAPIFileRoute('/hello')({
    GET: async ({ request }) => {
      return new Response('Hello, World!', {
        headers: {
          'Content-Type': 'text/plain',
        },
      })
    },
  })

  // /api/hello로 접속하면 응답 확인 가능
  // Hello, World!
  ```

- 또는 `vinxi/http`의 `setHeaders` 헬퍼 함수를 사용하는 방법

  ```ts
  // routes/api/hello.ts
  import { createAPIFileRoute } from '@tanstack/start/api'
  import { setHeaders } from 'vinxi/http'

  export const APIRoute = createAPIFileRoute('/hello')({
    GET: async ({ request }) => {
      setHeaders({
        'Content-Type': 'text/plain',
      })
      return new Response('Hello, World!')
    },
  })
  ```


