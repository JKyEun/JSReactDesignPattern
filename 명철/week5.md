# 5주차

## 별도 정리

생성역사(?)

CommonJS -> AMD -> UMD -> ESM

### AMD
(Asynchronous Module Definition)
- 비동기적이고 동적으로 모듈 로드
  - 초기 로딩 시간 감소 (비동기)
  - 필요한 모듈을 적절할 때 로드 (동적)
- 브라우저 환경을 위함
- 스크립트 로더: RequireJS, curl.js
- 장점
  - 독립적인 모듈과 의존성을 명확하게 선언
  - 전역 네임스페이스 오염 방지
- 단점
  - 서버사이드와 호환 되지 않음
  - ESM의 표준화에 따라 사용빈도 줄어듦

### CommonJS
- 동기적이고 동적으로 모듈 로드
- Node.js에서 사용
- `require()`, `exports`로 활용가능

### UMD
(Universal Module Definition)
- 브라우저와 서버에서 모두 동작이 가능한 형태

### ESM
(ECMAScript Modules)
- 표준 모듈 시스템
- `import`, `export` 키워드 사용
- 정적 분석이 가능
  - 선언적으로 작성하며, 파싱 시점에 구조를 파악가능
- 의존성 트리 구축 가능
- 트리쉐이킹 원활
- 순환 참조 감지 가능
  -> 효율적이고 예측가능한 코드 작성 가능
- 정적 분석 과정이 있어 CJS보다는 느리다

- 요즘엔 서버에선 CommonJS, 브라우저에선 ESM 주로 사용
- ESM이 표준이지만 이미 CommonJS로된 오픈소스들이 많음
- CommonJS와 ESM은 호환되지 않는다.
  - ESM에서 CJS를 `import` 할 수는 있으나 default import만 가능하다.
  - 반대는 쉽지 않다. (특히 top-level await 때문에)

## 이야깃 거리

### ESM <-> CJS 모듈 사용하다가 에러난 경험을 공유해줘여

### 왜 fetch는 응답을 프라미스로 주는가?
(예시를 보다가 뜬금)

```ts
const response = await fetch('http://어떤주소.com');

// Promise를 반환
response.json()
```

1. 응답 데이터가 엄청 클 경우 파싱에 오랜 시간이 걸릴 수 있다.
2. 서버가 응답을 청크 단위로 줄 수도 있음.
3. 파싱 에러를 catch 블록에서 처리 가능함.

### Axios vs fetch API

> https://blog.logrocket.com/axios-vs-fetch-best-http-requests/

#### Axios vs. fetch(): Which is best for making HTTP requests?

- Axios
  - 별도 패키지 다운로드 필요
  - 구형 브라우저 지원 (XMLHttpRequest를 기반으로 하기 때문)
  - Timeout 셋팅의 간편
    - option 값으로 주면 끝
  - 응답 값 JSON 자동 파싱
  - 인터셉터를 통한 간단한 글로벌 처리
  - 간단한 프로그레스 처리
  - 서버에러 응답 시 `reject`

- Fetch
  - Navtive API로 사용가능, 번들 용량 줄일 수 있음.
  - Timeout 셋팅시 `AbortController` 필요
  - 응답 값 `json()`을 별도로 호출 필요
  - 프로그레스 처리시 `ReadableStream` 내부에서 별도 로직 구현 필요
  - 서버에러 응답 시 `ok` status에 boolean 값을 주고 `resolve`
  - 더 많은 설정이 필요햐지만 더 낮은 수준의 제어가 가능하다는 것이 장점

일반적인 내용들인 것 같은데 실무적으로 각 필요성이 더 부각되는 예시들이 있을까요?

---

