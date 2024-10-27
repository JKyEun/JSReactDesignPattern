# 11. 네임스페이스 패턴

- 자바스크립트의 네임스페이스
  - 전역 네임스페이스 내에 존재하는 다른 객체나 변수와의 충돌을 방지함에 있어 유용하다.
  - 프로그램의 기능들을 체계적으로 구성하여 코드의 재사용성과 관리의 편의성을 높여준다.

## 11.1 네임스페이스의 기초

- 네임스페이스 패턴의 종류
  - 단일 전역 변수
  - 접두사 네임스페이스
  - 객체 리터럴 표기법
  - 중첩 네임스페이스
  - 즉시 실행 함수 표현식
  - 네임스페이스 주입

## 11.2 단일 전역 변수 패턴

- 하나의 전역 변수를 주요 참조 객체로 사용하는 방식

  ```tsx
  const myUniqueApplication = (() => {
    function myMethod() {
      // 코드
      return;
    }

    return {
      myMethod,
    };
  })();

  myUniqueApplication.myMethod();
  ```

- 단일 전역 변수 패턴의 가장 큰 문제점은 다른 개발자가 같은 이름의 전역 변수를 이미 사용하고 있을 가능성이 있다는 것.

## 11.3 접두사 네임스페이스 패턴

- 고유한 접두사를 선정한 다음에 모든 메서드, 변수, 객체를 이 접두사 뒤에 붙여서 정의한다.
  ```tsx
  const myApplication_propertyA = {};
  const myApplication_propertyB = {};
  function myApplication_myMethod() {
    // ...
  }
  ```
- 접두사 네임 스페이스 패턴은 전역에서 특정 변수와 이름이 겹칠 가능성을 효과적으로 줄이지만 스스로 고유한 이름을 가진 객체도 같은 효과를 낼 수 있다.
- 가장 큰 문제점은 애플리케이션이 커짐에 따라 많은 전역 객체가 생성된다는 점.

## 11.4 객체 리터럴 표기법 패턴

- 객체 리터럴 표기법은 일종의 객체로, 키와 값으로 이뤄진 집합을 가지며, 각각의 키와 값은 콜론으로 구분된다.
- 또한 키 자체가 새로운 네임스페이스가 될 수 있다.

  ```tsx
  const myApplication = {
    // 앞서 살펴봤듯이, 이 객체 리터럴에 대해 쉽게 함수를 정의할 수 있다.
    getInfo() {
      // ...
    },

    // 또한 객체 안에 추가로 객체 네임스페이스를 만들어
    // 필요한 요소들을 담을 수도 있다.
    models: {},
    views: {
      pages: {},
    },
    collections: {},
  };
  ```

- 네임스페이스에 속성을 직접 추가하는 방법도 있다.

  ```tsx
  myApplication.foo = () => "bar';

  myApplication.utils = {
  	tostring(){
  		//...
  	},
  	export(){
  		//...
  	},
  };
  ```

- 객체 리터럴 표기법 패턴은 전역 네임스페이스를 오염시키지 않으면서도 코드와 매개변수를 논리적으로 구성하는 데 도움을 준다.
- 특히 쉽게 읽을 수 있고, 깊은 중첩까지 지원하는 구조를 구현할 때 매우 유용하다.
- 또한, 일반적인 전역 변수들과 달리 동일한 이름의 변수가 있는지 검사하도록 설계되는 경우가 많아 충돌 가능성을 크게 줄여준다.
- 객체 리터럴의 장점은 키 - 값 구조이기에 손쉽게 알아볼 수 있다는 것이다. 덕분에 애플리케이션 내의 서로 다른 로직이나 기능을 쉽게 캡슐화하여 깔끔하게 분리하고, 코드 확장에 있어 든든한 기반을 제공한다.

## 11.5 중첩 네임스페이스 패턴

- 객체 리터럴 패턴을 발전시킨 형태가 바로 중첩 네임스페이스 패턴이다.
- 중첩 네임스페이스 패턴은 같은 이름의 네임스페이스가 존재한다고 하더라도, 하위에 중첩된 네임스페이스까지 정확하게 일치할 가능성은 낮기 때문에 다른 패턴에 비해 충돌 위험이 낮은 편이다.

## 11.6 즉시 실행 함수 표현식 패턴

- 즉시 실행 함수는 정의 직후 바로 실행되는, 이름이 없는 함수이다.
- 자바스크립트에서는 즉시 실행 함수로 정의된 내부의 변수와 함수 모두 외부에서 접근 할 수 없다. 따라서 함수를 호출하는 것만으로도 쉽게 코드의 은닉성을 구현할 수 있다.
- 즉시 실행 함수는 애플리케이션의 로직을 캡슐화하여 전역 네임스페이스로부터 보호하는 데 널리 사용되는 방법이다.

## 11.7 네임 스페이스 주입 패턴

- 네임 스페이스 주입 패턴은 즉시 실행 함수 패턴의 또 다른 변형이다.
- 이 패턴에서는 함수 내에서 this를 네임스페이스의 프록시로 활용하여 특정 네임스페이스에 메서드와 속성을 주입한다.
- 네임스페이스 주입 패턴의 장점은 여러 객체나 네임스페이스에 기능적인 동작을 쉽게 적용할 수 있다는 점. 또한 이후에 확장될 기본 메서드에 적용할 때 유용하다.
- 네임 스페이스 주입 패턴의 단점은, 같은 목적을 달성하는 더 쉽고 효율적인 방법이 존재할 수도 있다는 것.

## 11.8 고급 네임스페이스 패턴

### 11.8.1 중첩 네임스페이스 자동화 패턴

- 중첩 네임스페이스는 코드에 체계적이고 계층적인 구조를 만들어 준다.

  ```tsx
  // 최상위 네임스페이스에 객체 리터렬을 할당합니다
  const myApp = {};

  // 문자열 형식의 네임스페이스튤 파싱하고
  // 자동으로 중첩 네임스페이스를 생성해주는 간편한 함수입니다.
  function extend(ns, ns_string) {
    const parts = ns_string.split(".");
    let parent = ns;
    let pl;

    pl = parts.length;

    for (let i = 6; i < pl; i++) {
      // 프로퍼티가 존재하지 않을 경우에만 생성합니다.
      if (typeof parent[parts[i]] === "undefined") {
        parent[parts[i]] = {};
      }

      parent = parent[parts[i]];
    }
    return parent;
  }
  ```

### 11.8.2 의존성 선언 패턴

- 객체에 대한 로컬 참조가 전체적인 조회 시간을 단축한다.

  ```tsx
  // 중첩된 네입스떼이스에 접근하는 일반적인 방법입니다.
  myApp.utilities.math.fibonacci(25);
  myApp.utilities.math.sin(56);
  myApp.utilities.drawing.plot(98, 50, 60);

  // 로컬 변수에 캐싱한 참조를 사용합니다.
  const utils = myApp.utilities;

  const maths = utils.math;
  const drawing = utils.drawing;

  // 이렇게 하면 네임스페이스에 더 쉽게 접근할 수 밌습니다.
  maths.fibonacci(25);
  maths.sin(56);
  drawing.plot(98, 50, 60);

  // 로컬 변수믈 사용하는 이 방식은 중첩 네입스페이스에
  // 수백, 수천번 호풀이 발생하는 경우에만 성능이 향상됩니다.
  ```

- 로컬 변수를 사용하는 것이 전역 변수를 매번 사용하는 것보다 더 빠르다.
- 또한 후속 작업에서 중첩된 속성이나 하위 네임스페이스에 매번 접근하는 것보다 더 편리하고 성능이 뛰어나다.
- 특히 복잡한 애플리케이션에서는 가독성을 높일 수 있다.

### 11.8.3 심층 객체 확장 패턴

- 객체 리터럴 표기법으로 선언된 네임스페이스는 다른 객체와 쉽게 확장될 수 있다.
- 병합 이후에는 두 네임스페이스의 속성과 함수 모두를 동일한 네임스페이스에서 접근할 수 있다.

  ```tsx
  // 사용 예시
  const myNamespace = myNamespace || {};

  extendObjects(myNamespace, {
    utils: {},
  });

  console.log("test 1", myNamespace);

  extendObjects(myNamespace, {
    hello: {
      world: {
        wave: {
          test() {
            //...
          },
        },
      },
    },
  });

  myNamespace.hello.test1 = "this is a test";
  myNamespace.hello.world.test2 = "this is another test";
  console.log("test 2", myNamespace);

  myNamespace.library = {
    foo() {},
  };

  extendObjects(myNamespace, {
    library: {
      bar() {
        //...
      },
    },
  });

  console.log("test 3", myNamespace);

  const shorterNamespaceAccess = myNamespace.hello.world;
  shorterNamespaceAccess.test3 = "hello again";
  console.log("test 4", myNamespace);
  ```

## 11.9 권장하는 패턴

- 글쓴이는 가능하다면 중첩 네임스페이스 자동화 패턴을 구현해서 적용하겠다고 함.

## 11.10 마치며

- 대규모 자바스크립트 애플리케이션에서는 프로젝트 파일을 잘 구성하면 모듈과 네임스페이스를 보다 효율적으로 관리할 수 있으며, 개발 경험 또한 향상시킬 수 있다.

# 12. 리액트 디자인 패턴

## 12.1 리액트 소개

- 리액트는 인터페이스를 컴포넌트, Props, 상태라는 세 가지 핵심 개념으로 나누어 정리함으로써 복잡하고 까다로운 인터페이스를 쉽게 구축할 수 있다.
- 리액트는 구성에 초점을 맞추고 있기 때문에, 디자인 시스템의 요소들과 완벽하게 연결될 수 있다.
- 따라서 리액트를 효과적으로 활용하려면 모듈화된 사고방식이 필요하다.
- 리액트는 페이지나 뷰를 구성하기 전에 개별 컴포넌트를 먼저 개발하도록 하기에, 각 컴포넌트의 역할과 목적을 확실히 할 수 있고 이러한 과정을 컴포넌트화한다고 한다.

## 12.2 고차 컴포넌트

- 고차 컴포넌트(HOC) 패턴은 여러 컴포넌트에서 동일한 로직을 재사용하는 방법 중 하나이다. 이 패턴을 사용하면 애플리케이션 전체에서 컴포넌트 로직을 재사용할 수 있다.
- 고차 컴포넌트는 다른 컴포넌트를 인자로 받아 새로운 컴포넌트를 반환하는 컴포넌트이다.
- 고차 컴포넌트는 특정 기능을 포함하고 있어, 이 기능을 매개변수로 전달받은 컴포넌트에 적용할 수 있다.
- 그리하여 고차 컴포넌트는 인자로 받은 컴포넌트에 추가 기능을 적용한 새로운 컴포넌트를 반환한다.

  ```tsx
  function withstyles(Component) {
  	return props => {
  		const Style = { padding: '0.2rem', margin: '1rem' }
  		return <Component style={style} {...props}/>
  	}
  }

  const Button = () = <button>Click me!</button>
  const Text = () => <p>Hello World!</p>

  const StyledButton = withStyles(Button)
  const StyledText = withStyles(Text )
  ```

- 유효한 컴포넌트와 API 엔드포인트만 전달되면, 해당 API 엔드포인트에서 가져온 데이터를 전달받은 대상 컴포넌트에 prop으로 전달한다.

  ```tsx
  import React, { useEffect, usestate } from "react";

  export default function withLoader(Element, url) {
  	return (props) => {
  		const[data,setData] = usestate(null);

  		useEffect(() => {
  			async function getData() {
  				const res = await fetch(url);
  				const data = await res.json();
  				setData(data);
  			}

  			getData();
  		}, []);

  		if (!data) {
  			return <div>Loading...</div>;
  		}

  		return<Element {...props} data={data) />;
  	};
  }

  import React from "react";
  import withLoader from "./withLoader";

  function DogImages(props) {
  	return props.data.message.map((dog, index) => (
  		<Img sr⊂={dog} alt="Dog" key={index} />
  	));
  }

  export default withLoader(
  	DogImages,
  	"https$//dog.ceo/api/breed/labrador/images/random/6"
  );
  ```

### 12.2.1 고차 컴포넌트 조합하기

- 여러 고차 컴포넌트를 조합하여 사용할 수도 있다.
  ```tsx
  export default withHover(
    withLoader(DogImages, "https://dog.ceo/api/breed/labrador/images/random/6")
  );
  ```
- 이제 DogImages 엘리먼트는 withHover와 withLoader에서 전달된 모든 props를 가지게 된다.
- 고차 컴포넌트 패턴이 효과적인 경우
  - 애플리케이션 전체에 걸쳐 여러 컴포넌트에 동일한 동작을 적용해야 할 때
  - 추가된 커스텀 로직 없이도 컴포넌트가 독립적으로 작동할 수 있을 때

### 12.2.2 장점

- 고차 컴포넌트 패턴을 사용하면 재사용하고자 하는 로직을 한 곳에 모아 관리할 수 있다.
- 로직을 한 곳에 집중시킴으로써 코드를 DRY하게 유지하고, 효과적으로 관심사를 분리할 수 있다.

### 12.2.3 단점

- 고차 컴포넌트가 대상 컴포넌트에 전달하는 prop의 이름은 충돌을 일으킬 수 있다.

## 12.3 렌더링 Props 패턴

- 고차 컴포넌트에서 살펴보았듯이, 여러 컴포넌트가 동일한 데이터에 접근하거나 동일한 로직을 포함해야 할 때 컴포넌트 로직을 재사용하면 개발이 편리해진다.
- 렌더링 prop은 JSX 요소를 반환하는 함수 값을 가지는 컴포넌트의 prop이다.
- 컴포넌트 자체는 렌더링 prop 외에는 아무것도 렌더링하지 않는다. 자신의 렌더링 로직을 구현하는 대신, 렌더링 prop을 호출한다.
- 렌더링 props의 장점은 prop을 받는 컴포넌트를 재사용할 수 있다는 것. 한 컴포넌트를 여러 번 사용하면서 매번 다른 값을 렌더링 prop에 전달할 수 있다.
- 렌더링 props라고 불리지만, 렌더링 prop의 이름이 반드시 render일 필요는 없다. JSX를 렌더링하는 모든 prop은 렌더링 prop으로 간주된다.
- 렌더링 prop을 받는 컴포넌트는 보통 렌더링 prop을 호출하는 것 이상의 역할을 한다. 일반적으로는 렌더링 prop을 받는 컴포넌트에서 가져온 데이터를, 렌더링 prop으로 전달된 요소에 전달한다.

### 12.3.1 상태 끌어올리기

- 입력 컴포넌트가 자신의 상태를 다른 컴포넌트와 공유하려면, 상태를 필요로 하는 컴포넌트와 가장 가까운 공통 조상 컴포넌트로 끌어올려야 한다. 이것이 바로 상태 끌어올리기이다.
- 작은 규모의 애플리케이션에서는 리덕스나 Context API와 같은 전역 상태 관리 라이브러리 대신 상태 끌어올리기를 사용하여 상태를 가장 가까운 공통 조상 컴포넌트로 끌어올리는 것만으로도 충분하다.
- 하지만 많은 자식 컴포넌트를 처리하는 큰 규모의 애플리케이션에서는 상태 끌어올리기가 복잡해질 수 있다.
- 각 상태 변경 시 데이터를 사용하지 않는 자식 컴포넌트까지 모두 리렌더링될 수 있어, 성능에 악영향을 줄 수 있다. 그러나 렌더링 Props 패턴을 사용하면 이러한 문제를 해결할 수 있다.

### 12.3.2 컴포넌트의 자식으로 함수 전달하기

- 일반적인 JSX 컴포넌트 외에도, 컴포넌트의 자식으로 함수를 전달할 수 있다.
- 이 함수는 children prop을 통해 접근할 수 있으며, 엄밀히 말허면 렌더링 Props 패턴이기도 하다.

### 12.3.3 장점

- 렌더링 Props 패턴을 사용하면 여러 컴포넌트 사이에서 로직과 데이터를 쉽게 공유할 수 있다.
- render 또는 children prop을 활용하여 컴포넌트의 재사용성을 높일 수 있다.
- 렌더링 Props 패턴은 고차 컴포넌트 패턴에서 발생할 수 있는 이름 충돌 문제를 해결한다.
- 요소에 전달해야 할 props는 모두 렌더링 prop의 인자 목록에 명확하게 나타나므로, 어떤 props가 어디에서 오는지 정확하게 파악할 수 있다. 또한 렌더링 prop을 통해 애플리케이션 로직과 렌더링 컴포넌트를 분리할 수 있다.

### 12.3.4 단점

- 리액트 Hooks는 렌더링 Props 패턴으로 해결할 수 있는 문제 대부분을 이미 해결했기 때문에 대부분의 경우 렌더링 Props 패턴을 대체해 사용할 수 있다.
- 그리고 렌더링 prop에는 라이프사이클 관련 메서드를 추가할 수 없으므로, 받은 데이터를 변경할 필요가 없는 렌더링에 치중한 컴포넌트에만 사용할 수 있다.

- 이야기 해볼 내용
  - 중첩 네임스페이스 자동화 패턴 사용해본적 있으신지?
  - 렌더링 Props 패턴의 단점은 그냥 Hooks 쓰면 다 해결되니까 그냥 Hooks 써라
