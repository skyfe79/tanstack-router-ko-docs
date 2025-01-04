# 라우트 매칭

라우트 매칭은 일관되고 예측 가능한 패턴을 따릅니다. 이 가이드에서는 라우트 트리가 어떻게 매칭되는지 설명합니다.

TanStack Router가 라우트 트리를 처리할 때, 모든 라우트는 가장 구체적인 라우트부터 먼저 매칭되도록 자동으로 정렬됩니다. 이는 라우트 트리가 정의된 순서와 상관없이 항상 다음과 같은 순서로 정렬됨을 의미합니다:

1. 인덱스 라우트
2. 정적 라우트 (가장 구체적인 것부터 덜 구체적인 순서)
3. 동적 라우트 (가장 긴 것부터 짧은 순서)
4. 스플랫/와일드카드 라우트

다음과 같은 가상의 라우트 트리를 예로 들어보겠습니다:

```
Root
  - blog
    - $postId
    - /
    - new
  - /
  - *
  - about
  - about/us
```

정렬 후, 이 라우트 트리는 다음과 같이 변경됩니다:

```
Root
  - /
  - about/us
  - about
  - blog
    - /
    - new
    - $postId
  - *
```

이 최종 순서는 라우트가 구체성에 따라 매칭되는 순서를 나타냅니다.

이 라우트 트리를 사용하여 몇 가지 다른 URL에 대한 매칭 과정을 살펴보겠습니다:

- `/blog`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ⏩ blog
      ✅ /
      - new
      - $postId
    - *
  ```
- `/blog/my-post`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ⏩ blog
      ❌ /
      ❌ new
      ✅ $postId
    - *
  ```
- `/`
  ```
  Root
    ✅ /
    - about/us
    - about
    - blog
      - /
      - new
      - $postId
    - *
  ```
- `/not-a-route`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ❌ blog
      - /
      - new
      - $postId
    ✅ *
  ```

이 예제를 통해 라우트 매칭이 어떻게 동작하는지 이해할 수 있습니다. 각 URL에 대해 가장 구체적인 라우트부터 순차적으로 매칭을 시도하며, 매칭이 되지 않으면 다음 라우트로 넘어갑니다. 마지막으로 와일드카드 라우트(`*`)가 모든 매칭되지 않은 URL을 처리합니다.


## 경로 없는 / 레이아웃 라우트 매칭

매칭 과정에서 [경로 없는 / 레이아웃 라우트](./routing-concepts.md#pathless--layout-routes)는 평면적으로 처리됩니다. 경로 없는 라우트의 자식들 중에서 해당 라우트를 찾지 못하면, 매칭은 경로 없는 라우트의 자식들을 벗어나 일반적인 방식으로 부모 서브트리의 나머지 부분을 계속 탐색합니다.


## `NotFoundRoute`와 매칭

라우터에 `NotFoundRoute`를 설정하려면, `routeTree`가 아닌 `notFoundRoute` 옵션에 전달해야 합니다. `NotFoundRoute`는 매칭 과정에서 고려되지 않으며, 다른 적절한 매칭이 없을 때만 대체로 렌더링되기 때문입니다.

`NotFoundRoute`에 대해 더 자세히 알고 싶다면 [Not Found Errors](./not-found-errors.md) 가이드를 참고하세요.


