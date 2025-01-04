# Table of Contents

- [ESLint 플러그인 라우터](#eslint-플러그인-라우터)
  - [설치](#설치)
  - [Flat Config (`eslint.config.js`)](#flat-config-eslintconfigjs)
    - [권장 Flat Config 설정](#권장-flat-config-설정)
    - [커스텀 Flat Config 설정](#커스텀-flat-config-설정)
  - [레거시 설정 (`.eslintrc`)](#레거시-설정-eslintrc)
    - [권장 레거시 설정 구성](#권장-레거시-설정-구성)
    - [커스텀 레거시 설정 방법](#커스텀-레거시-설정-방법)
  - [규칙](#규칙)

# ESLint 플러그인 라우터

TanStack 라우터는 자체 ESLint 플러그인을 제공합니다. 이 플러그인은 **모범 사례를 강제**하고, **흔히 발생하는 실수를 방지**하는 데 도움을 줍니다.


## 설치

이 플러그인은 별도의 패키지로 설치해야 합니다:

```sh
npm install -D @tanstack/eslint-plugin-router
```

또는

```sh
pnpm add -D @tanstack/eslint-plugin-router
```

또는

```sh
yarn add -D @tanstack/eslint-plugin-router
```

또는

```sh
bun add -D @tanstack/eslint-plugin-router
```


## Flat Config (`eslint.config.js`)

ESLint 9.0 릴리스와 함께 새로운 설정 방식인 **Flat Config**가 도입되었습니다. 이 새로운 형식은 기존 `.eslintrc` 방식보다 더 유연하며, 더 세밀하게 ESLint를 설정할 수 있게 해줍니다. TanStack Router ESLint 플러그인은 이 새로운 형식을 지원하며, 플러그인의 모든 권장 규칙을 활성화할 수 있는 **추천 설정**을 제공합니다.

```javascript
// eslint.config.js 예제
export default [
  {
    // TanStack Router ESLint 플러그인의 권장 설정 적용
    plugins: ['@tanstack/eslint-plugin-router'],
    rules: {
      '@tanstack/router/rule-name': 'error', // 예시 규칙
    },
  },
];
```

이 설정 파일은 모던 자바스크립트 문법을 사용하며, 기존 방식보다 더 간결하고 명확한 구성을 가능하게 합니다.


### 권장 Flat Config 설정

플러그인의 모든 권장 규칙을 활성화하려면 다음 설정을 추가하세요:

```js
// eslint.config.js
import pluginRouter from '@tanstack/eslint-plugin-router'

export default [
  ...pluginRouter.configs['flat/recommended'],
  // 기타 설정...
]
```


### 커스텀 Flat Config 설정

다른 방법으로, 플러그인을 불러오고 사용하려는 규칙만 설정할 수도 있습니다:

```js
// eslint.config.js
import pluginRouter from '@tanstack/eslint-plugin-router'

export default [
  {
    plugins: {
      '@tanstack/router': pluginRouter,
    },
    rules: {
      '@tanstack/router/create-route-property-order': 'error',
    },
  },
  // 기타 설정...
]
```


## 레거시 설정 (`.eslintrc`)

ESLint 9.0 출시 이전에는 `.eslintrc` 파일을 사용해 ESLint를 설정하는 것이 가장 일반적인 방법이었습니다. TanStack Router ESLint 플러그인은 여전히 이 설정 방식을 지원합니다.


### 권장 레거시 설정 구성

플러그인의 모든 권장 규칙을 활성화하려면 `extends`에 `plugin:@tanstack/eslint-plugin-router/recommended`를 추가하세요:

```json
{
  "extends": ["plugin:@tanstack/eslint-plugin-router/recommended"]
}
```


### 커스텀 레거시 설정 방법

다른 방법으로, `@tanstack/eslint-plugin-router`를 플러그인 섹션에 추가하고 사용할 규칙을 설정할 수 있습니다:

```json
{
  "plugins": ["@tanstack/eslint-plugin-router"],
  "rules": {
    "@tanstack/router/create-route-property-order": "error"
  }
}
```


## 규칙

TanStack Router ESLint 플러그인에서 사용할 수 있는 규칙은 다음과 같습니다:

- [@tanstack/router/create-route-property-order](./create-route-property-order.md)


