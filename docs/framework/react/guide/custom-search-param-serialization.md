# Table of Contents

- [커스텀 검색 파라미터 직렬화](#커스텀-검색-파라미터-직렬화)
  - [Base64 사용하기](#base64-사용하기)
  - [query-string 라이브러리 사용하기](#query-string-라이브러리-사용하기)
  - [JSURL2 라이브러리 사용하기](#jsurl2-라이브러리-사용하기)
  - [Zipson 라이브러리 사용하기](#zipson-라이브러리-사용하기)
    - [안전한 바이너리 인코딩/디코딩](#안전한-바이너리-인코딩디코딩)
      - [문자열을 바이너리 문자열로 인코딩하기](#문자열을-바이너리-문자열로-인코딩하기)
      - [바이너리 문자열을 문자열로 디코딩하기](#바이너리-문자열을-문자열로-디코딩하기)

# 커스텀 검색 파라미터 직렬화

기본적으로 TanStack Router는 `JSON.stringify`와 `JSON.parse`를 사용해 URL 검색 파라미터를 자동으로 파싱하고 직렬화합니다. 이 과정은 검색 객체의 직렬화와 역직렬화 외에도 URL 검색 파라미터에 대한 일반적인 관행인 검색 문자열의 이스케이프와 언이스케이프를 포함합니다.

예를 들어, 기본 설정을 사용할 때 다음과 같은 검색 객체가 있다고 가정해 보겠습니다.

```tsx
const search = {
  page: 1,
  sort: 'asc',
  filters: { author: 'tanner', min_words: 800 },
}
```

이 객체는 다음과 같은 검색 문자열로 직렬화되고 이스케이프됩니다.

```txt
?page=1&sort=asc&filters=%7B%22author%22%3A%22tanner%22%2C%22min_words%22%3A800%7D
```

기본 동작은 다음과 같은 코드로 구현할 수 있습니다.

```tsx
import {
  createRouter,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'

const router = createRouter({
  // ...
  parseSearch: parseSearchWith(JSON.parse),
  stringifySearch: stringifySearchWith(JSON.stringify),
})
```

그러나 이 기본 동작이 모든 사용 사례에 적합하지 않을 수 있습니다. 예를 들어, base64 인코딩과 같은 다른 직렬화 형식을 사용하거나, [query-string](https://github.com/sindresorhus/query-string), [JSURL2](https://github.com/wmertens/jsurl2), 또는 [Zipson](https://jgranstrom.github.io/zipson/)과 같은 특수 목적의 직렬화/역직렬화 라이브러리를 사용하고 싶을 수 있습니다.

이러한 경우, [`Router`](../api/router/RouterOptionsType.md#stringifysearch-method) 설정에서 `parseSearch`와 `stringifySearch` 옵션에 직접 직렬화 및 역직렬화 함수를 제공할 수 있습니다. 이때 TanStack Router의 내장 헬퍼 함수인 `parseSearchWith`와 `stringifySearchWith`를 사용하면 프로세스를 단순화할 수 있습니다.

> [!TIP]
> 직렬화와 역직렬화의 중요한 측면은 역직렬화 후 동일한 객체를 얻을 수 있어야 한다는 점입니다. 이는 직렬화와 역직렬화 과정이 올바르게 수행되지 않으면 일부 정보를 잃을 수 있기 때문입니다. 예를 들어, 중첩 객체를 지원하지 않는 라이브러리를 사용하는 경우, 검색 문자열을 역직렬화할 때 중첩 객체를 잃을 수 있습니다.

![URL 검색 파라미터 직렬화 및 역직렬화의 멱등성(idempotency)을 보여주는 다이어그램](https://raw.githubusercontent.com/TanStack/router/main/docs/assets/search-serialization-deserialization-idempotency.jpg)

다음은 TanStack Router에서 검색 파라미터 직렬화를 커스터마이징하는 몇 가지 예제입니다.


## Base64 사용하기

브라우저와 URL 언퍼러(URL unfurlers) 등에서 최대한 호환성을 유지하기 위해 검색 파라미터를 Base64로 인코딩하는 것이 일반적입니다. 다음 코드를 통해 이를 구현할 수 있습니다:

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'

const router = createRouter({
  parseSearch: parseSearchWith((value) => JSON.parse(decodeFromBinary(value))),
  stringifySearch: stringifySearchWith((value) =>
    encodeToBinary(JSON.stringify(value)),
  ),
})

function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}

function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

> [⚠️ 이 코드 조각이 atob/btoa를 사용하지 않는 이유는?](#safe-binary-encodingdecoding)

이 설정을 사용하여 이전 객체를 검색 문자열로 변환하면 다음과 같이 나타납니다:

```txt
?page=1&sort=asc&filters=eyJhdXRob3IiOiJ0YW5uZXIiLCJtaW5fd29yZHMiOjgwMH0%3D
```

이 코드는 검색 파라미터를 Base64로 인코딩하고 디코딩하는 방법을 보여줍니다. 이를 통해 URL에서 안전하게 데이터를 전달할 수 있습니다.


## query-string 라이브러리 사용하기

[query-string](https://github.com/sindresorhus/query-string) 라이브러리는 쿼리 문자열을 안정적으로 파싱하고 문자열로 변환할 수 있어 인기가 많습니다. 이 라이브러리를 사용하면 검색 파라미터의 직렬화 형식을 커스터마이징할 수 있습니다. 다음 코드를 통해 이를 구현할 수 있습니다:

```tsx
import { createRouter } from '@tanstack/react-router'
import qs from 'query-string'

const router = createRouter({
  // ...
  stringifySearch: stringifySearchWith((value) =>
    qs.stringify(value, {
      // ...옵션
    }),
  ),
  parseSearch: parseSearchWith((value) =>
    qs.parse(value, {
      // ...옵션
    }),
  ),
})
```

이 설정을 사용하여 이전 객체를 검색 문자열로 변환하면 다음과 같은 결과가 나옵니다:

```txt
?page=1&sort=asc&filters=author%3Dtanner%26min_words%3D800
```


## JSURL2 라이브러리 사용하기

[JSURL2](https://github.com/wmertens/jsurl2)는 URL을 압축하면서도 가독성을 유지할 수 있는 비표준 라이브러리입니다. 아래 코드를 통해 이를 구현할 수 있습니다:

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'
import { parse, stringify } from 'jsurl2'

const router = createRouter({
  // ...
  parseSearch: parseSearchWith(parse),
  stringifySearch: stringifySearchWith(stringify),
})
```

이 설정을 사용하여 이전 객체를 검색 문자열로 변환하면 다음과 같이 나타납니다:

```txt
?page=1&sort=asc&filters=(author~tanner~min*_words~800)~
```


## Zipson 라이브러리 사용하기

[Zipson](https://jgranstrom.github.io/zipson/)은 사용하기 쉽고 성능이 뛰어난 JSON 압축 라이브러리입니다. (런타임 성능과 압축 성능 모두 우수합니다.) 검색 파라미터를 압축하려면 (이스케이프/언이스케이프와 base64 인코딩/디코딩도 필요합니다) 다음과 같은 코드를 사용할 수 있습니다:

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'
import { stringify, parse } from 'zipson'

const router = createRouter({
  parseSearch: parseSearchWith((value) => parse(decodeFromBinary(value))),
  stringifySearch: stringifySearchWith((value) =>
    encodeToBinary(stringify(value)),
  ),
})

function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}

function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

> [⚠️ 이 코드 조각이 atob/btoa를 사용하지 않는 이유는?](#safe-binary-encodingdecoding)

이 설정을 사용하여 이전 객체를 검색 문자열로 변환하면 다음과 같이 나타납니다:

```txt
?page=1&sort=asc&filters=JTdCJUMyJUE4YXV0aG9yJUMyJUE4JUMyJUE4dGFubmVyJUMyJUE4JUMyJUE4bWluX3dvcmRzJUMyJUE4JUMyJUEyQ3UlN0Q%3D
```

<hr>


### 안전한 바이너리 인코딩/디코딩

브라우저에서 `atob`와 `btoa` 함수는 UTF-8이 아닌 문자를 제대로 처리하지 못할 수 있습니다. 대신 아래의 인코딩/디코딩 유틸리티를 사용하는 것을 권장합니다:

#### 문자열을 바이너리 문자열로 인코딩하기

```typescript
export function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

#### 바이너리 문자열을 문자열로 디코딩하기

```typescript
export function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}
```

이 코드는 UTF-8이 아닌 문자도 안전하게 처리할 수 있도록 설계되었습니다. `encodeToBinary` 함수는 문자열을 바이너리 문자열로 변환하고, `decodeFromBinary` 함수는 바이너리 문자열을 다시 원래의 문자열로 복원합니다.


