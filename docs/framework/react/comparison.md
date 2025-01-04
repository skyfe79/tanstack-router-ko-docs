# Table of Contents

- [비교 | TanStack Router & TanStack Start vs Next.js vs React Router / Remix](#비교--tanstack-router--tanstack-start-vs-nextjs-vs-react-router--remix)

# 비교 | TanStack Router & TanStack Start vs Next.js vs React Router / Remix

새로운 도구를 선택하기 전에, 경쟁 제품과 어떻게 비교되는지 알아보는 것이 좋습니다!

> 이 비교표는 가능한 한 정확하고 편견 없이 작성되었습니다. 만약 이 라이브러리 중 하나를 사용하고 있고 정보가 개선될 수 있다고 생각한다면, 페이지 하단의 "Edit this page on GitHub" 링크를 통해 변경 사항을 제안해 주세요. (주장에 대한 설명이나 증거와 함께)

기능/능력 키:

- ✅ 퍼스트클래스, 내장되어 있으며 추가 설정이나 코드 없이 바로 사용 가능
- 🔵 추가 패키지를 통해 지원
- 🟡 부분 지원 (5점 만점)
- 🔶 가능하지만 커스텀 코드/구현/캐스팅이 필요
- 🛑 공식적으로 지원되지 않음

|                                                | TanStack Router / Start                          | React Router DOM [_(웹사이트)_][router]               | Next.JS [_(웹사이트)_][nextjs]                        |
| ---------------------------------------------- | ------------------------------------------------ | ----------------------------------------------------- | ----------------------------------------------------- |
| GitHub 저장소 / 스타 수                        | [![][stars-tanstack-router]][gh-tanstack-router] | [![][stars-router]][gh-router]                        | [![][stars-nextjs]][gh-nextjs]                        |
| 번들 크기                                      | [![][bp-tanstack-router]][bpl-tanstack-router]   | [![][bp-router]][bpl-router]                          | ❓                                                    |
| History, Memory & Hash 라우터                  | ✅                                               | ✅                                                    | 🛑                                                    |
| 중첩/레이아웃 라우트                           | ✅                                               | ✅                                                    | 🟡                                                    |
| Suspense와 유사한 라우트 트랜지션              | ✅                                               | ✅                                                    | ✅                                                    |
| 타입 안전한 라우트                             | ✅                                               | 🟡 (1/5)                                              | 🟡                                                    |
| 코드 기반 라우트                               | ✅                                               | ✅                                                    | 🛑                                                    |
| 파일 기반 라우트                               | ✅                                               | ✅                                                    | ✅                                                    |
| 가상/프로그래밍 방식의 파일 기반 라우트        | ✅                                               | ✅                                                    | 🛑                                                    |
| 라우트 로더                                    | ✅                                               | ✅                                                    | ✅                                                    |
| SWR 로더 캐싱                                  | ✅                                               | 🛑                                                    | ✅                                                    |
| 라우트 미리 가져오기                           | ✅                                               | ✅                                                    | ✅                                                    |
| 자동 라우트 미리 가져오기                      | ✅                                               | ✅                                                    | ✅                                                    |
| 라우트 미리 가져오기 지연                      | ✅                                               | 🔶                                                    | 🛑                                                    |
| 경로 파라미터                                  | ✅                                               | ✅                                                    | ✅                                                    |
| 타입 안전한 경로 파라미터                      | ✅                                               | ✅                                                    | 🛑                                                    |
| 타입 안전한 라우트 컨텍스트                    | ✅                                               | 🛑                                                    | 🛑                                                    |
| 경로 파라미터 유효성 검사                      | ✅                                               | 🛑                                                    | 🛑                                                    |
| 커스텀 경로 파라미터 파싱/직렬화               | ✅                                               | 🛑                                                    | 🛑                                                    |
| 순위 기반 라우트                               | ✅                                               | ✅                                                    | ✅                                                    |
| 활성 링크 커스터마이징                         | ✅                                               | ✅                                                    | ✅                                                    |
| 낙관적 UI                                      | ✅                                               | ✅                                                    | 🔶                                                    |
| 타입 안전한 절대 및 상대 네비게이션            | ✅                                               | 🛑                                                    | 🛑                                                    |
| 라우트 마운트/트랜지션/언마운트 이벤트         | ✅                                               | 🛑                                                    | 🛑                                                    |
| 개발자 도구                                    | ✅                                               | 🛑                                                    | 🛑                                                    |
| 기본 검색 파라미터                             | ✅                                               | ✅                                                    | ✅                                                    |
| 검색 파라미터 훅                               | ✅                                               | ✅                                                    | ✅                                                    |
| `<Link/>`/`useNavigate` 검색 파라미터 API      | ✅                                               | 🟡 (검색 문자열만 `to`/`search` 옵션을 통해 지원)     | 🟡 (검색 문자열만 `to`/`search` 옵션을 통해 지원)     |
| JSON 검색 파라미터                             | ✅                                               | 🔶                                                    | 🔶                                                    |
| 타입 안전한 검색 파라미터                      | ✅                                               | 🛑                                                    | 🛑                                                    |
| 검색 파라미터 스키마 유효성 검사               | ✅                                               | 🛑                                                    | 🛑                                                    |
| 검색 파라미터 불변성 + 구조적 공유             | ✅                                               | 🔶                                                    | 🛑                                                    |
| 커스텀 검색 파라미터 파싱/직렬화               | ✅                                               | 🔶                                                    | 🛑                                                    |
| 검색 파라미터 미들웨어                         | ✅                                               | 🛑                                                    | 🛑                                                    |
| Suspense 라우트 엘리먼트                       | ✅                                               | ✅                                                    | ✅                                                    |
| 라우트 오류 엘리먼트                           | ✅                                               | ✅                                                    | ✅                                                    |
| 라우트 대기 엘리먼트                           | ✅                                               | ✅                                                    | ✅                                                    |
| `<Block>`/`useBlocker`                         | ✅                                               | 🔶                                                    | ❓                                                    |
| 지연된 프리미티브                              | ✅                                               | ✅                                                    | ✅                                                    |
| 네비게이션 스크롤 복원                         | ✅                                               | ✅                                                    | ❓                                                    |
| 로더 캐싱 (SWR + 무효화)                       | 🔶 (TanStack Query 사용 권장)                    | 🛑                                                    | ✅                                                    |
| 라우터 무효화                                  | ✅                                               | ✅                                                    | ✅                                                    |
| 런타임 라우트 조작 (Fog of War)                | 🛑                                               | ✅                                                    | ✅                                                    |
| --                                             | --                                               | --                                                    | --                                                    |
| **풀 스택**                                    | --                                               | --                                                    | --                                                    |
| SSR                                            | ✅                                               | ✅                                                    | ✅                                                    |
| 스트리밍 SSR                                   | ✅                                               | ✅                                                    | ✅                                                    |
| 일반 RPC                                       | ✅                                               | 🛑                                                    | 🛑                                                    |
| 일반 RPC 미들웨어                               | ✅                                               | 🛑                                                    | 🛑                                                    |
| React 서버 함수                                | ✅                                               | 🛑                                                    | ✅                                                    |
| React 서버 함수 미들웨어                       | ✅                                               | 🛑                                                    | 🛑                                                    |
| API 라우트                                     | ✅                                               | ✅                                                    | ✅                                                    |
| API 미들웨어                                   | ✅                                               | 🛑                                                    | ✅                                                    |
| React 서버 컴포넌트                            | 🛑                                               | 🛑                                                    | ✅                                                    |
| `<Form>` API                                   | 🛑                                               | ✅                                                    | ✅                                                    |

[bp-tanstack-router]: https://badgen.net/bundlephobia/minzip/@tanstack/react-router
[bpl-tanstack-router]: https://bundlephobia.com/result?p=@tanstack/react-router
[gh-tanstack-router]: https://github.com/tanstack/router
[stars-tanstack-router]: https://img.shields.io/github/stars/tanstack/router?label=%F0%9F%8C%9F
[_]: _
[router]: https://github.com/remix-run/react-router
[bp-router]: https://badgen.net/bundlephobia/minzip/react-router
[gh-router]: https://github.com/remix-run/react-router
[stars-router]: https://img.shields.io/github/stars/remix-run/react-router?label=%F0%9F%8C%9F
[bpl-router]: https://bundlephobia.com/result?p=react-router
[bpl-history]: https://bundlephobia.com/result?p=history
[_]: _
[nextjs]: https://nextjs.org/docs/routing/introduction
[bp-nextjs]: https://badgen.net/bundlephobia/minzip/next.js?label=All
[gh-nextjs]: https://github.com/vercel/next.js
[stars-nextjs]: https://img.shields.io/github/stars/vercel/next.js?label=%F0%9F%8C%9F
[bpl-nextjs]: https://bundlephobia.com/result?p=next


