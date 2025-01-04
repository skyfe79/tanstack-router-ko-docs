# Table of Contents

- [TanStack Start 개요](#tanstack-start-개요)
  - [Router vs Start: 어떤 것을 선택해야 할까?](#router-vs-start-어떤-것을-선택해야-할까)
    - [TanStack Router의 주요 기능](#tanstack-router의-주요-기능)
    - [TanStack Start의 주요 기능](#tanstack-start의-주요-기능)
    - [결론](#결론)
  - [어떻게 동작하나요?](#어떻게-동작하나요)
  - [언제 사용해야 할까요?](#언제-사용해야-할까요)
  - [언제 사용하지 않는 것이 좋을까요?](#언제-사용하지-않는-것이-좋을까요)
  - [TanStack Start의 자금 지원 방식](#tanstack-start의-자금-지원-방식)
  - [시작할 준비가 되셨나요?](#시작할-준비가-되셨나요)

# TanStack Start 개요

TanStack Start는 TanStack Router로 구동되는 풀스택 React 프레임워크입니다. TanStack Router, Vinxi, Vite를 기반으로 전체 문서 SSR(서버 사이드 렌더링), 스트리밍, 서버 함수, 번들링 등을 제공합니다. 여러분이 선호하는 호스팅 프로바이더에 바로 배포할 수 있습니다!


## Router vs Start: 어떤 것을 선택해야 할까?

TanStack Router는 React 애플리케이션을 위한 강력하고 타입 안전하며 기능이 풍부한 라우팅 시스템입니다. 이 라우터는 복잡한 풀스택 라우팅 요구사항도 쉽게 처리할 수 있도록 설계되었습니다. TanStack Start는 Router의 타입 시스템을 기반으로 타입 안전한 풀스택 API를 제공하여 빠른 개발 속도를 유지할 수 있게 도와줍니다.

### TanStack Router의 주요 기능

- **100% 타입 추론(TypeScript 지원)**
- **타입 안전한 네비게이션**
- **중첩 라우팅 및 레이아웃 라우트**
- **SWR 캐싱이 포함된 내장 라우트 로더**
- **클라이언트 측 데이터 캐시(TanStack Query, SWR 등)에 최적화**
- **자동 라우트 미리 가져오기(prefetching)**
- **비동기 라우트 엘리먼트 및 에러 경계**
- **파일 기반 라우트 생성**
- **타입 안전한 JSON 우선 검색 파라미터 상태 관리 API**
- **경로 및 검색 파라미터 스키마 검증**
- **검색 파라미터 네비게이션 API**
- **커스텀 검색 파라미터 파서/직렬화 지원**
- **검색 파라미터 미들웨어**
- **라우트 매칭/로딩 미들웨어**

### TanStack Start의 주요 기능

- **전체 문서 SSR(서버 사이드 렌더링)**
- **스트리밍**
- **서버 함수 / RPC(원격 프로시저 호출)**
- **번들링**
- **배포**
- **풀스택 타입 안전성**

### 결론

- **TanStack Router**는 클라이언트 측 라우팅에 적합합니다.
- **TanStack Start**는 풀스택 라우팅을 위해 사용하세요.

이 두 도구는 각각의 목적에 맞게 설계되었으므로, 여러분의 프로젝트 요구사항에 따라 적절한 도구를 선택하면 됩니다.


## 어떻게 동작하나요?

TanStack Start는 **Vinxi**라는 도구를 사용해 여러분의 애플리케이션을 번들링하고 배포합니다. 사실 이 도구는 Solid Start에서도 사용 중입니다! Vinxi를 통해 이전에는 할 수 없었던 몇 가지 작업을 수행할 수 있습니다:

- SSR, 스트리밍, 하이드레이션을 위한 통합 API 제공
- 클라이언트 사이드 코드에서 서버 전용 코드 분리 (예: 서버 함수)
- 어떤 호스팅 프로바이더에도 배포할 수 있도록 애플리케이션 번들링

Vinxi는 이러한 기능들을 가능하게 해주는 핵심 도구로, 다양한 환경에서 애플리케이션을 효율적으로 관리할 수 있도록 지원합니다.


## 언제 사용해야 할까요?

TanStack Start는 다음과 같은 요구사항을 가진 풀스택 React 애플리케이션을 구축할 때 적합합니다:

- **전체 문서 SSR & 하이드레이션**
- **스트리밍**
- **서버 함수 / RPCs**
- **풀스택 타입 안정성**
- **견고한 라우팅**
- **풍부한 클라이언트 측 상호작용**

이러한 기능들이 필요한 프로젝트라면 TanStack Start를 사용해 보세요.


## 언제 사용하지 않는 것이 좋을까요?

TanStack Start는 다음과 같은 경우에 적합하지 않습니다:

- 사이트가 완전히 정적(static)일 때
- 서버 렌더링 사이트를 목표로 하며, 클라이언트 측에서 자바스크립트를 전혀 사용하지 않거나 최소한의 상호작용만 필요한 경우
- React Server Components(RSCs)를 우선적으로 지원하는 프레임워크를 찾고 있을 때 (곧 우리만의 멋진 방식으로 RSCs를 지원할 예정입니다!)


## TanStack Start의 자금 지원 방식

TanStack은 파트너사와 긴밀히 협력하여 최고의 개발자 경험을 제공하고, 어디서나 작동하며 업계 전문가들이 검증한 솔루션을 제공합니다. 각 파트너사는 TanStack 생태계에서 고유한 역할을 수행합니다:

- **Netlify**
  <a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" style="height: 90px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">
  </picture>
  </a>
  웹 애플리케이션을 위한 선도적인 호스팅 플랫폼으로, 빠르고 안전하며 신뢰할 수 있는 환경을 제공합니다. TanStack은 Netlify와 긴밀히 협력하여 TanStack Start 애플리케이션이 해당 플랫폼에 원활하게 배포될 뿐만 아니라, 어디에 배포하든 성능, 보안, 신뢰성에 대한 최적의 방법을 구현할 수 있도록 합니다.

- **Clerk**
  <a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" style="height: 40px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">
  </picture>
  </a>
  현대적인 웹 애플리케이션을 위한 최고의 인증 경험을 제공합니다. Clerk는 TanStack Start 사용자에게 퍼스트클래스 통합과 인증 솔루션을 제공하며, TanStack 팀과 긴밀히 협력하여 최신 인증 모범 사례를 반영한 API를 제공합니다.

- **Convex**
  <a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" style="height: 40px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">
  </picture>
  </a>
  서버리스 데이터베이스 플랫폼으로, TanStack Start와 원활하게 통합됩니다. Convex는 애플리케이션 데이터 관리를 단순화하고, 실시간으로 확장 가능하며 트랜잭션을 지원하는 데이터 백엔드를 제공합니다. 또한 TanStack 팀과 긴밀히 협력하여 최신 데이터베이스 모범 사례를 반영한 API를 제공합니다.

- **Sentry**
  <a href="https://sentry.io?utm_source=tanstack" alt='Sentry Logo'>
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-dark.svg" style="height: 60px;">
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">
  </picture>
  </a>
  강력하고 기능이 풍부한 관측 가능성 플랫폼으로, TanStack Start와 원활하게 통합됩니다. Sentry는 개발자가 실시간으로 충돌을 모니터링하고 수정할 수 있도록 도와주며, 애플리케이션의 성능과 오류 추적에 대한 통찰력을 제공합니다. Sentry는 TanStack 팀과 긴밀히 협력하여 최신 관측 가능성 모범 사례를 반영한 API를 제공합니다.


## 시작할 준비가 되셨나요?

다음 페이지로 이동하여 TanStack Start를 설치하고 첫 번째 앱을 만드는 방법을 알아보세요!


