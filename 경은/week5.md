## [Chapter 9] 비동기 프로그래밍 패턴

- 비동기 자바스크립트 프로그래밍은 브라우저가 이벤트에 응답하여 다른 코드를 실행하는 동안, 백그라운드에서 오랜 시간이 걸리는 작업을 수행하게 해줍니다.

### 비동기 프로그래밍

- 동기 코드는 블로킹 방식으로 실행됩니다.
- 비동기 코드는 논블로킹 방식으로 실행됩니다.

### 프로미스 패턴

#### 프로미스 순차 실행

- 프로미스 순차 실행 패턴은 Promise.resolve 메서드를 사용하여 프로미스를 순차적으로 실행할 수 있도록 해줍니다.

#### 프로미스 메모이제이션

- 프로미스 메모이제이션(Promise Memoization) 패턴은 캐시를 사용하여 프로미스 함수 호출의 결과 값을 저장합니다. 이를 통해 중복된 요청을 방지할 수 있습니다.
- new Map()으로 Map을 만들어 이곳에 set합니다.

#### 프로미스 데코레이터

- 프로미스 데코레이터(Promise Decorator) 패턴은 고차 함수를 사용하여 프로미스에 적용할 수 있는 데코레이터를 생성합니다. 이를 통해 프로미스에 추가적인 기능을 부여할 수 있습니다.

#### 프로미스 경쟁

- 프로미스 경쟁(Promise Race) 패턴은 여러 프로미스를 동시에 실행하고 가장 먼저 완료되는 프로미스의 결과를 반환합니다.

### async/await 패턴

- Promise랑 똑같은 얘기

## [Chapter 10] 모듈형 자바스크립트 디자인 패턴

- 확장 가능한 자바스크립트 환경에서 모듈형modular이란 서로 의존성이 낮은 기능들이 모듈로써 저장된 형태를 뜻합니다. 이러한 느슨한 결합은 의존성을 제거하여 애플리케이션의 유지보수를 용이하게 만들어줍니다.

### AMD

- AMD(Asynchronous Module Definition) 모듈 형식은 모듈과 의존성 모두를 비동기적으로 로드06할 수 있도록 설계된 모듈 정의 방식입니다.
- define으로 모듈을 정의하고, require로 의존성 로딩을 처리

### CommonJS

- CommonJS는 서버 사이드에서 모듈을 선언하는 간단한 API를 지정하는 모듈 제안입니다. AMD와는 달리 I/O, 파일 시스템, 프로미스 등 더욱 광범위한 부분을 다룹니다.
- exports 변수는 다른 모듈에 내보내고자 하는 객체를 담고, require 함수는 다른 모듈에서 내보낸 객체를 가져올 때 사용

### AMD vs CommonJS: 동상이몽

- AMD는 브라우저 우선 접근 방식을 채택하여 비동기 동작과 간소화된 하위 호환성을 선택한 반면, 파일 I/O에 대한 개념은 없습니다. 또한 객체, 함수, 생성자, 문자열, JSON 등 다양한 형태의 모듈을 지원하며 브라우저에서 자체적으로 실행된다는 면에서 대단히 유연한 포맷입니다.

- 반면에 CommonJS는 서버 우선 접근 방식을 취하며 동기적 작동, 전역 변수와의 독립성 그리고 미래의 서버 환경을 고려합니다. 이를 통해 AMD에서 필수적인 define() 함수를 사용하지 않아도 됩니다. 다만, CommonJS 모듈은 오직 객체만을 모듈로써 지원합니다.

### UMD: 플러그인을 위한 AMD 및 CommonJS 호환 모듈

---

### 이야기할 거리

- new Map()을 이용해서 캐시 저장소를 만들고 캐싱하는걸 보고 리액트쿼리도 이렇게 동작할까? 찾아보니 MutationCache에서 new Map()을 사용하는 것을 보았습니다. Map이 캐싱으로 많이 쓰이나봐요

```ts
export class MutationCache extends Subscribable<MutationCacheListener> {
  #mutations: Map<string, Array<Mutation<any, any, any, any>>>
  #mutationId: number

  constructor(public config: MutationCacheConfig = {}) {
    super()
    this.#mutations = new Map()
    this.#mutationId = Date.now()
  }

  build<TData, TError, TVariables, TContext>(
    client: QueryClient,
    options: MutationOptions<TData, TError, TVariables, TContext>,
    state?: MutationState<TData, TError, TVariables, TContext>,
  ): Mutation<TData, TError, TVariables, TContext> {
    const mutation = new Mutation({
      mutationCache: this,
      mutationId: ++this.#mutationId,
      options: client.defaultMutationOptions(options),
      state,
    })

    this.add(mutation)

    return mutation
  }
```
