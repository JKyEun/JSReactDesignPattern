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

