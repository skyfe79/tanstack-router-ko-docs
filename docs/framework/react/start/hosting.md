# Table of Contents

- [호스팅](#호스팅)
  - [어떤 것을 사용해야 할까요?](#어떤-것을-사용해야-할까요)
  - [Netlify란 무엇인가요?](#netlify란-무엇인가요)
  - [배포](#배포)
    - [Netlify 설정](#netlify-설정)
    - [Vercel](#vercel)
    - [Cloudflare Pages 배포하기](#cloudflare-pages-배포하기)
- [wrangler.toml](#wranglertoml)
    - [Node.js](#nodejs)
    - [Bun](#bun)

# 호스팅

호스팅은 여러분의 애플리케이션을 인터넷에 배포하여 사용자가 접근할 수 있게 하는 과정입니다. 이는 웹 개발 프로젝트에서 매우 중요한 부분으로, 애플리케이션이 전 세계에서 사용 가능하도록 보장합니다. TanStack Start는 [Nitro](https://nitro.unjs.io/)를 기반으로 구축되었습니다. Nitro는 웹 애플리케이션을 어디서나 배포할 수 있는 강력한 서버 도구입니다. 이를 통해 TanStack Start는 어떤 호스팅 프로바이더에서도 SSR(서버 사이드 렌더링), 스트리밍, 하이드레이션을 위한 통합 API를 제공할 수 있습니다.


## 어떤 것을 사용해야 할까요?

TanStack Start는 **어떤 호스팅 프로바이더와도 호환되도록 설계**되었습니다. 이미 호스팅 프로바이더를 정했다면, TanStack Start가 제공하는 풀스택 API를 사용해 애플리케이션을 배포할 수 있습니다.

하지만 호스팅은 애플리케이션의 성능, 안정성, 확장성에 있어 가장 중요한 요소 중 하나입니다. 따라서 공식 호스팅 파트너인 [Netlify](https://www.netlify.com?utm_source=tanstack)를 사용하는 것을 적극 권장합니다.


## Netlify란 무엇인가요?

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify 로고">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify 로고" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify는 웹 애플리케이션을 배포하기 위한 빠르고 안전하며 신뢰할 수 있는 환경을 제공하는 선도적인 호스팅 플랫폼입니다. Netlify를 사용하면 TanStack Start 애플리케이션을 단 몇 번의 클릭만으로 배포할 수 있으며, 글로벌 에지 네트워크, 자동 스케일링, GitHub 및 GitLab과의 원활한 통합과 같은 기능을 활용할 수 있습니다. Netlify는 로컬 개발부터 프로덕션 배포까지 개발 프로세스를 최대한 매끄럽게 만들어 줍니다.

- Netlify에 대해 더 알아보려면 [Netlify 웹사이트](https://www.netlify.com?utm_source=tanstack)를 방문하세요.
- 가입하려면 [Netlify 대시보드](https://www.netlify.com/signup?utm_source=tanstack)를 확인하세요.


## 배포

> [!WARNING]
> 이 페이지는 아직 작업 중입니다. 곧 다양한 호스팅 제공업체에 대한 배포 가이드를 업데이트할 예정입니다!

TanStack Start 애플리케이션을 배포할 때, `app.config.ts` 파일의 `server.preset` 값이 배포 대상을 결정합니다. 배포 대상은 다음 값 중 하나로 설정할 수 있습니다:

- [`netlify`](#netlify): Netlify에 배포
- [`vercel`](#vercel): Vercel에 배포
- [`cloudflare-pages`](#cloudflare-pages): Cloudflare Pages에 배포
- [`node-server`](#nodejs): Node.js 서버에 배포
- [`bun`](#bun): Bun 서버에 배포
- ... 그리고 더 많은 옵션이 추가될 예정입니다!

배포 대상을 선택한 후, 아래의 배포 가이드를 따라 원하는 호스팅 제공업체에 TanStack Start 애플리케이션을 배포할 수 있습니다.


### Netlify 설정

`app.config.ts` 파일에서 `server.preset` 값을 `netlify`로 설정하세요.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

또는 애플리케이션을 빌드할 때 `build` 커맨드와 함께 `--preset` 플래그를 사용하여 배포 대상을 지정할 수도 있습니다.

```sh
npm run build --preset netlify
```

Netlify의 원클릭 배포 프로세스를 사용하여 애플리케이션을 배포하면 준비가 완료됩니다!


### Vercel

TanStack Start 애플리케이션을 Vercel에 배포하는 것은 쉽고 간단합니다. `app.config.ts` 파일에서 `server.preset` 값을 `vercel`로 설정하면 Vercel에 애플리케이션을 배포할 준비가 됩니다.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

또는 애플리케이션을 빌드할 때 `build` 커맨드와 함께 `--preset` 플래그를 사용하여 배포 대상을 지정할 수도 있습니다:

```sh
npm run build --preset vercel
```

Vercel의 원클릭 배포 프로세스를 사용하여 애플리케이션을 배포하면 모든 준비가 완료됩니다!


### Cloudflare Pages 배포하기

Cloudflare Pages에 배포할 때, 사용자가 앱을 사용할 수 있게 하려면 몇 가지 추가 단계를 완료해야 합니다.

1. 설치

먼저 `unenv`를 설치해야 합니다.

```sh
npm install unenv
```

2. `app.config.ts` 업데이트

`app.config.ts` 파일에서 `server.preset` 값을 `cloudflare-pages`로 설정하고, `server.unenv` 값을 `unenv`의 `cloudflare`로 설정합니다.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'
import { cloudflare } from 'unenv'

export default defineConfig({
  server: {
    preset: 'cloudflare-pages',
    unenv: cloudflare,
  },
})
```

3. `wrangler.toml` 설정 파일 추가

```toml
# wrangler.toml
name = "your-cloudflare-project-name"
pages_build_output_dir = "./dist"
compatibility_flags = ["nodejs_compat"]
compatibility_date = "2024-11-13"
```

이제 Cloudflare Pages의 원클릭 배포 프로세스를 사용해 애플리케이션을 배포하면 준비가 완료됩니다!


### Node.js

`app.config.ts` 파일에서 `server.preset` 값을 `node-server`로 설정하세요.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 또는 애플리케이션을 빌드할 때 --preset 플래그를 사용하여
// 배포 대상을 지정할 수도 있습니다:
// npm run build --preset node-server
```

그런 다음, 다음 명령어를 실행하여 애플리케이션을 빌드하고 시작할 수 있습니다:

```sh
npm run build
```

이제 여러분의 애플리케이션을 Node.js 서버에 배포할 준비가 되었습니다. 다음 명령어로 애플리케이션을 시작할 수 있습니다:

```sh
node .output/server/index.mjs
```


### Bun

> [!중요]
> 현재 Bun 전용 배포 가이드라인은 React 19에서만 작동합니다. React 18을 사용 중이라면 [Node.js](#nodejs) 배포 가이드라인을 참고하세요.

`package.json` 파일에서 `react`와 `react-dom` 패키지가 19.0.0 이상 버전으로 설정되어 있는지 확인하세요. 그렇지 않다면, 다음 명령어를 실행하여 패키지를 업그레이드하세요:

```sh
npm install react@rc react-dom@rc
```

`app.config.ts` 파일에서 `server.preset` 값을 `bun`으로 설정하세요.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 또는 애플리케이션을 빌드할 때 --preset 플래그를 사용하여
// 배포 대상을 지정할 수도 있습니다:
// npm run build --preset bun
```

그런 다음 다음 명령어를 실행하여 애플리케이션을 빌드하고 시작할 수 있습니다:

```sh
bun run build
```

이제 Bun 서버에 애플리케이션을 배포할 준비가 되었습니다. 다음 명령어를 실행하여 애플리케이션을 시작하세요:

```sh
bun run .output/server/index.mjs
```


