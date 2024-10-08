## [Chapter 5] 최신 자바스크립트 문법과 기능

### 애플리케이션 분리의 중요성

- 확장 가능한 자바스크립트 생태계에서 애플리케이션이 모듈형이라는 것은 잘게 분리된 모듈로 구성되었음을 뜻합니다.
- 이렇게 이루어진 느슨한 결합은 의존성을 낮추어 애플리케이션의 유지보수를 용이하게 만듭니다.

### 모듈 가져오기와 내보내기

- 의존성을 가진 모듈을 가져오고 내보낼 수 있습니다.
- .mjs는 모듈 파일과 기존 스크립트(.js)를 구분하기 위해 쓰이는 모듈 전용 확장자입니다. .mjs 확장자를 사용하여 런타임 및 빌드 도구(예: Node.js09, Babel10)에 모듈임을 알릴 수 있습니다
- nomodule 속성은 브라우저에 모듈이 아님을 알려줍니다. 이 속성은 모듈 문법을 사용하지 않는 대체 스크립트에 유용하며 모듈을 지원하지 않는 브라우저에서도 기능이 제대로 작동할 수 있도록 합니다.

### 정적 가져오기, 동적 가져오기

- 정적 가져오기는 메인 코드를 실행하기 전에 모듈을 다운로드하고 실행해야 합니다.
- 지연로딩(lazy-loading) 모듈을 사용하면 필요한 시점에 로드할 수 있습니다.
- import(url)는 요청된 모듈의 네임스페이스 객체에 대한 프로미스(promise) 객체를 반환합니다.

### 사용자 상호작용에 따라 가져오기

- 일부 기능은 사용자가 상호작용할 때만 필요할 수도 있습니다. 채팅 창이나 다이얼로그, 비디오 등이 대표적인 예시입니다. 이런 기능은 페이지 로드 시점에 필요한 게 아니니 사용자가 컴포넌트를 클릭하는 등 상호작용에 따라 로드되는 것이 좋습니다.
- 이벤트 리스너에 import를 사용할 수 있습니다.

### 화면에 보이면 가져오기

- IntersectionObserver API13를 사용하면 컴포넌트가 화면에 보이는지 감지할 수 있고, 이에 따라 모듈을 동적으로 로드할 수도 있습니다.

### 모듈을 사용하면 생기는 이점

- 한 번만 실행된다.
- 자동으로 지연 로드된다. (defer가 필요없다)
- 유지보수와 재사용이 쉽다.
- 네임스페이스를 제공한다.
  - 모듈 내부 변수와 상수는 개별 공간이다.
- 사용하지 않는 코들르 제거한다.
  - 웹팩이나 롤업같은 번들러를 사용해 사용하지 않는 모듈을 자동으로 제거할 수 있다.

### 클래스

- 클래스 멤버는 기본적으로 공개 상태이며, #(해시)를 앞에 붙여 비공개 멤버로 만들 수 있습니다.
- static 키워드를 통해 정적 메서드와 프로퍼티를 정의할 수 있습니다. 정적 멤버는 클래스를 초기화하지 않고도 사용할 수 있습니다. 주로 어떠한 설정이나 캐시 데이터를 보관하기 위해 사용됩니다.

## [Chapter 6] 디자인 패턴의 유형

- 디자인 패턴은 공통 설계 구조의 핵심 요소를 이름 짓고 추상화하여 재사용할 수 있는 객체 지향 설계를 만드는 데 유용한 역할을 합니다. 또한 유용한 클래스와 인스턴스를 제공하여 각각의 역할과 협업, 책임의 분배를 실현합니다.

### 생성 패턴

- 생성 패턴은 주어진 상황에 적합한 객체를 생성하는 방법에 중점을 둡니다.
- 생성 패턴은 이 과정의 복잡성을 제어하여 문제를 해결하는 것을 목표로 합니다.

### 구조 패턴

- 구조패턴은 객체의 구성과 각 객체 간의 관계를 인식하는 방법에 중점을 둡니다.

### 행위 패턴

- 행위 패턴은 시스템 내의 객체 간 커뮤니케이션을 개선하거나 간소화하는 방법에 중점을 둡니다.

## [Chapter 7] 자바스크립트 디자인 패턴

### 생성자 패턴

- 클래스는 새 객체를 초기화하는 constructor()라는 이름의 메서드를 가지고 있어야 합니다.
- 또한 new 키워드는 생성자를 호출할 수 있으며, 생성자 내부에서 사용된 this 키워드는 새로 생성된 해당 객체를 가리킵니다.

- 프로토타입 객체는 함수나 클래스 등 특정 객체의 모든 인스턴스 내에 공통 메서드를 쉽게 정의할 수 있게 합니다.
- 이러한 방식으로 동일한 프로토타입 객체를 사용하는 여러 개의 Car 객체를 만들 수 있습니다.

### 모듈 패턴

- 모듈 패턴은 전통적인 소프트웨어 엔지니어링 분야에서 클래스의 캡슐화를 위해 처음 고안되었습니다.
- 개발자들은 자바스크립트 모듈을 사용하여 객체, 함수, 클래스, 변수 등을 구성하여 다른 파일에 쉽게 내보내거나 가져올 수 있습니다.
- 이를 통해 서로 다른 모듈 간의 클래스 또는 함수명 충돌을 방지할 수 있습니다.
- 모듈을 사용하면 클로저 내부로 스코프가 제한되어 비공개 변수를 만들 수 있습니다.

#### 장점

- 모듈 패턴은 캡슐화 개념보다 객체 지향 프로그래밍 지식을 가진 초보 개발자가 이해하기 쉽습니다.
- 비공개를 지원합니다. 공개되면 안되는 코드를 캡슐화할 수 있습니다. 덕분에 여러 의존성을 동시에 사용할 수 있고 이름의 충돌도 피할 수 있습니다.

#### 단점

- 공개와 비공개 멤버를 서로 다르게 접근해야 합니다.
- 나중에 추가한 메서드에서는 비공개 멤버에 접근할 수 없습니다.

### WeakMap을 사용하는 최신 모듈 패턴

- WeakMap 객체는 약한 참조를 가진 키-값의 쌍으로 이루어진 집합체입니다.
- 키는 객체여야만 하나, 값으로는 뭐든지 넣을 수 있습니다.
- WeakMap 객체는 기본적으로 키가 약하게 유지되는 맵입니다. 그 말인 즉슨 참조되지 않는 키는 가비지 컬렉션의 대상이 된다는 뜻입니다.

### 노출 모듈 패턴

- 모든 함수와 변수를 비공개 스코프에 정의하고, 공개하고 싶은 부분만 포인터를 통해 비공개 요소에 접근할 수 있게 해주는 익명 객체를 반환하는 패턴

#### 장점

- 노출 모듈 패턴을 사용하면 코드의 일관성이 유지됩니다. 또한 모듈의 가장 아래에 위치한 공개 객체를 더 알아보기 쉽게 바꾸어 가독성을 향상시킵니다

#### 단점

- 비공개 함수를 참조하는 공개 함수를 수정할 수 없습니다.
- 노출 모듈 패턴으로 만들어진 모듈은 기존 모듈 패턴보다도 취약할 수 있으므로 사용에 주의해야 합니다.

### 싱글톤 패턴

- 싱글톤(Singleton) 패턴은 클래스의 인스턴스(instance)가 오직 하나만 존재하도록 제한하는 패턴입니다.
- 싱글톤의 특징은 인스턴스에 대한 전역 접근을 허용한다는 것입니다.
- 싱글톤은 유용합니다. 다만 자바스크립트에서 싱글톤이 필요하다는 것은 설계를 다시 생각해 봐야 한다는 신호일 수도 있습니다.

#### 단점

- 싱글톤임을 파악하는 것이 힘들다.
- 테스트하기 힘들다.
- 신중한 조정이 필요하다.

### 프로토타입 패턴

- 이미 존재하는 객체를 복제해 만든 템플릿을 기반으로 새 객체를 생성하는 패턴
- 프로토타입 상속은 클래스처럼 따로 정의되는 것이 아니라, 이미 존재하는 다른 객체를 복제하여 새로운 객체를 만들어냅니다.
- 프로토타입 패턴은 상속을 구현하는 쉬운 방법일 뿐만 아니라 성능에서의 이점도 챙길 수 있습니다. 객체 내에 함수를 정의할 때 복사본이 아닌 참조로 생성되어 모든 자식 객체가 동일한 함수를 가리키게 할 수 있기 때문입니다.

---

### 이야기할 거리

- 솔직히 클래스 관련 문법은 익숙치 않네요. #가 붙으면 비공개이다 / static 키워드 등은 잘 사용하지 않아서 익숙하지 않은 것 같아요. 직접 많이 사용해봐야 제대로 터득할 수 있을 것 같은데 클래스 공부를 따로 좀 해봐야 할지..
- 이미 디자인 패턴을 사용하고 있으면서도 해당 패턴을 인지하지 못하고 있을 수 있다고 했었는데 그 중 하나가 오늘 나왔던 모듈 내용인 것 같다고 생각했습니다.
- 싱글톤 패턴을 읽으면서 아 이게 전역 상태관리랑 비슷한 건가 라고 생각했는데 마지막에 둘이 구분해주는 느낌이네요..! 어떤 차이인지 생각드는게 있으신가요?
