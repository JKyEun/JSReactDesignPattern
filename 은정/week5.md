# 9강. 비동기 프로그래밍 패턴

## 정리

### 9.1 비동기 프로그래밍

- 자바스크립트에서 동기 코드는 블로킹 방식으로 실행된다
- 비동기 코드는 논블로킹 방식으로 실행된다
- 네트워크 요청, 데이터베이스 읽기쓰기, 또는 기타 I/O 작업이 비동기 코드를 사용하기 적합하다
- 콜백(callback), 프로미스(promise), 비동기(async/await)을 통해 비동기 코드를 사용한다

### 9.2 배경

- 자바스크립트에서 콜백 함수는 다른 함수에 인수로서 전달되어, 비동기 작업이 완료된 후 실행된다
- 콜백을 사용할 대 '콜백 지옥'을 초래할 수 있는데, 이는 중첩된 콜백 구조로 인해 코드의 가독성과 유지보수성이 크게 저하되는 상황을 말한다

### 9.3 프로미스 패턴

- 프로미스는 비동기 작업의 결과를 나타내는 객체로, 대기(pending), 완료(fulfilled), 거부(rejected)의 세 가지 상태를 가진다
- Promise 생성자가 받는 함수는 resolve와 reject 인수를 전달받는데, 각각 비동기 작업이 성공적으로 완료되었을 때와 작업이 실패했을 때 호출하는 함수이다
- 요청이 성공하면 Promise는 응답을 완료하여 데이털르 반환하고, 실패하면 에러와 함께 거부된다
- 호출자는 반환된 Promise 객체의 .then 및 .catch 메서드를 통해 이러한 요청의 결과를 처리할 수 있다
- 프로미스를 통해 콜백보다 체계적이고 가독성이 높은 방법으로 비동기 작업을 처리할 수 있다

#### 9.3.1 프로미스 체이닝

- 프로미스 체이닝 패턴을 사용해 여러 개의 프로미스를 함께 연결하여 보다 복잡한 비동기 로직을 만들 수 있다

#### 9.3.2 프로미스 에러 처리

- 프로미스 에러 처리 패턴은 catch 메서드를 사용하여 프로미스 체인의 실행 중에 발생할 수 있는 에러를 처리한다

#### 9.3.3 프로미스 병렬 처리

- 프로미스 병렬 처리 패턴은 Promise.all 메서드를 사용하여 여러 프로미스를 동시에 실행할 수 있게 해준다

#### 9.3.4 프로미스 순차 실행

- 프로미스 순차 실행 패턴은 Promise.resolve 메서드를 사용하여 프로미스를 순차적으로 실행할 수 있게 해준다

#### 9.3.5 프로미스 메모이제이션

- 프로미스 메모이제이션 패턴은 캐시를 사용하여 프로미스 함수 호출의 결과 값을 저장한다
- 이를 통해 중복된 요청을 방지한다

```js copy
const cache = new Map();

function memoizedFetchData(url) {
  if (cache.has(url)) {
    return cache.get(url);
  }

  return new Promise((resolve, reject) => {
    fetch(url)
      .then((response) => {
        if (!response.ok) {
          throw new Error(`HTTP error: ${response.status}`);
        }
        return response.json();
      })
      .then((data) => {
        cache.set(url, Promise.resolve(data));
        resolve(data);
      })
      .catch((error) => reject(error));
  });
}
```

#### 9.3.6 프로미스 파이프라인

- 프로미스 파이프라인 패턴은 프로미스와 함수형 프로그래밍 기법을 활용하여 비동기 처리의 파이프라인을 생성한다

#### 9.3.7 프로미스 재시도

- 프로미스 재시도 패턴을 사용하면 프로미스가 실패할 때 다시 시도할 수 있다

#### 9.3.8 프로미스 데코레이터

- 프로미스 데코레이터 패턴은 고차 함수를 사용하여 프로미스에 적용할 수 있는 데코레이터를 생헝한다

```js copy
function makeRequest(url) {
  return new Promise((resolve, reject) => {
    fetch(url)
      .then((response) => response.json())
      .then((data) => resolve(data))
      .catch((error) => reject(error));
  });
}

function logger(fn) {
  return function (...args) {
    console.log("Starting function...");
    return fn(...args).then((result) => {
      console.log("Function completed.");
      return result;
    });
  };
}

const makeRequestWithLogger = logger(makeRequest);

document.getElementById("fetchWithLogger").addEventListener("click", () => {
  makeRequestWithLogger(apiUrl)
    .then((data) => printResult(data))
    .catch((error) => {
      output.innerText = `Error: ${error.message}`;
    });
});
```

#### 9.3.9 프로미스 경쟁

- 프로미스 경쟁 패턴은 여러 프로미스를 동시에 실행하고 가장 먼저 완료되는 프로미스의 결과를 반환한다

```js copy
Promise.race([makeRequest(apiUrl1), makeRequest(apiUrl2)])
  .then((data) => {
    printResult(data);
  })
  .catch((error) => {
    output.innerText = `Error: ${error.message}`;
  });
```

### 9.4 async/await 패턴

- async/await는 프로미스를 기반으로 구축되었으며, 비동기 코드 작업을 보다 쉽고 간결하게 만들어준다
- 함수 내부에서 await 키워드는 fetch 호출이 완료될 때까지 함수 실행을 일시 중지한다

### 9.4.1 비동기 함수 조합

- 비동기 함수 조합 패턴은 여러 비동기 함수를 조합하여 보다 복잡한 비동기 로직을 구성하는 것을 의미한다

```js copy
async function makeRequest(url) {
  const response = await fetch(url);
  const data = await response.json();
  return data;
}

async function processData(data) {
  // Simulating data processing
  await new Promise((resolve) => setTimeout(resolve, 2000));
  const processedData = `Processed data: ${data.title}`;
  return processedData;
}

async function main() {
  try {
    const data = await makeRequest(
      "https://jsonplaceholder.typicode.com/posts/1"
    );
    const processedData = await processData(data);
    output.innerText = processedData;
  } catch (error) {
    output.innerText = `Error: ${error.message}`;
  }
}
```

### 9.4.2 비동기 반복

- 비동기 반복 패턴은 for-await-of 반복문을 사용하여 비동기 반복 가능 객체를 순회할 수 있도록 한다

### 9.4.3 비동기 에러 처리

- 비동기 에러 처리 패턴은 try-catch 블록을 사용하여 비동기 함수 실행 도중 발생할 수 있는 에러를 처리한다

### 9.4.4 비동기 병렬

- 비동기 병렬 패턴은 Promise.all 메서드를 사용하여 여러 비동기 작업을 동시에 실행할 수 있게 한다

### 9.4.5 비동기 순차 실행

- 비동기 순차 실행 패턴은 Promise.resolve 메서드를 사용하여 비동기 작업을 순차적으로 실행할 수 있도록 한다

### 9.4.6 비동기 메모이제이션

- 비동기 메모이제이션 패턴은 캐시를 사용해 비동기 함수 호출 결과를 저장하여 중복 요청을 방지할 수 있다

### 9.4.7 비동기 이벤트 처리

- 비동기 이벤트 처리 패턴은 비동기 함수를 사용하여 이벤트를 처리할 수 있도록 한다

### 9.4.8 async/await 파이프라인

- async/await 파이프라인 패턴은 async/await와 함수형 프로그래밍 기법을 활용하여 비동기 변환 작업들의 파이프라인을 생성한다

### 9.4.9 비동기 재시도

- 비동기 재시도 패턴을 사용하면 비동기 작업이 실패해도 자동으로 재시도할 수 있다

### 9.4.10 async/await 데코레이터

- async/await 데코레이터 패턴은 고차 함수를 사용하여 데코레이터를 생성한다

### async/await를 활용하는 실용적인 예제

- HTTP 요청 보내기
- 파일 시스템에서 파일 읽어오기
- 파일 시스템에 파일 쓰기
- 여러 비동기 함수를 한 번에 실행하기
- 여러 비동기 함수를 순서대로 실행하기
- 함수의 결과를 캐싱하기
- async/await로 이벤트 처리하기
- 비동기 함수 실패 시 자동으로 재시도하기
- async/await 데코레이터 작성하기

<br />

# 10강. 모듈형 자바스크립트 디자인 패턴

- **모듈형**이란 서로 의존성이 낮은 기능들이 모듈로써 저장된 형태를 뜻한다
- 이러한 느슨한 결합은 **의존성**을 제거하여 애플리케이션의 유지보수를 용이하게 만들어준다
- 전통적인 자바스크립트(ES5) 문법을 사용하는 AMD, CommonJS, UMD 세 가지 방식이 있다

## 10.1 스크립트 로더에 대한 참고사항

- 스크립트 로더는 모듈형 자바스크립트를 구현하기 위한 핵심적인 도구였으며, 호환 가능한 스크립트 로더를 사용해야만 모듈형 자바스크립트를 구현할 수 있었다

## 10.2 AMD(Asynchronous Module Definition)

- 모듈과 의존성 모두를 비동기적으로 로드할 수 있도록 설계된 모듈 정의 방식이다
- AMD 형식은 본래 비동기적이면서도 높은 유연성을 가지고 있어, 개발 과정에서 흔히 발생하는 코드와 모듈 간 긴밀한 결합을 줄여주는 등의 장점을 가지고 있다

### 10.2.1 모듈 알아보기
- AMD에서 주목할만한 가장 중요한 두 가지 개념은 모듈 정의를 구현하는 define 메서드와 의존성 로딩을 처리하는 require 메서드로 나뉜다
- define 메서드는 이름이 있는 모듈 혹은 익명 모듈을 정의하는 데 사용된다
``` copy
define(
  module_id, /* 선택 인자 */
  [dependecies], /* 선택 인자 */
  definition function {} /* 모듈이나 객체를 인스턴스화하는 함수 */
)
```
- module_id 인자를 생략하면 이 모듈은 '**익명** 모듈'이라고 한다
- 익명 모듈을 사용할 때, 모듈의 아이덴티티인 DRY(Don't Repeat Yourself) 원칙에 따라, 파일명과 코드의 중복을 최소화할 수 있다
- 익명 모듈은 코드의 재사용성이 높기 때문에, 코드 내용이나 모듈 ID를 변경하지 않고도 다른 영역으로 손쉽게 이동할 수 있다
- dependecies 인자는 우리가 정의하고 있는 모듈에서 필요로 하는 의존성 배열이다
- 세 번째 인자(definition function 또는 factory function)는 모듈을 초기화하기 위해 실행되는 함수이다
- 반면에, require는 일반적으로 최상위 자바스크립트 파일이나 모듈 내에서 의존성을 동적으로 가져오고자 할 때 사용된다

### 10.2.2 AMD 모듈과 jQuery
#### jQuery를 비동기 호환 모듈로 등록하기
- AMD를 사용하면서 jQuery의 버전이 전역 공간에 유출되는 것을 원치 않는다면, jQuery를 사용하는 최상위 모듈 내에서 noConflict를 호출해야 한다
- jQuery는 define.amd.jQuery를 포함하고 있는 로더에만 AMD 로더로 등록한다
- RequireJS와 curl이 그러한 로더의 대표적인 예시이다

#### 어째서 AMD가 모듈형 자바스크립트 작성에 더 좋을까?
- 유연한 모듈 정의 방식에 대한 명확한 제안을 제공한다
- 훨씬 더 구조화되어 있어, 독립적인 모듈과 의존성을 명확하게 선언할 수 있다
- 전역 네임스페이스의 오염을 방지할 수 있다
- AMD는 다른 크로스 도메인, 로컬 환경, 디버깅 등에서 문제가 없으며, 서버 사이드 툴을 사용할 필요도 없다. 대부분의 AMD 로더는 빌드 과정 없이 브라우저에서 모듈을 로딩하는 것을 지원한다
- 여러 모듈을 하나의 파일로 가져오기 위한 '전송' 방식을 제공한다. CommonJS와 같은 다른 방식은 아직 전송 형식에 도입하지 못하고 있다
- 스크립트의 지연 로딩(lazy-load)을 지원한다.

### 10.2.3 AMD에 대한 결론
- 전역 객체의 사용에 대한 걱정을 줄여주고, 변수에 모듈을 할당할 수 있게 해주고, 브라우저 환경의 모듈 작동을 위해 서버 사이드에서의 변환이 따로 필요하지 않으며, 의존성 관리 측면에서 매우 효율적이다
- AMD는 Backbone.js, ember.js 등 구조적 프레임워크를 사용하여 애플리케이션을 체계적으로 개발할 때에도 매우 유용하다

## 10.3 CommonJS
- CommonJS는 서버 사이드에서 모듈을 선언하는 간단한 API를 지정하는 모듈 제안이다
- AMD와는 달리 I/O, 파일 시스템, 프로미스 등 더욱 광범위한 부분을 다룬다

### 10.3.1 CommonJS 시작하기
- 구조적 관점에서 볼 때, CommonJS 모듈은 재사용 가능한 자바스크립트 코드로써 외부 의존 코드에 공개할 특정 객체를 내보낸다
- AMD와 달리 CommonJS는 모듈을 함수로 감싸는 작업이 필요하지 않다(예를 들어 define을 사용하지 않는다)
- CommonJS 모듈은 두 가지 핵심 요소로 구성된다
- exports 변수는 다른 모듈에 내보내고자 하는 객체를 담는다
- require 함수는 다른 모듈에서 내보낸 객체를 가져올 때 사용하는 함수이다

### 10.3.3 Node.js 환경에서의 CommonJS
- Node.js 환경에서는 CommonJS가 기본 형식으로 쓰인다
- CommonJS 모듈은 Node.js에서 사용할 자바스크립트 코드를 패키징하기 위해서 처음 등장했다
- 많은 Node.js 라이브러리 모듈은 CommonJS로 작성되어 있다
- 브라우저 호환을 위해 모든 주요 브라우저에서는 ES 모듈 문법을 지원하며, 리액트나 Vue.js 같은 프레임워크에서 가져오기/내보내기(import/export)를 사용할 수 있다

### 10.3.4 CommonJS는 브라우저 환경에 적합할까?

## 10.4 AMD vs CommonJS: 동상이몽
- AMD는 브라우저 우선 접근 방식을 채택하여 비동기 동작과 간소화된 하위 호환성을 선택한 반면, 파일 I/O에 대한 개념은 없다. 또한 객체, 생성자, JSON 등 다양한 형태의 모듈을 지원하며 브라우저에서 자체적으로 실행된다는 면에서 대단히 유연한 포맷이다
- CommonJS는 서버 우선 접근 방식을 취하며 동기적 작동, 전역 변수와의 독립성 그리고 미래의 서버 환경을 고려한다. 이러한 특징이 의미하는 바는 CommonJS가 언래핑된 모듈을 지원하기 때문에 ES2015+ 표준에 조금 더 가깝게 느껴진다는 것이다. 이를 통해 AMD에서 필수적인 define() 함수를 사용하지 않아도 된다. 다만 CommonJS 모듈은 오직 객체만을 모듈로써 지원한다

### 10.4.1 UMD: 플러그인을 위한 AMD 및 CommonJS 호환 모듈
- 브라우저와 서버 환경에서 모두 작동할 수 있는 모듈을 원하는 개발자에게는 기존 AMD와 CommonJS의 약점을 해결하는 방안이 필요해 UMD(Universal Module Definition) 형식을 만들었다
- UMD 포맷을 만들 때 AMD 사양에서 지원하는 CommonJS 래퍼의 간소화된 방식을 참고했다
- 하지만, 모듈에 의존성 배열이 없고, 정의 함수가 최소한 하나 이상의 매개변수를 가질 때에만 CommonJS 모듈로써 작동한다는 점에 유의해야 한다
- 더 나아가, UMD는 AMD와 CommonJS 모두에서 동작할 수 있는 다양한 패턴을 제공하고자 했다
- 이를 통해 다양한 환경에서 사용할 수 있는 모듈을 개발할 때 겪는 일반적인 호환성 문제를 해결할 수 있을 것이다
- UMD 저장소에는 다양한 모듈 정의 방식을 지원하는 코드들이 포함되어 있다. 브라우저 환경에 최적화된 모듈, 내보내기 기능을 최적의 방법으로 사용하는 모듈, CommonJS 환경에 최적화된 모듈, 그리고 심지어 jQuery 플러그인 정의에 최적화된 모듈까지 찾아볼 수 있다
- UMD는 AMD나 CommonJS를 대체하기 위한 것이 아니라, 오늘날 다양한 환경에서 코드가 동작할 수 있도록 돕는 보조 도구이다

## 10.5 마치며
- 모듈 형식들은 단순히 모듈 패턴만 사용하는 것과 비교하여 여러 장점이 있다
- 대표적으로, 전역 변수 관리의 필요성 감소, 정적/동적 의존성 관리에 대한 향상된 지원, 스크립트 로더와의 높은 호환성, 서버 환경에서의 모듈 호환성 강화 등이 있다
