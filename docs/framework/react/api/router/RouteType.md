# Table of Contents

- [라우트 타입](#라우트-타입)
  - [`Route`의 속성과 메서드](#route의-속성과-메서드)
    - [`.addChildren` 메서드](#addchildren-메서드)
    - [`.update` 메서드](#update-메서드)
    - [`.lazy` 메서드](#lazy-메서드)
    - [`RouteApi` 메서드](#routeapi-메서드)

# 라우트 타입

`Route` 타입은 라우트 인스턴스를 설명하는 데 사용됩니다.


## `Route`의 속성과 메서드

`Route` 인스턴스는 다음과 같은 속성과 메서드를 가지고 있습니다:


### `.addChildren` 메서드

- 타입: `(children: Route[]) => this`
- 라우트 인스턴스에 자식 라우트를 추가하고, 새로운 자식 라우트를 반영한 타입으로 업데이트된 라우트 인스턴스를 반환합니다.


### `.update` 메서드

- 타입: `(options: Partial<UpdatableRouteOptions>) => this`
- 라우트 인스턴스의 옵션을 새로운 값으로 업데이트하고, 업데이트된 라우트 인스턴스를 반환합니다. 반환된 인스턴스는 새로운 옵션을 반영한 타입을 가집니다.
- 특정 상황에서는 순환 참조를 피하기 위해 라우트 인스턴스가 생성된 후에 옵션을 업데이트하는 것이 유용할 수 있습니다.
- ...`RouteApi` 메서드


### `.lazy` 메서드

- **타입**: `(lazyImporter: () => Promise<Partial<UpdatableRouteOptions>>) => this`
- **설명**: 라우트 인스턴스에 새로운 지연 로딩 임포터(lazy importer)를 추가합니다. 이 임포터는 라우트를 로드할 때 지연되어 해결됩니다. 이 기능은 코드 분할(code splitting)에 유용하게 사용할 수 있습니다.

```typescript
// 예제 코드
const route = new Route('/example')
  .lazy(() => import('./ExampleComponent'))
  .then(component => {
    // 라우트 로드 후 추가 작업
  });
```

이 메서드를 사용하면, 라우트가 실제로 필요할 때만 해당 컴포넌트를 로드할 수 있어 초기 로딩 시간을 줄일 수 있습니다.


### `RouteApi` 메서드

- [`RouteApi`](./RouteApiType.md)에 있는 모든 메서드를 사용할 수 있습니다.


