# Table of Contents

- [경로 별칭(Path Aliases)](#경로-별칭path-aliases)

# 경로 별칭(Path Aliases)

경로 별칭은 TypeScript의 유용한 기능 중 하나로, 프로젝트 디렉토리 구조에서 멀리 떨어진 경로에 대한 단축키를 정의할 수 있게 해줍니다. 이를 통해 긴 상대 경로 임포트를 피할 수 있고, 프로젝트 구조를 리팩토링할 때도 더 쉽게 작업할 수 있습니다. 특히 코드에서 긴 상대 경로 임포트를 피하는 데 매우 유용합니다.

기본적으로 TanStack Start는 경로 별칭을 포함하고 있지 않습니다. 하지만 프로젝트 루트에 있는 `tsconfig.json` 파일을 업데이트하고 다음 설정을 추가하면 쉽게 경로 별칭을 추가할 수 있습니다.

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "~/*": ["app/*"]
    }
  }
}
```

이 예제에서는 `~/*` 경로 별칭을 정의하여 `app/*` 디렉토리로 매핑했습니다. 이제 `app` 디렉토리에 있는 파일을 `~` 접두사를 사용하여 임포트할 수 있습니다.

`tsconfig.json` 파일을 업데이트한 후, TanStack Start 프로젝트에서 경로 별칭을 활성화하려면 `vite-tsconfig-paths` 플러그인을 설치해야 합니다. 다음 커맨드를 실행하여 설치할 수 있습니다.

```sh
npm install -D vite-tsconfig-paths
```

이제 `app.config.ts` 파일을 업데이트하여 다음 내용을 포함시켜야 합니다.

```ts
// app.config.ts
import { defineConfig } from '@tanstack/start/config'
import viteTsConfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  vite: {
    plugins: [
      // 경로 별칭을 활성화하는 플러그인
      viteTsConfigPaths({
        projects: ['./tsconfig.json'],
      }),
    ],
  },
})
```

이 설정이 완료되면, 이제 다음과 같이 경로 별칭을 사용하여 파일을 임포트할 수 있습니다.

```ts
// app/routes/posts/$postId/edit.tsx
import { Input } from '~/components/ui/input'

// 기존 방식
import { Input } from '../../../components/ui/input'
```


