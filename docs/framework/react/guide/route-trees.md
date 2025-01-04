# 라우트 트리와 중첩 구조

TanStack Router는 URL과 렌더링할 올바른 컴포넌트 트리를 매칭하기 위해 중첩된 라우트 트리를 사용합니다.

라우트 트리를 구축하는 방법으로 TanStack Router는 두 가지 방식을 지원합니다:

- 파일 기반 라우팅
- 코드 기반 라우팅

두 방법 모두 동일한 핵심 기능을 제공하지만, **파일 기반 라우팅은 동일하거나 더 나은 결과를 위해 더 적은 코드를 필요로 합니다**. 이러한 이유로 **파일 기반 라우팅이 TanStack Router를 구성하는 데 선호되고 권장되는 방식**이며, 대부분의 문서는 파일 기반 라우팅 관점에서 작성되었습니다.

코드 기반 라우팅에 대한 문서는 [코드 기반 라우팅 가이드](./code-based-routing.md)를 참고하세요.


## 라우트 트리

중첩 라우팅은 URL을 사용해 중첩된 컴포넌트 트리를 렌더링할 수 있는 강력한 개념입니다. 예를 들어, `/blog/posts/123`이라는 URL이 주어졌을 때, 다음과 같은 라우트 계층 구조를 만들 수 있습니다:

```tsx
├── blog
│   ├── posts
│   │   ├── $postId
```

그리고 다음과 같은 컴포넌트 트리를 렌더링할 수 있습니다:

```tsx

  
    
  

```

이 개념을 확장해 더 큰 사이트 구조로 만들어 보겠습니다. 이번에는 파일 이름을 사용해 표현해 보겠습니다:

```
/routes
├── __root.tsx
├── index.tsx
├── about.tsx
├── posts/
│   ├── index.tsx
│   ├── $postId.tsx
├── posts.$postId.edit.tsx
├── settings/
│   ├── profile.tsx
│   ├── notifications.tsx
├── _layout/
│   ├── layout-a.tsx
├── ├── layout-b.tsx
├── files/
│   ├── $.tsx
```

위 구조는 TanStack Router에서 사용할 수 있는 유효한 라우트 트리 설정입니다. 파일 기반 라우팅에는 많은 기능과 규칙이 담겨 있으므로, 이를 조금씩 살펴보겠습니다.


## 라우트 트리 구성

라우트 트리는 여러 가지 방식으로 구성할 수 있습니다:

- [플랫 라우트](./file-based-routing.md#flat-routes)
- [디렉토리](./file-based-routing.md#directory-routes)
- [플랫 라우트와 디렉토리 혼합](./file-based-routing.md#mixed-flat-and-directory-routes)
- [가상 파일 라우트](./virtual-file-routes.md)
- [코드 기반 라우트](./code-based-routing.md)

각 라우트 트리 타입에 대한 자세한 내용은 위의 문서 링크를 참고하거나, 다음 섹션으로 넘어가 파일 기반 라우팅을 시작해 보세요.


