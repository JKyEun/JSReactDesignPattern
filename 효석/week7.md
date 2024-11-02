b# Chapter 12. 리액트 디자인 패턴

## 12.4 리액트 Hooks 패턴

### 12.4.1 클래스 컴포넌트

### 12.4.2 구조 변경의 필요성

### 12.4.3 복잡성 증가

### 12.4.4 Hooks

## 12.5 상태 Hook

### 12.5.1 이펙트 Hook

### 12.5.2 커스텀 Hook

- Hook 의 큰 장점은 누구나 Hook 을 만들고 공유가 가능 -> 이미 만들어 놓은 Hook 을 사용하여 편하게 개발이 가능

### 12.5.3 Hook 관련 추가 정보

### 12.5.4 Hook 의 장단점

#### 장점

- 더 적은 코드 라인수
- 복잡한 컴포넌트의 단순화
- 상태 관련 로직 재사용
- UI 에서 분리된 로직 공유

#### 단점

- 사용 규칙 준수 필요 / 올바르게 사용 필요 / 그른 사용에 주의

### 12.5.5 Hooks vs Class

\*\* [p.308] 이제는 사실상 이러한 논의가 굳이 필요한가 정도의 상태가 아닌가 싶습니다. 오히려 클래스를 적용하는 특수 케이스를 잘 고려하는 것이 중요한 듯 싶습니다.

## 12.6 정적 가져오기

- 웹팩으로 번들을 하게 될 경우 하나의 큰 main.bundle.js 라는 파일에 묶이게 되므로, 로딩 시간에 영향을 줄 수 있다
- 동적 가져오기로 어느정도 해결이 가능

## 12.7 동적 가져오기

- Suspense 컴포넌트를 사용하여 동적으로 컴포넌트를 가져오기가 가능해짐
- 초기 번들 로딩 속도를 줄여서 사용자 경험 개선이 가능

### 12.7.1 로더블 컴포넌트

- 리액트 18 부터는 서버 컴포넌트에서도 Suspense 가 사용 가능!
- Next 의 경우 loading.js 등을 사용하여 처리 가능

### 12.7.3 화면에 보이는 순간 가져오기

- react-loadable-visibility / react-lazyload 같은 라이브러리로 사용 가능

\*\* [p.314] 다만 리액트 18 버전 이후로는 아래와 같이 React.lazy() 와 Suspense 의 조합으로 더 편리하게 구현이 가능<br />
\*\* 또한 라이브러리가 제외되어 번들 크기를 감소 시킬 수 있음

```js
import { lazy, Suspense } from 'react';

// 기본 React.lazy를 사용한 코드 스플리팅
const HeavyComponent = lazy(() => import('./HeavyComponent'));

// Intersection Observer를 활용한 뷰포트 진입 감지
function ViewportAwareLoader({ children }) {
  const [isVisible, setIsVisible] = useState(false);
  const containerRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.disconnect();
        }
      },
      { threshold: 0.1 }
    );

    if (containerRef.current) {
      observer.observe(containerRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return <div ref={containerRef}>{isVisible ? children : null}</div>;
}

function App() {
  return (
    <ViewportAwareLoader>
      <Suspense fallback={<div>Loading...</div>}>
        <HeavyComponent />
      </Suspense>
    </ViewportAwareLoader>
  );
}
```

## 12.8 코드 스플리팅

- 적절한 로딩을 구현하기 위해 코드를 최적으로 스플리팅하여 구현할 필요가 있음

### 12.8.1 경로 기반 분할

### 12.8.2 번들 분할

- 초기 페이지 렌더링에 필요한 코드를 분리해서 로딩 시간을 줄이는 것이 필요

## 12.9 PRPL(Push Render Pre-cache Lazy-load) 패턴

- 푸시 : 중요한 리소스를 효율적으로 푸시하여 서버 왕복 횟수를 최소화하고 로딩 시간을 단축
- 렌더링 : 사용자 경험을 개선하기 위해 초기 경로를 빠르게 렌더링
- 사전 캐싱 : 자주 방문하는 경로의 에셋을 백그라운드에 캐싱하여 서버 요청 횟수를 줄이기
- 지연 로딩 : 자주 요청되지 않은 경로나 에셋을 지연 로딩

### HTTP/2 의 등장

- 기존의 HTTP/1.1 에서는 기존 요청이 완료되지 않으면 다른 요청을 보낼 수 없는 문제인 HOL(Head-of-Line) Blocking 이슈가 발생
- HTTP/2 에 들어서는 양방향 스트림을 통해 여러개의 요청 및 응답 프레임이 전달 가능하여, 응답 프레임을 클라이언트에서 재조립하는 방식으로 사용이 가능 -> HOL Blocking 이슈 회피 가능
- 또한, 특정 리소스의 경우 캐시를 사용하여 서버 요청 대신 캐시에서 읽어들이는 방식도 사용 가능

## 12.10 로딩 우선 순위

- Preload 를 통해 브라우저가 특정 리소스를 일찍 요청할 수 있도록 사용 가능

### 12.10.1 SPA 의 Preload

- 초기 렌더링에 사용되는 특정 폰트나 히어로 이미지는 Preload 를 사용하여 빠르게 로딩이 가능

### 12.10.2 Preload + async 기법

- 스크립트를 기다리는 동안 파싱을 멈추기 않기 위해서 async 적용 가능

### 12.10.3 크롬 95+ 버전에서의 Preload

## 12.11 리스트 가상화

- 현재 화면에 보인느 행만 동적으로 렌더링 하는 방법
- react-virtualized 같은 라이브러리로 구현 가능
  - react-virtual, @tanstack/virtual 같은 더 현대화 된 라이브러로 대체 가능, 더 작은 번들 사이즈 + 리액트 18 버전과의 호환성을 보여줌
  - react-window 라는 react-virtualized 의 경량화 버전도 사용 가능

### 12.11.1 윈도잉 / 가성화의 작동 방식

### 12.11.2 List

### 12.11.3 Grid 컴포넌트

### 12.11.4 웹 플랫폼의 발전

- 최신 브라우저는 CSS 의 content-visibility 속성을 지원
- 화면 밖의 콘텐츠를 렌더링 시점까지 지연이 가능

## 12.12 결론

- Preload 는 항상 신중하게 사용을 해야한다
- 상황에 맞게 리소스 로딩 우선순위를 조정을 해야만 적합한 사용자 경험 제공이 가능

## 12.13 마치며

# 이야기 거리

- [p.308] 훅과 클래스에 대한 내용은 논의가 굳이 필요한가 정도의 상태가 아닌가 싶습니다. 오히려 클래스를 적용하는 특수 케이스를 잘 고려하는 것이 중요한 듯 싶습니다.
- [p.314] 다만 리액트 18 버전 이후로는 아래와 같이 React.lazy() 와 Suspense 의 조합으로 더 편리하게 구현이 가능한 것을 보면서 확실히 프론트 씬은 변화가 빠르다는 것을 알 수 있습니다. 물론 백엔드도 빠르게 진화하고 있지만 자바를 사용하는 진영은 변화가 좀 느리지 않나 싶어요
- 전반적으로 이번 주차의 내용은 디자인 패턴이라기 보다는 리액트에서 사용 가능한 라이브러리나 기능을 알려주는 느낌에 가까웠던 것 같습니다
