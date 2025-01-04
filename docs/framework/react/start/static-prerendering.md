# Table of Contents

- [정적 사전 렌더링(Static Prerendering)](#정적-사전-렌더링static-prerendering)
  - [Nitro 기반의 사전 렌더링(Prerendering)](#nitro-기반의-사전-렌더링prerendering)

# 정적 사전 렌더링(Static Prerendering)

> 정적 사전 렌더링은 Nitro의 기능이며, TanStack Start에서도 사용할 수 있지만, 아직 최적의 사용 방법을 탐구 중입니다. 주의해서 사용하세요!

정적 사전 렌더링은 애플리케이션을 위한 정적 HTML 파일을 생성하는 과정입니다. 이 기능은 애플리케이션의 성능을 향상시키는 데 유용할 수 있습니다. 사용자에게 실시간으로 HTML을 생성하지 않고도 미리 렌더링된 HTML 파일을 제공할 수 있기 때문입니다. 또한, 서버 사이드 렌더링을 지원하지 않는 플랫폼에 정적 사이트를 배포할 때도 유용합니다.


## Nitro 기반의 사전 렌더링(Prerendering)

TanStack Start는 Nitro 위에 구축되어 있어, Nitro의 사전 렌더링 기능을 활용할 수 있습니다. Nitro는 애플리케이션을 정적 HTML 파일로 미리 렌더링할 수 있으며, 이 파일들은 실시간으로 생성하지 않고도 사용자에게 제공될 수 있습니다. 애플리케이션을 사전 렌더링하려면 `app.config.js` 파일에 `server.prerender` 옵션을 추가하면 됩니다.

```js
// app.config.js

import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    prerender: {
      routes: ['/'], // 사전 렌더링할 라우트 지정
      crawlLinks: true, // 링크 크롤링 활성화
    },
  },
})
```

사전 렌더링과 관련된 다양한 옵션은 [Nitro 설정 문서의 사전 렌더링 섹션](https://nitro.unjs.io/config#prerender)에서 확인할 수 있습니다.


