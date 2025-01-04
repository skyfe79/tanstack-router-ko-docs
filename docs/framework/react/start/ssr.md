# Table of Contents

- [SSR (서버 사이드 렌더링)](#ssr-서버-사이드-렌더링)
  - [SSR 기본 개념](#ssr-기본-개념)

# SSR (서버 사이드 렌더링)

서버 사이드 렌더링(SSR)은 서버에서 애플리케이션을 렌더링하고, 렌더링된 HTML을 클라이언트로 전송하거나 스트리밍하는 과정입니다. 이 방식은 애플리케이션의 성능을 향상시키고 SEO(검색 엔진 최적화)를 개선하는 데 유용합니다. 사용자는 애플리케이션의 콘텐츠를 더 빠르게 볼 수 있고, 검색 엔진은 애플리케이션을 더 쉽게 크롤링할 수 있습니다.


## SSR 기본 개념

TanStack Start는 서버 사이드 렌더링(SSR)을 기본적으로 지원합니다. 서버 사이드 렌더링을 활성화하려면 프로젝트에 `app/ssr.tsx` 파일을 생성하세요:

```tsx
// app/ssr.tsx

import {
  createStartHandler,
  defaultStreamHandler,
} from '@tanstack/start/server'
import { getRouterManifest } from '@tanstack/start/router-manifest'

import { createRouter } from './router'

export default createStartHandler({
  createRouter,
  getRouterManifest,
})(defaultStreamHandler)
```

이 파일은 서버 사이드 렌더링 핸들러를 생성하는 함수를 내보냅니다. 핸들러는 `@tanstack/start/server`에서 제공하는 `createStartHandler` 함수를 사용하여 생성됩니다. 이 함수는 다음과 같은 속성을 가진 객체를 인자로 받습니다:

- `createRouter`: 애플리케이션의 라우터를 생성하는 함수입니다. 이 함수는 호출될 때마다 새로운 라우터 인스턴스를 반환해야 합니다.
- `getRouterManifest`: 애플리케이션의 모든 라우트를 포함한 매니페스트를 반환하는 함수입니다.

이 핸들러는 `@tanstack/start/server`에서 제공하는 `defaultStreamHandler` 함수와 함께 호출됩니다. 이 함수는 클라이언트로 응답을 스트리밍하는 역할을 합니다.


