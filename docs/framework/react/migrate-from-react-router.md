# Table of Contents

- [React Router에서 TanStack Router로 마이그레이션 체크리스트](#react-router에서-tanstack-router로-마이그레이션-체크리스트)

# React Router에서 TanStack Router로 마이그레이션 체크리스트

**_UI가 비어 있다면 콘솔을 열어보세요. `useNavigate`를 컨텍스트 외부에서 사용할 수 없다는 오류가 표시될 가능성이 높습니다. 이는 여전히 React Router API가 임포트되고 참조되고 있다는 의미입니다. 모든 React Router 임포트를 찾아 제거해야 합니다. 가장 쉬운 방법은 `react-router-dom`을 제거한 후 타입스크립트 오류를 확인하는 것입니다. 그러면 어떤 부분을 `@tanstack/react-router`로 변경해야 하는지 알 수 있습니다._**

[예제 저장소](https://github.com/Benanna2019/SickFitsForEveryone/tree/migrate-to-tanstack/router/React-Router)를 참고하세요.

- [ ] Router 설치 - `npm i @tanstack/react-router`
- [ ] **선택 사항:** React Router를 제거하여 임포트에 대한 타입스크립트 오류를 확인합니다.
  - 현재로서는 점진적 마이그레이션이 가능한지 확실하지 않지만, 여러 라우터 프로바이더를 사용할 수 있습니다. 이는 바람직하지 않습니다.
  - React Router와 TanStack Router의 API는 매우 유사하며, 회사에서 스프린트 주기로 작업한다면 한두 번의 스프린트 내에 처리할 수 있을 것입니다.
- [ ] 기존 React Router 라우트에 대한 라우트 생성
- [ ] 루트 라우트 생성
- [ ] 라우터 인스턴스 생성
- [ ] main.tsx에 전역 모듈 추가
- [ ] main.tsx에서 React Router (`createBrowserRouter` 또는 `BrowserRouter`), `Routes`, `Route` 컴포넌트 제거
- [ ] **선택 사항:** 커스텀 설정/프로바이더를 위한 `render` 함수 리팩토링 - 위의 저장소에 예제가 있습니다. Supertokens의 경우 이 작업이 필요했습니다. Supertokens는 React Router와 다른 React 구현체에 대해 각기 다른 설정을 가지고 있습니다.
- [ ] RouterProvider를 설정하고 라우터를 prop으로 전달
- [ ] 모든 React Router `Link` 컴포넌트를 `@tanstack/react-router`의 `Link` 컴포넌트로 교체
  - [ ] `to` prop에 리터럴 경로 추가
  - [ ] 필요한 경우 `params` prop 추가, 예: `params={{ orderId: order.id }}`
- [ ] 모든 React Router `useNavigate` 훅을 `@tanstack/react-router`의 `useNavigate` 훅으로 교체
  - [ ] 필요한 경우 `to` 속성과 `params` 속성 설정
- [ ] 모든 React Router `Outlet`을 `@tanstack/react-router`의 동등한 컴포넌트로 교체
- [ ] React Router의 `useSearchParams` 훅을 사용 중이라면, 검색 파라미터 기본값을 라우트 정의의 `validateSearch` 속성으로 이동
  - [ ] `useSearchParams` 훅 대신 `@tanstack/react-router`의 `Link` 컴포넌트의 `search` 속성을 사용하여 검색 파라미터 상태 업데이트
  - [ ] 검색 파라미터를 읽으려면 다음과 같이 할 수 있습니다:
    ```typescript
    const { page } = useSearch({ from: productPage.fullPath })
    ```
- [ ] React Router의 `useParams` 훅을 사용 중이라면, `@tanstack/react-router`에서 임포트하고 `from` 속성을 파라미터를 읽을 리터럴 경로 이름으로 설정
  - 예를 들어, 경로 이름이 `orders/$orderid`인 경우:
  - `useParams` 훅을 다음과 같이 설정합니다:
    ```typescript
    const params = useParams({ from: "/orders/$orderId" })
    ```
  - 그런 다음, order id에 접근하려면 params 객체에서 가져옵니다:
    ```typescript
    params.orderId
    ```


