# Chapter 07. 자바스크립트 디자인 패턴

## 7.7 팩토리 패턴

- 객체 생성의 로직을 클라이언트 코드로 분리하여, 팩토리 클래스가 객체 생성을 전담하는 패턴

### 7.7.1 팩토리 패턴을 사용하면 좋은 상황

- 객체나 컴포넌트 생성 과정이 복잡할 때
- 상황에 맞는 객체 생성이 필요할 때
- 같은 속성을 공유하는 객체 또는 컴포넌트를 다뤄야 할 때
- 덕타이핑 처럼 같은 규칙만 충족하면 되는 객체를 구성하거나 구성 요소간의 의존성을 줄여야 할 때(디커플링)

> [p.108] 확실히 프론트 파트로 넘어와서 객체, 클래스는 컴포넌트에 대응이 되기 시작한 것 같습니다. 그리고 컴포넌트가 본격적으로 사용되기 시작한 모던 프론트 프레임워크 패러다임의 변화 이후에 디자인 패턴을 적용하려는 부분이 생기는 것으로 보이네요. 결국 리액트의 한계를 JS 적으로 풀기에는 한계가 있으니까요

### 7.7.2 팩토리 패턴을 사용하면 안되는 상황

- 어플리케이션의 복잡도가 크게 증가하면 안되는 상황
- 라이브러리 또는 프레임워크 설계가 아닌 경우 피하는게 좋다

### 7.7.3 추상 팩토리 패턴

- 같은 목표를 가진 팩토리들을 하나의 그룹으로 캡슐화하는 패턴

## 7.8 구조 패턴

- 클래스와 객체를 더 큰 구조로 조합하는 방법
- 상속 개념을 통해 인터페이스와 객체를 구성하여 새로운 기능을 추가하는 것 처럼 객체를 체계적으로 구성하는 방법을 제공
- 퍼사드 / 믹스인 / 데코레이터 / 플라이웨이트 패턴

> [p.110] 아 진짜... 책 구조 킹받네요... 이거 안티 패턴 아닌가요!? ㅋㅋㅋㅋㅋㅋㅋ

## 7.9 퍼사드 패턴

- 복잡한 시스템에 대해 간단한 인터페이스를 제공하는 구조적 디자인 패턴
- 홈씨어터의 경우 티비, 음향, 인터넷 등등 다양한 서브 시스템에 의존하지만 사용자는 리모컨이라는 간단한 인터페이스로 이를 이용 가능

### 장점

- 클라이언트 코드가 간단해진다
- 서브시스템의 복잡성을 숨긴다
- 서브시스템 간의 결합도를 낮춘다

## 7.10 믹스인 패턴

- 클래스에 기능을 추가하는 유연한 방법을 제공
- 다중 상속을 제공하지만 그에 따른 복잡성을 줄이는 장점을 가진다
- 특정 기능을 대표하는 믹스인을 만들고, 해당 클래스에 필요한 믹스인을 적용하여 각각의 클래스의 기능을 쉽게 추가하는 개념으로 이해

```java
interface SwimmingMixin {
    default void swim() {
        System.out.println(getName() + " is swimming.");
    }
    String getName();
}

interface FlyingMixin {
    default void fly() {
        System.out.println(getName() + " is flying.");
    }
    String getName();
}

interface SpeakingMixin {
    default void speak(String phrase) {
        System.out.println(getName() + " says: " + phrase);
    }
    String getName();
}

// 기본 클래스
abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

// 믹스인을 사용한 클래스들
class Duck extends Animal implements SwimmingMixin, FlyingMixin, SpeakingMixin {
    public Duck(String name) {
        super(name);
    }
}

class Fish extends Animal implements SwimmingMixin {
    public Fish(String name) {
        super(name);
    }
}
```

### 논쟁 거리

- 믹스인은 결국 메서드와 클래스가 분리되는 상황을 초래하고, 다양한 믹스(Mix) 인(In) 이 발생하게 되어 JS 는 프로토타입의 오염, 다른 언어에서는 메서드 출처의 불확실성을 가져오는 문제를 초래
- 결국 다양한 믹스인의 조합으로 탄생한 클래스 또는 객체는 의존성이 희미해지는 결과를 가지게 되어서 가독성 및 유지 보수의 어려움을 가져옴
- 네임스페이스, 복잡성이 증가하는 문제 발생
- 상속이 중복되면 다이아몬드 문제 야기
  - 다중 상속이 가능한 언어에서 오버라이드 된 메서드를 실행하면 어떤 부모의 메서드를 참조 해야하는지 판단이 안되는 문제
- 리액트의 경우 믹스인 보다는 HOC 등으로 풀 것을 제시
  - 로깅 기능이 필요하면 특정 컴포넌트 내부에 로깅 믹스인을 넣는 것이 아니라, 로깅 HOC 로 감싸는 패턴을 사용
  - HOC 사용 시 사용 방법 및 구조가 동일한 형태를 가질 수 밖에 없음, 대신 믹스인은 컴포넌트가 달라지면 각기 다른 컴포넌트의 수 만큼 믹스인 패턴이 나오는 문제가 발생

> [p.119] 이 부분에 대해서 어찌 생각 하시나요?

## 7.11 서브클래싱

- 상속이랑 같은 개념
- 부모 클래스를 상속 받아 새로운 클래스를 만들 수 있음
- 부모 클래스의 메서드를 그대로 사용(super) 하거나, 오버라이드하여 새로운 클래스를 만들 수 있는 개념

## 7.13 데코레이터 패턴

- 기존 클래스에 동적으로 기능을 추가하기 위해 사용
- 기존 코드 변경 없이 데코레이터 추가로 새로운 기능 추가가 가능
- 각각 데코레이터는 단일 책임 원칙을 따르므로 유지 보수 용이
- 단, 믹스인과 비슷한 형태의 문제를 가질 수 있음

> [p.124] 데코레이터 패턴도 결과적으로는 믹스인과 비슷하다고 생각하는데, 어떻게 생각하시나요?

- 믹스인은 다중 상속의 의미(컴파일 타임)
- 데코레이터는 동적으로 메서드나 책임을 추가(런 타임)

## 7.14 의사 클래스 데코레이터

### 7.14.1 인터페이스

### 7.14.2 추상 데코레이터

### 7.15 장점과 단점

- 장점 : 서브 클래스 없이 다양한 클래스를 생성 가능하다
- 단점 : 복잡성이 증가한다

> [p.130] 어떻게 7.15로 장단점이 나올 수 있는 거죠? 제 뇌가 안티 패턴인가요? ㅋㅋㅋㅋㅋㅋㅋ

## 7.16 플라이웨이트 패턴

- 연관된 객체끼리 데이터를 공유하여 느리고 비효율적으로 데이터 공유를 최적화 하는 패턴
- 데이터를 내재적 상태와 외재적 상태로 나누어 관리
- 내재적 상태는 필수적 데이터를 의미하며, 필수적 데이터만을 남겨 내재적 데이터의 양을 줄일 수 있다
- 외재적 상태는 가급적 단일화 된 관리자(싱글톤)를 사용하여 관리하여 해당 관리자를 중앙 관리자 처럼 사용하는 패턴을 주로 사용한다

### 도서관에 플라이웨이트 패턴 적용

- 책의 필수 정보를 내재적 정보로 관리
- 책의 관리자 BookManager 를 사용하여 책의 외재적 정보(대여 관련 정보들)를 관리
- 매니저는 하나의 싱글톤이며 책의 왜재적 정보를 생성하는 팩토리 패턴이 적용된다
- 하나의 매니저를 통해 관리하고, 정보를 BookDatabase 에 저장을 시킴으로서 각각의 레이어를 분명하게 나눈다
- 책이라는 데이터의 복잡성은 증가되지만 결합도, 의존성이 낮아지고 확장성이 증대 / 또한 성능에 대한 이점도 가지게 된다

> [p.142] AI 의 응답결과 1,000,000 개의 데이터 사용 기준 일반 구현은 12MB 메모리, 플라이웨이트 패턴은 3.8MB 의 데이터를 사용

### 프론트에서의 플라이웨이트 패턴 적용

- 특정 기능이 반복되는 요소의 경우 해당 기능이 필요한 요소에 직접 이벤트 핸들러를 부착하는 것이 아니라 최상위 요소에 이벤트 핸들러를 붙이고, 실제로 이벤트를 이벤트 위임을 통해 사용하는 방법을 사용

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flyweight Event Handling</title>
    <style>
        .item {
            padding: 10px;
            margin: 5px;
            border: 1px solid #ddd;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="itemContainer">
        <div class="item" data-id="1">Item 1</div>
        <div class="item" data-id="2">Item 2</div>
        <div class="item" data-id="3">Item 3</div>
        <!-- 더 많은 아이템들... -->
    </div>

    <script>
        // 플라이웨이트 이벤트 핸들러
        const itemHandler = {
            handleClick: function(event) {
                const target = event.target.closest('.item');
                if (target) {
                    const id = target.getAttribute('data-id');
                    console.log(`Clicked item with id: ${id}`);
                    // 여기에 추가적인 로직 구현
                }
            }
        };

        // 이벤트 리스너 등록
        document.getElementById('itemContainer').addEventListener('click', itemHandler.handleClick);

        // 동적으로 새 아이템 추가하는 함수
        function addNewItem(id) {
            const newItem = document.createElement('div');
            newItem.className = 'item';
            newItem.setAttribute('data-id', id);
            newItem.textContent = `Item ${id}`;
            document.getElementById('itemContainer').appendChild(newItem);
        }

        // 테스트를 위해 몇 개의 아이템 추가
        for (let i = 4; i <= 100; i++) {
            addNewItem(i);
        }
    </script>
</body>
</html>
```

## 이야기거리

- [p.108] 확실히 프론트 파트로 넘어와서 객체, 클래스는 컴포넌트에 대응이 되기 시작한 것 같습니다. 그리고 컴포넌트가 본격적으로 사용되기 시작한 모던 프론트 프레임워크 패러다임의 변화 이후에 디자인 패턴을 적용하려는 부분이 생기는 것으로 보이네요. 결국 리액트의 한계를 JS 적으로 풀기에는 한계가 있으니까요
- [p.119] 믹스인에 대한 논쟁 부분에 대해서 어찌 생각 하시나요?
- [p.124] 데코레이터 패턴도 결과적으로는 믹스인과 비슷하다고 생각하는데, 어떻게 생각하시나요?
- [p.142] 플라이웨이트 패턴이 프론트 프레임워크 기준이면 어디서 사용이 가능할까요?

> 그나저나 책 구조 정말 이게 최선일까요;;;;;;
