## Ch 13. 렌더링 패턴

- 렌더링 패턴은 주어진 사용 사례에 맞는 최적의 콘텐츠 렌더링 방식을 제공함
- 렌더링 패턴
    - CSR: HTML이 클라이언트에서 완전히 렌더링
    - SSR: HTML 콘텐츠를 서버에서 동적으로 렌더링한 후, 클라이언트에서 하이드레이션
    - SR: 빌드 타임에 서버에서 페이지를 렌더링하여 정적인 사이트를 구축
    - ISR: 초기 빌드 이후에도 정적 사이트를 동적으로 추가하거나 수정할 수 있음
    - Streaming SSR: 서버에서 렌더링된 콘텐츠를 더 작은 스트림 조각으로 분할하여 전송
    - Edge Rendering: 렌더링된 HTML을 클라이언트에 전송하기 전에 엣지에서 수정
    - Hybrid Rendering: 빌드 타임, 서버 및 클라이언트 렌더링을 결합하여 웹 개발에 더 유연한 접근 방식을 제공
    - Partial Hydration: 클라이언트에서 일부 컴포넌트만 하이드레이션 (e.g 서버 컴포넌트)
    - Progressive Hydration: 클라이언트에서 컴포넌트 하이드레이션 순서를 제어
    - Islands Architecture: 정적인 사이트 내에 여러 진입점을 가진 동적인 동작의 격리된 영역을 만듦
    - Progressive Enhancement: 자바스크립트 없이도 애플리케이션이 작동하도록 보장
- 주요 질문
    - 콘텐츠를 어디에서 어떻게 렌더링할 것인가?
    - 웹 서버, 빌드 서버, 엣지 네트워크 또는 클라이언트, 어디에서 콘텐츠를 렌더링할 것인가?
    - 콘텐츠를 한 번에, 부분적으로, 또는 점진적으로 어떻게 렌더링할 것인가?

### 13.1 렌더링 패턴의 중요성

- 핵심 웹 지표
    - TTFB
    - FCP
    - TTI
    - LCP
    - CLS
    - FID
- 성공적인 개발 원칙
    - 빠른 빌드 시간
    - 낮은 서버 비용
    - 동적 콘텐츠
    - 쉬운 롤백
    - 안정적인 가동 시간
    - 확장 가능한 인프라

### 13.2 CSR

- 렌더링 기법은 두 가지 사이에서 균형을 찾는 걸 목표로 함
    - CSR 애플리케이션 수준의 상호작용성
    - SSR 애플리케이션 수준의 SEO 및 성능상의 이점

### 13.3 SSR

- SSR은 하이드레이션 과정을 최적화하는 과정이기도 함

### 13.4 정적 렌더링

- 순수 정적 렌더링은 동적 데이터를 포함하지 않음
- 다만 여러 변형이 있을 수 있음
    - 데이터베이스의 동적 데이터를 활용한 리스트 페이지 정적 생성
    - 동적 경로를 사용한 상세 페이지 정적 생성
    - 클라이언트 사이드 데이터 fetching을 통한 정적 렌더링
- 특징
    - HTML은 빌드 시점에 생성
    - CDN이나 Vercel의 엣지 네트워크를 통해 쉽게 캐싱 가능
    - 순수 정적 렌더링은 요청 기반 데이터가 필요하지 않은 페이지에 가장 적합
    - 클라이언트 사이드 데이터 fetching을 통한 정적 렌더링은 매 페이지 로드 시 새로고침되어야 하고 안정적인 placeholder 컴포넌트에 포함된 데이터가 있는 페이지에 가장 적합

### 13.4.1 점진적 정적 생성(ISR)

- 정적 렌더링 + SSR을 결합한 방법
- 특정 정적 페이지만 미리 렌더링하고 동적 페이지는 사용자 요청 시에 on-demand 방식으로 렌더링
- 두 케이스에서 동작함
    - 새로운 페이지 추가 허용
    - 기존 페이지 업데이트
        - Stale-While-Revalidate 전략으로 동작(재검증이 진행되는 동안 사용자는 캐시된 또는 이전 버전을 보게 됨)

### 13.4.2 On-demand ISR

- 일반 ISR과 달리 정해진 시간 간격이 아니라 특정 이벤트 발생 시에 페이지가 재생성
- 일반 ISR에서는 업데이트된 페이지가 해당 페이지에 대한 사용자 요청을 처리한 엣지 네트워크 노트에만 캐시됨. On-demand ISR은 엣지 네트워크 전체에 페이지를 다시 생성하고 재분배하므로, 자동으로 전 세계 사용자가 캐시된 오래된 콘텐츠가 아닌 최신 버전의 페이지를 볼 수 있음

### 13.4.3 정적 렌더링 요약

- 순수 정적 렌더링
    - 동적인 데이터가 포함되지 않은 페이지에 적합
- 클라이언트 사이드 데이터 fetching을 통한 정적 렌더링
    - 매 페이지 로드 시 데이터가 새로고침되어야 하고 안정적인 placeholder 컴포넌트를 가진 페이지에 적합
- ISR
    - 특정 간격 또는 필요에 따라 재생성되어야 하는 페이지에 적합
- On-demand ISR
    - 특정 이벤트 발생 시 재생성되어야 하는 페이지에 적합

### 13.5 스트리밍 SSR

- 현재 페이지에 필요한 마크업을 모두 담은 큰 HTML 파일 하나를 생성하는 게 아니라, 작은 조각으로 나눠서 전송
- 네트워크 정체 현상에 효과적

### 13.6 엣지 SSR

- 엣지 SSR은 CDN의 모든 지역에서 서버 렌더링을 가능하게 하고, 콜드 부트 시간을 0에 가깝게 줄여줌
- e.g) 사용자별로 지역 특화 리스트 페이지 구축

### 13.7 하이브리드 렌더링

- 여러 렌더링 방식을 결합

### 13.8 점진적 하이드레이션

- 각 노드를 시간에 따라 개별적으로 하이드레이션하여 필요한 최소한의 자바스크립트만 요청하는 방식
- 점진적 하이드레이션을 하려면?
    - 모든 컴포넌트에 SSR 사용 가능
    - 개별 컴포넌트 또는 조각 단위로 코드 스플리팅 지원
    - 개발자가 정의한 순서대로 클라이언트 사이드에서 각 조각 별 하이드레이션 지원
    - 이미 하이드레이션된 조각에서 사용자 입력 가능 상태 유지
    - 지연된 하이드레이션이 적용되는 조각에 로딩 중임을 표시 가능
- 리액트의 동시성 모드로 위 내용 춫ㅇ족 가능 ⇒ 여러 작업을 동시에 처리하면서도 우선순위에 따라 작업 간 전환을 가능하게 함.

### 13.9 아일랜드 아키텍처

- 정적인 HTML 위에 독립적으로 전달될 수 있는 상호작용 아일랜드를 통해 자바스크립트의 전송량을 줄이는 패러다임
- 컴포넌트 기반 아키텍처로 정적, 동적 아일랜드로 구분된 페이지 뷰를 제안
- 점진적 하이드레이션과 차이점
    - 점진적 하이드레이션: 페이지의 하이드레이션 구조가 하향식
    - 아일랜드 아키텍처: 각 컴포넌트가 자체적으로 하이드레이션 스크립트를 가지고 있으며, 스크립트는 페이지의 다른 스크립트와 독립적으로 비동기 실행됨

### 13.9.1 아일랜드 아키텍처 구현하기

### 13.9.2 아일랜드 아키텍처의 장단점

- 성능, SEO, 중요 콘텐츠 우선순위, 접근성, 컴포넌트 기반(재사용성과 유지보수 용이성)의 이점 있음
- 보편적이지 않고 상호작용 위주의 페이지에는 부적합

### 13.10 리액트 서버 컴포넌트

- 서버에서 실행되도록 설계된 상태를 가지지 않는 리액트 컴포넌트
- 서버 컴포넌트에서 데이터를 가져오는 방법으로 async/await을 사용하여 데이터 fetching을 컴포넌트 트리의 필수적인 부분으로 통합하고, 최상위 레벨에서 await을 사용해 서버 사이드 데이터 직렬화를 가능하게 함
- 컴포넌트는 데이터를 정기적으로 가져올 수 있으며, 새로운 데이터가 있을 때 다시 렌더링되는 컴포넌트를 가진 애플리케이션을 서버에서 실행할 수 있어 클라이언트로 전송해야 하는 코드 양을 줄일 수 있음
- 서버는 클라이언트가 컴포넌트 트리의 일부를 요청할 수 있는 특별한 엔드포인트를 제공하여 MPA와 유사한 아키텍처로 SPA와 같은 라우팅을 가능하게 함 → 상태의 변경 없이 서버 컴포넌트 트리와 클라이언트 사이드 트리를 병합

### 13.10.1 RSC과 Next App router를 활용한 하이브리드 렌더링

### 13.11 마치며

## Ch 14. 리액트 애플리케이션 구조

### 14.1 소개

- 리액트 앱에서 파일 그룹핑 방식
    - 기능별 그룹화: 각 애플리케이션 모듈, 기능 또는 경로별로 폴더를 만듦
- 파일 유형별 그룹화
    - CSS, Js, Image 등 파일 유형별로

### 14.1.1 모듈, 기능 또는 경로별 그룹화

- 파일 구조는 비즈니스 모델이나 애플리케이션의 흐름을 반영함

```jsx
common/
	Avatar.js
	Avatar.css
	ErrorUitls.js
	ErrorUtils.test.js
product/
	index.js
	product.css
	price.js
	product.test.js
checkout/
	index.js
	checkout.css
	checkout.test.js
```

- 모듈에 변경사항이 있을 때 관련된 모든 파일이 같은 폴더에 모여 있어서 변경사항이 특정 코드 영역으로 제한됨
- 모듈 간에 공통적으로 사용되는 컴포넌트, 로직 또는 스타일을 주기적으로 파악해야만 중복을 피하고 일관성과 재사용성을 높일 수 있음

### 14.1.2 파일 유형별 그룹화

```jsx
css/
	global.css
	checkout.css
	product.css
lib/
	date.js
	currency.js
	gtm.js
pages/
	product.js
	productlist.js
	checkout.js
```

- 장점
    - 표준 구조 재사용: 여러 프로젝트에서 동일하게 사용할 수 있는 표준적인 구조를 가짐
    - 새로운 팀원의 빠른 적응
    - 공통 컴포넌트 및 스타일 변경 용이
- 단점
    - 모듈 수정 시 여러 폴더 수정 필요
    - 파일 찾기 어려움

### 14.1.3 도메인 및 공통 컴포넌트 기반의 혼합 그룹화

```jsx
css/
	global.css
components/
	User/
		profile.js
		profile.test.js
		avatar.js
	date.js
	currency.js
	gtm.js
	errorUtils.js
domain/
	product/
		product.js
		product.css
		product.test.js
	checkout/
		checkout.js
		checkout.css
		checkout.test.js
```

### 14.2 최신 리액트 기능을 위한 애플리케이션 구조

### 14.2.1 리덕스

- 특정 기능에 대한 로직을 한 곳에 모아두는 것을 강력히 권장

### 14.2.2 컨테이너

```jsx
/src
	/compoennts
		/compoennt1
			index.js
			styled.js
		/containers
			/container1
```

### 14.2.3 Hooks

```jsx
/components
	/productList
		index.js
		test.js
		style.css
		hook.js
	/hooks
		/useClickOutside
			index.js
		/useData
			index.js
```

### 14.2.4 Styled components

### 14.3 기타 모범 사례

### 14.4 Next 애플리케이션을 위한 애플리케이션 구조

### 14.5 마치며

## Ch 15. 결론

## 이야기거리

- Q. 좋은 폴더 구조를 짜려면 다른 프로젝트를 많이 보라는 이야길 들었는데요. 혹시 추천해주실 만한 레포가 있을까요 ㅎ