# 검색 파라미터(Search Params)

TanStack Query가 React 애플리케이션에서 서버 상태를 쉽게 다루게 해준 것처럼, TanStack Router는 여러분의 애플리케이션에서 URL 검색 파라미터의 힘을 발휘할 수 있도록 돕습니다.


## 왜 `URLSearchParams`를 사용하지 않을까요?

최근 "플랫폼을 활용하라"는 말을 자주 듣고 있을 겁니다. 대체로 동의하지만, 더 복잡한 사용 사례에서 플랫폼이 부족한 부분을 인식하는 것도 중요하다고 생각합니다. `URLSearchParams`가 바로 그런 경우 중 하나입니다.

기존의 검색 파라미터 API는 보통 몇 가지를 가정합니다:

- 검색 파라미터는 항상 문자열입니다.
- 대부분 단순한 구조를 가집니다.
- `URLSearchParams`를 사용한 직렬화와 역직렬화가 충분합니다. (스포일러: 충분하지 않습니다.)
- 검색 파라미터 수정은 URL의 경로와 밀접하게 연결되어 있으며, 경로가 변경되지 않더라도 함께 업데이트되어야 합니다.

하지만 현실은 이 가정과 매우 다릅니다.

- 검색 파라미터는 애플리케이션 상태를 나타내므로, 다른 상태 관리 도구와 동일한 개발자 경험(DX)을 기대하게 됩니다. 이는 기본 값 타입을 구분하고, 중첩된 배열이나 객체 같은 복잡한 데이터 구조를 효율적으로 저장하고 조작할 수 있는 기능을 의미합니다.
- 상태를 직렬화하고 역직렬화하는 방법은 여러 가지가 있으며, 각각 장단점이 있습니다. 애플리케이션에 가장 적합한 방법을 선택하거나, 최소한 `URLSearchParams`보다 나은 기본값을 제공받아야 합니다.
- **불변성과 구조적 공유**: URL 검색 파라미터를 문자열로 변환하고 다시 파싱할 때마다, 참조 무결성과 객체 식별성이 사라집니다. 매번 새로운 데이터 구조가 생성되기 때문입니다. 이 과정이 반복되면, 특히 불변성을 통해 반응성을 추적하는 React나 역직렬화된 데이터 소스의 변경을 감지하기 위해 조정(reconciliation)을 사용하는 Solid 같은 프레임워크에서 예상치 못한 성능 문제가 발생할 수 있습니다.
- 검색 파라미터는 URL의 중요한 부분이지만, URL의 경로와 독립적으로 자주 변경됩니다. 예를 들어, 사용자는 URL 경로를 건드리지 않고 페이지네이션된 목록의 페이지 번호만 변경하고 싶을 수 있습니다.


## 검색 파라미터(Search Params): "원조" 상태 관리자

여러분은 아마도 URL에서 `?page=3`이나 `?filter-name=tanner`와 같은 검색 파라미터를 본 적이 있을 겁니다. 이는 URL 안에 존재하는 **일종의 전역 상태**임에 틀림없습니다. 특정 상태를 URL에 저장하는 것은 다음과 같은 이유로 가치가 있습니다:

- 사용자는 다음과 같은 작업을 할 수 있어야 합니다:
  - Cmd/Ctrl + 클릭으로 링크를 새 탭에서 열고, 예상한 상태를 정확히 볼 수 있어야 합니다.
  - 애플리케이션에서 링크를 북마크하거나 공유할 때, 링크를 복사한 당시의 상태를 그대로 볼 수 있어야 합니다.
  - 앱을 새로 고침하거나 페이지를 앞뒤로 이동해도 상태를 잃지 않아야 합니다.
- 개발자는 다음과 같은 작업을 쉽게 할 수 있어야 합니다:
  - 다른 상태 관리자와 동일한 수준의 개발자 경험(DX)으로 URL의 상태를 추가, 제거 또는 수정할 수 있어야 합니다.
  - 애플리케이션이 안전하게 사용할 수 있는 형식과 타입으로 URL에서 오는 검색 파라미터를 쉽게 검증할 수 있어야 합니다.
  - 내부 직렬화 형식을 걱정하지 않고 검색 파라미터를 읽고 쓸 수 있어야 합니다.


## JSON 우선 검색 파라미터

위 기능을 구현하기 위해, TanStack Router는 URL의 검색 문자열을 구조화된 JSON으로 자동 변환하는 강력한 검색 파라미터 파서를 내장하고 있습니다. 이는 여러분이 검색 파라미터에 JSON으로 직렬화 가능한 데이터 구조를 저장할 수 있으며, 이를 JSON으로 파싱하고 직렬화할 수 있다는 것을 의미합니다. 이는 배열 형태의 구조나 중첩된 데이터를 제한적으로 지원하는 `URLSearchParams`에 비해 큰 개선점입니다.

예를 들어, 다음 라우트로 이동하는 경우를 살펴보겠습니다:

```tsx
const link = (
  <Link
    to="/shop"
    search={{
      pageIndex: 3,
      includeCategories: ['electronics', 'gifts'],
      sortBy: 'price',
      desc: true,
    }}
  />
)
```

이 코드는 다음과 같은 URL을 생성합니다:

```
/shop?pageIndex=3&includeCategories=%5B%22electronics%22%2C%22gifts%22%5D&sortBy=price&desc=true
```

이 URL이 파싱되면, 검색 파라미터는 정확히 다음과 같은 JSON으로 변환됩니다:

```json
{
  "pageIndex": 3,
  "includeCategories": ["electronics", "gifts"],
  "sortBy": "price",
  "desc": true
}
```

여기서 몇 가지 중요한 점을 확인할 수 있습니다:

- 검색 파라미터의 첫 번째 레벨은 `URLSearchParams`와 마찬가지로 평평하고 문자열 기반입니다.
- 문자열이 아닌 첫 번째 레벨 값들은 실제 숫자와 불리언 값으로 정확히 유지됩니다.
- 중첩된 데이터 구조는 자동으로 URL에 안전한 JSON 문자열로 변환됩니다.

> 🧠 다른 도구들은 검색 파라미터가 항상 평평하고 문자열 기반이라고 가정하는 경우가 많습니다. 그래서 우리는 첫 번째 레벨에서 URLSearchParam과 호환되도록 유지하기로 결정했습니다. 이는 TanStack Router가 중첩된 검색 파라미터를 JSON으로 관리하더라도, 다른 도구들이 URL에 쓰고 첫 번째 레벨 파라미터를 정상적으로 읽을 수 있음을 의미합니다.


## 검색 파라미터 검증 및 타입 지정

TanStack Router가 검색 파라미터를 신뢰할 수 있는 JSON으로 파싱할 수 있지만, 이 파라미터들은 결국 **사용자가 입력한 원시 텍스트**에서 비롯됩니다. 다른 직렬화 경계와 마찬가지로, 검색 파라미터를 사용하기 전에 애플리케이션이 신뢰하고 의존할 수 있는 형식으로 검증해야 합니다.


### 검증 + 타입스크립트!

TanStack Router는 검색 파라미터를 검증하고 타입을 지정할 수 있는 편리한 API를 제공합니다. 이 모든 것은 `Route`의 `validateSearch` 옵션에서 시작됩니다:

```tsx
// /routes/shop.products.tsx

type ProductSearchSortOptions = 'newest' | 'oldest' | 'price'

type ProductSearch = {
  page: number
  filter: string
  sort: ProductSearchSortOptions
}

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search: Record): ProductSearch => {
    // 검색 파라미터를 검증하고 타입이 지정된 상태로 파싱
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

위 예제에서는 `allProductsRoute`의 검색 파라미터를 검증하고, 타입이 지정된 `ProductSearch` 객체를 반환합니다. 이렇게 타입이 지정된 객체는 이 라우트의 다른 옵션과 **자식 라우트에서도 사용할 수 있습니다!**


### 검색 파라미터 유효성 검사

`validateSearch` 옵션은 JSON으로 파싱된(아직 유효성 검사는 되지 않은) 검색 파라미터를 `Record<string, unknown>` 형태로 받아서, 여러분이 원하는 타입의 객체를 반환하는 함수입니다. 일반적으로 잘못된 형식이나 예상치 못한 검색 파라미터에 대해 적절한 기본값을 제공하는 것이 좋습니다. 이렇게 하면 사용자 경험이 중단되지 않습니다.

다음은 예제입니다:

```tsx
// /routes/shop.products.tsx

type ProductSearchSortOptions = 'newest' | 'oldest' | 'price'

type ProductSearch = {
  page: number
  filter: string
  sort: ProductSearchSortOptions
}

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search: Record): ProductSearch => {
    // 검색 파라미터를 검증하고 타입이 지정된 상태로 파싱
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

다음은 [Zod](https://zod.dev/) 라이브러리를 사용하여 검색 파라미터를 한 번에 검증하고 타입을 지정하는 예제입니다(다른 검증 라이브러리를 사용해도 괜찮습니다):

```tsx
// /routes/shop.products.tsx

import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().catch(1),
  filter: z.string().catch(''),
  sort: z.enum(['newest', 'oldest', 'price']).catch('newest'),
})

type ProductSearch = z.infer

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search) => productSearchSchema.parse(search),
})
```

`validateSearch`는 `parse` 속성을 가진 객체도 받을 수 있기 때문에, 다음과 같이 줄일 수 있습니다:

```tsx
validateSearch: productSearchSchema
```

위 예제에서 Zod의 `.catch()` 수정자를 사용한 이유는 사용자에게 오류를 보여주지 않기 위해서입니다. 검색 파라미터가 잘못된 경우, 사용자의 경험을 중단시키고 큰 오류 메시지를 보여주는 것보다는 기본값을 제공하는 것이 더 나은 선택일 수 있습니다. 하지만 때로는 **오류 메시지를 보여주고 싶을 수도 있습니다**. 그런 경우에는 `.catch()` 대신 `.default()`를 사용할 수 있습니다.

이것이 동작하는 내부 메커니즘은 `validateSearch` 함수가 오류를 던지는 것에 의존합니다. 오류가 발생하면 라우트의 `onError` 옵션이 트리거되고, `error.routerCode`가 `VALIDATE_SEARCH`로 설정됩니다. 그리고 `errorComponent`가 렌더링되어, 검색 파라미터 오류를 원하는 방식으로 처리할 수 있습니다.


#### 어댑터

[Zod](https://zod.dev/)와 같은 라이브러리를 사용해 검색 파라미터를 검증할 때, URL에 검색 파라미터를 적용하기 전에 `transform`을 통해 파라미터를 변환하고 싶을 수 있습니다. Zod에서 자주 사용하는 `transform` 중 하나는 `default`입니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().default(1),
  filter: z.string().default(''),
  sort: z.enum(['newest', 'oldest', 'price']).default('newest'),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```

이 라우트로 네비게이션을 시도할 때 `search`가 필수로 요구된다는 점이 의아할 수 있습니다. 아래 `Link`는 `search`가 누락되어 타입 에러가 발생합니다.

```tsx
// 예시 코드 생략
```

검증 라이브러리를 사용할 때는 올바른 `input`과 `output` 타입을 추론하는 어댑터를 사용하는 것을 권장합니다.


### Zod

[Zod](https://zod.dev/)를 위한 어댑터가 제공됩니다. 이 어댑터는 올바른 `input` 타입과 `output` 타입을 전달합니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().default(1),
  filter: z.string().default(''),
  sort: z.enum(['newest', 'oldest', 'price']).default('newest'),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator(productSearchSchema),
})
```

여기서 중요한 부분은 `Link`를 사용할 때 더 이상 `search` 파라미터를 명시적으로 전달할 필요가 없다는 점입니다.

```tsx
// 예제 코드 생략
```

그러나 여기서 `catch`를 사용하면 타입이 오버라이드되어 `page`, `filter`, `sort`가 `unknown` 타입으로 변환되며, 이로 인해 타입 정보가 손실됩니다. 이 문제를 해결하기 위해 `fallback` 제네릭 함수를 제공했습니다. 이 함수는 타입을 유지하면서 검증이 실패할 경우 `fallback` 값을 제공합니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { fallback, zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: fallback(z.number(), 1).default(1),
  filter: fallback(z.string(), '').default(''),
  sort: fallback(z.enum(['newest', 'oldest', 'price']), 'newest').default(
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator(productSearchSchema),
})
```

따라서 이 라우트로 네비게이션할 때 `search`는 선택 사항이며, 올바른 타입을 유지합니다.

추천되지는 않지만, `output` 타입이 `input` 타입보다 더 정확한 경우 `input`과 `output` 타입을 별도로 설정할 수도 있습니다.

```tsx
const productSearchSchema = z.object({
  page: fallback(z.number(), 1).default(1),
  filter: fallback(z.string(), '').default(''),
  sort: fallback(z.enum(['newest', 'oldest', 'price']), 'newest').default(
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator({
    schema: productSearchSchema,
    input: 'output',
    output: 'input',
  }),
})
```

이를 통해 네비게이션 시 추론할 타입과 검색 파라미터를 읽을 때 추론할 타입을 유연하게 설정할 수 있습니다.


### Valibot

> [!경고]
> 라우터는 Valibot 1.0 패키지가 설치되어 있어야 합니다.

[Valibot](https://valibot.dev/)을 사용할 때, 네비게이션과 검색 파라미터를 읽기 위해 올바른 `input`과 `output` 타입을 보장하기 위해 어댑터가 필요하지 않습니다. 이는 `valibot`이 [Standard Schema](https://github.com/standard-schema/standard-schema)를 구현하기 때문입니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'
import * as v from 'valibot'

const productSearchSchema = v.object({
  page: v.optional(v.fallback(v.number(), 1), 1),
  filter: v.optional(v.fallback(v.string(), ''), ''),
  sort: v.optional(
    v.fallback(v.picklist(['newest', 'oldest', 'price']), 'newest'),
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```


### Arktype

> [!경고]
> 라우터를 사용하려면 arktype 2.0-rc 패키지가 설치되어 있어야 합니다.

[ArkType](https://arktype.io/)을 사용할 때는 네비게이션과 검색 파라미터를 읽기 위해 `input`과 `output` 타입을 올바르게 사용하기 위해 어댑터가 필요하지 않습니다. 이는 [ArkType](https://arktype.io/)이 [Standard Schema](https://github.com/standard-schema/standard-schema)를 구현하기 때문입니다.

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { type } from 'arktype'

const productSearchSchema = type({
  page: 'number = 1',
  filter: 'string = ""',
  sort: '"newest" | "oldest" | "price" = "newest"',
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```


## 검색 파라미터 읽기

검색 파라미터가 유효성 검사를 통과하고 타입이 지정되면, 이제 검색 파라미터를 읽고 쓸 준비가 된 것입니다. TanStack Router에서는 이를 수행하는 몇 가지 방법이 있습니다. 하나씩 살펴보겠습니다.


### 로더에서 검색 파라미터 사용하기

`loaderDeps` 옵션을 사용해 로더에서 검색 파라미터를 읽는 방법에 대한 자세한 내용은 [로더에서 검색 파라미터 사용하기](./data-loading.md#using-loaderdeps-to-access-search-params) 섹션을 참고하세요.


### 부모 라우트에서 검색 파라미터 상속하기

검색 파라미터와 타입은 라우트 트리를 따라 내려가면서 병합됩니다. 따라서 자식 라우트도 부모의 검색 파라미터에 접근할 수 있습니다.

- `shop.products.tsx`

```tsx
const productSearchSchema = z.object({
  page: z.number().catch(1),
  filter: z.string().catch(''),
  sort: z.enum(['newest', 'oldest', 'price']).catch('newest'),
})

type ProductSearch = z.infer

export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})
```

- `shop.products.$productId.tsx`

```tsx
export const Route = createFileRoute('/shop/products/$productId')({
  beforeLoad: ({ search }) => {
    search
    // ^? ProductSearch ✅
  },
})
```

위 예제에서 `shop.products.$productId.tsx` 라우트는 부모 라우트인 `shop.products.tsx`에서 정의된 `ProductSearch` 타입의 검색 파라미터를 상속받습니다. 이를 통해 자식 라우트에서도 부모의 검색 파라미터를 사용할 수 있습니다.


### 컴포넌트에서 검색 파라미터 사용하기

라우트의 검증된 검색 파라미터는 `useSearch` 훅을 통해 컴포넌트에서 접근할 수 있습니다.

```tsx
// /routes/shop.products.tsx

export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  const { page, filter, sort } = Route.useSearch()

  return ...
}
```

> [!TIP]
> 컴포넌트가 코드 분할(code-split)된 경우, [getRouteApi 함수](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)를 사용하면 `Route` 설정을 임포트하지 않고도 타입이 지정된 `useSearch()` 훅에 접근할 수 있습니다.


### 라우트 컴포넌트 외부에서 검색 파라미터 사용하기

`useSearch` 훅을 사용하면 앱 어디에서나 라우트의 검증된 검색 파라미터에 접근할 수 있습니다. 출발 라우트의 `from` id/path를 전달하면 더 나은 타입 안전성을 얻을 수 있습니다:

```tsx
const allProductsRoute = createRoute({
  getParentRoute: () => shopRoute,
  path: 'products',
  validateSearch: productSearchSchema,
})

// 다른 곳에서...

const routeApi = getRouteApi('/shop/products')

const ProductList = () => {
  const routeSearch = routeApi.useSearch()

  // 또는

  const { page, filter, sort } = useSearch({
    from: allProductsRoute.fullPath,
  })

  return ...
}
```

타입 안전성을 완화하고 선택적 `search` 객체를 얻으려면 `strict: false`를 전달할 수 있습니다:

```tsx
function ProductList() {
  const search = useSearch({
    strict: false,
  })
  // {
  //   page: number | undefined
  //   filter: string | undefined
  //   sort: 'newest' | 'oldest' | 'price' | undefined
  // }

  return ...
}
```


## 검색 파라미터 작성하기

이제 여러분은 라우트의 검색 파라미터를 읽는 방법을 배웠습니다. 이제는 검색 파라미터를 수정하고 업데이트하는 주요 API를 이미 보셨다는 사실을 알게 되어 기쁠 것입니다. 잠시 복습해 보겠습니다.


### `<Link search />`

검색 파라미터를 업데이트하는 가장 좋은 방법은 `<Link />` 컴포넌트의 `search` 프로퍼티를 사용하는 것입니다.

현재 페이지의 검색 파라미터를 업데이트하고 `from` 프로퍼티가 지정된 경우, `to` 프로퍼티는 생략할 수 있습니다.  
다음은 예제입니다:

```tsx
// /routes/shop.products.tsx
export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  return (
    <Link
      from={allProductsRoute.fullPath}
      search={(prev) => ({ page: prev.page + 1 })}
    >
      Next Page
    </Link>
  )
}
```

여러 라우트에서 렌더링되는 일반적인 컴포넌트에서 검색 파라미터를 업데이트하려는 경우, `from`을 지정하는 것이 어려울 수 있습니다.

이런 경우 `to="."`를 설정하면 느슨하게 타입이 지정된 검색 파라미터에 접근할 수 있습니다.  
다음은 이를 보여주는 예제입니다:

```tsx
// `page`는 루트 라우트에서 정의된 검색 파라미터이므로 모든 라우트에서 사용 가능합니다.
const PageSelector = () => {
  return (
    <Link
      to="."
      search={(prev) => ({ ...prev, page: prev.page + 1 })}
    >
      Next Page
    </Link>
  )
}
```

일반적인 컴포넌트가 라우트 트리의 특정 하위 트리에서만 렌더링되는 경우, `from`을 사용하여 해당 하위 트리를 지정할 수 있습니다. 이 경우 `to='.'`를 생략할 수 있습니다.

```tsx
// `page`는 /posts 라우트에서 정의된 검색 파라미터이므로 해당 라우트의 모든 자식 라우트에서 사용 가능합니다.
const PageSelector = () => {
  return (
    <Link
      from="/posts"
      to="."
      search={(prev) => ({ ...prev, page: prev.page + 1 })}
    >
      Next Page
    </Link>
  )
}
```


### `useNavigate(), navigate({ search })`

`navigate` 함수는 `<Link />`의 `search` 속성과 동일하게 작동하는 `search` 옵션을 받을 수 있습니다.

```tsx
// /routes/shop.products.tsx
export const Route = createFileRoute('/shop/products/$productId')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  const navigate = useNavigate({ from: Route.fullPath })

  return (
    <button
      onClick={() => {
        navigate({
          search: (prev) => ({ page: prev.page + 1 }),
        })
      }}
    >
      Next Page
    </button>
  )
}
```

위 예제에서 `navigate` 함수는 현재 페이지의 `search` 값을 기반으로 다음 페이지로 이동합니다. `search` 옵션은 이전 상태(`prev`)를 받아 새로운 `search` 값을 반환하는 함수를 사용할 수 있습니다. 이 경우, `prev.page` 값을 1 증가시켜 다음 페이지로 이동합니다.


### `router.navigate({ search })`

`router.navigate` 함수는 위에서 설명한 `useNavigate`/`navigate` 훅/함수와 동일하게 작동합니다.


### `<Navigate search />`

`<Navigate search />` 컴포넌트는 위에서 설명한 `useNavigate`/`navigate` 훅/함수와 동일하게 동작합니다. 다만, 함수 인자 대신에 옵션을 props로 받는다는 점이 다릅니다.


## 검색 미들웨어로 검색 변환하기

링크의 `href`가 생성될 때, 기본적으로 `<Link>`의 `search` 속성만 쿼리 문자열 부분에 영향을 미칩니다.

TanStack Router는 **검색 미들웨어**를 통해 `href`가 생성되기 전에 검색 파라미터를 조작할 수 있는 방법을 제공합니다. 검색 미들웨어는 라우트나 그 하위 라우트에 대한 새로운 링크를 생성할 때 검색 파라미터를 변환하는 함수입니다. 또한, 검색 유효성 검사 후 네비게이션 시에도 실행되어 쿼리 문자열을 조작할 수 있게 합니다.

다음 예제는 **모든** 링크가 생성될 때, 현재 검색 파라미터에 `rootValue`가 포함되어 있다면 해당 파라미터를 추가하는 방법을 보여줍니다. 만약 링크가 `search` 내부에 `rootValue`를 명시적으로 지정했다면, 그 값을 사용하여 링크를 생성합니다.

```tsx
import { z } from 'zod'
import { createFileRoute } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  rootValue: z.string().optional(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    middlewares: [
      ({ search, next }) => {
        const result = next(search)
        return {
          rootValue: search.rootValue,
          ...result,
        }
      },
    ],
  },
})
```

이와 같은 특정 사용 사례는 매우 일반적이기 때문에, TanStack Router는 `retainSearchParams`를 통해 검색 파라미터를 유지하는 일반적인 구현을 제공합니다.

```tsx
import { z } from 'zod'
import { createFileRoute, retainSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  rootValue: z.string().optional(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    middlewares: [retainSearchParams(['rootValue'])],
  },
})
```

또 다른 일반적인 사용 사례는 검색 파라미터의 기본값이 설정된 경우, 링크에서 해당 파라미터를 제거하는 것입니다. TanStack Router는 `stripSearchParams`를 통해 이 사용 사례를 위한 일반적인 구현을 제공합니다.

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

여러 미들웨어를 체인으로 연결할 수 있습니다. 다음 예제는 `retainSearchParams`와 `stripSearchParams`를 결합하는 방법을 보여줍니다.

```tsx
import {
  Link,
  createFileRoute,
  retainSearchParams,
  stripSearchParams,
} from '@tanstack/react-router'
import { z } from 'zod'
import { zodValidator } from '@tanstack/zod-adapter'

const defaultValues = ['foo', 'bar']

export const Route = createFileRoute('/search')({
  validateSearch: zodValidator(
    z.object({
      retainMe: z.string().optional(),
      arrayWithDefaults: z.string().array().default(defaultValues),
      required: z.string(),
    }),
  ),
  search: {
    middlewares: [
      retainSearchParams(['retainMe']),
      stripSearchParams({ arrayWithDefaults: defaultValues }),
    ],
  },
})
```


