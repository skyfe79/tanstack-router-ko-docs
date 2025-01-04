# Table of Contents

- [인증](#인증)
  - [어떤 것을 사용해야 할까요?](#어떤-것을-사용해야-할까요)
  - [Clerk란 무엇인가요?](#clerk란-무엇인가요)
  - [문서 및 API](#문서-및-api)

# 인증

<!-- 여기에 인증을 위한 플레이스홀더 콘텐츠가 필요합니다. 우리 파트너 중 하나인 Clerk은 TanStack과 함께 사용할 때 "선호되는" 인증 방식으로 우선적으로 다뤄져야 하지만, 다른 모든 인증 프로바이더와 전략도 지원할 예정입니다. Clerk 및 다른 인증 프로바이더에 대한 문서가 준비될 때까지 여기에 일반적인 인증 콘텐츠를 작성해 주세요: -->

인증은 사용자의 신원을 확인하는 과정입니다. 이는 사용자가 로그인하거나 보호된 리소스에 접근해야 하는 모든 애플리케이션에서 중요한 부분입니다. TanStack Start는 여러분의 애플리케이션에서 인증을 구현하기 위해 필요한 풀스택 API를 제공합니다.


## 어떤 것을 사용해야 할까요?

TanStack Start는 **모든 인증 프로바이더와 함께 작동하도록 설계**되었습니다. 이미 사용 중인 인증 프로바이더나 전략이 있다면, 기존 예제를 찾거나 TanStack Start가 제공하는 풀스택 API를 사용해 직접 인증 로직을 구현할 수 있습니다.

하지만 인증은 가볍게 다룰 문제가 아닙니다. 저희가 여러 차례 검토하고 사용해 본 결과, 최상의 인증 경험을 위해 [Clerk](https://clerk.dev)를 적극 추천합니다. Clerk는 다양한 인증 API와 UI 컴포넌트를 제공하여 애플리케이션에 인증을 쉽게 구현하고 원활한 사용자 경험을 제공합니다.


## Clerk란 무엇인가요?

<a href="https://go.clerk.com/wOwHtuJ" alt="Clerk 로고">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
    <img alt="Clerk 로고" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
  </picture>
</a>

Clerk는 현대적인 인증 플랫폼으로, 애플리케이션에 인증 기능을 쉽게 구현할 수 있도록 다양한 API와 UI 컴포넌트를 제공합니다. Clerk는 사용하기 쉬우면서도 원활한 사용자 경험을 제공하도록 설계되었습니다. Clerk를 사용하면 단 몇 분 만에 애플리케이션에 인증 기능을 추가할 수 있으며, 사용자에게 안전하고 신뢰할 수 있는 인증 경험을 제공할 수 있습니다.

- Clerk에 대해 더 알아보려면 [Clerk 웹사이트](https://go.clerk.com/wOwHtuJ)를 방문하세요.
- 가입하려면 [Clerk 대시보드](https://go.clerk.com/PrSDXti)를 확인하세요.
- Clerk를 시작하려면 [공식 Start + Clerk 예제](../../examples/start-clerk-basic/)를 참고하세요.


## 문서 및 API

TanStack Start를 사용하여 여러분만의 인증 로직을 구현하는 방법에 대한 문서는 곧 제공될 예정입니다! 그동안은 시작점으로 `-auth` 접두사가 붙은 [예제](../../../../examples)를 확인해 보세요.


