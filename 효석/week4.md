# Chapter 07. 자바스크립트 디자인 패턴

## 7.17 행위 패턴

- 시스템 내 서로 다른 객체 간의 의사 소통 방식을 개선하고 간소화 하는 방식으로 객체 간의 의사 소통을 돕는 패턴
- 관찰자 패턴 / 중재자 패턴 / 커맨드 패턴

## 7.18 관찰자 패턴

- 한 객체가 변경 될 때 다른 객체들에게 변경 되었음을 알려주는 패턴
- 관찰자와 주체가 있으며 주체의 변경이 발생했을 경우 관찰자에게 변화를 알려준다

### 7.18.1 관찰자 패턴과 발행/구독 패턴의 차이점

- 관찰자와 유사한 패턴이나, 주체와 관찰자 사이에 토픽/이벤트 채널이 있어서 해당 채널이 구독한 객체에게 변경 사항을 전달

### 7.18.2 장점

- 관찰자 패턴에 비해 느슨한 결합을 가져다 주므로 코드의 관리와 재사용성을 높일 수 있음

### 7.8.13 단점

- 관계가 직접적이지 않기 때문에 기능에 문제가 생겨도 장애를 파악하기가 어렵다는 단점이 존재

### 7.18.4 발행/구독 패턴 구현하기

#### 리액트 생태계에서의 관찰자 패턴

- 리액트의 state 는 주체 역항을, 그리고 랜더링은 관찰자 역할을 하는것과 같이 동작

## 7.19 중재자 패턴

- 객체간의 통신을 줄이고 객체들 간의 통신을 중재자를 통해 간접적으로 통신
- 객체는 중재자를 통해 연결 되므로 느슨한 결합을 가질 수 있다
- 모든 상호 작용 및 로직이 중재자에 집중 되므로 문제 발견 및 해결이 쉽다
- 각 객체의 로직이 단순해지고 책임이 명확해진다
- 객체들의 독립성, 캡슐화가 유지 되므로 재사용이 쉽다
- 다만 중재자 객체가 복잡해지는 문제와, 오버 엔지니어링 이슈가 발생

### 발행/구독 또는 이벤트 집합 패턴 vs 중재자 패턴

- 이벤트의 발행과 구독을 중앙에서 관리 vs 객체 간 통신을 하나의 중앙 객체가 담당
- 발행자는 구독자를 알 필요가 없음 vs 중앙에서 모든 객체를 알고 있어야 함
- 결합도가 매우 낮음 vs 객체와 중제자의 결합도는 존재, 객체간 결합도는 없음
- 이벤트 또는 구독자 추가가 쉬워 확장성이 매우 높음 vs 새로운 객체 추가시 중재자 코드 수정 필요
- 이벤트는 발행 후 망각 모델을 사용 vs 객체 간 해동을 조율하고 촉진

```java
// 이벤트 집합 패턴
class EventAggregator {
    constructor() {
        this.events = {};
    }

    on(eventName, callback) {
        if (!this.events[eventName]) {
            this.events[eventName] = [];
        }
        this.events[eventName].push(callback);
    }

    trigger(eventName, data) {
        if (this.events[eventName]) {
            this.events[eventName].forEach(callback => callback(data));
        }
    }
}

// 사용 예
const eventAggregator = new EventAggregator();
eventAggregator.on('userLoggedIn', data => console.log('User logged in:', data));
eventAggregator.trigger('userLoggedIn', { username: 'John' });

// 중재자 패턴
class ChatMediator {
    constructor() {
        this.users = [];
    }

    addUser(user) {
        this.users.push(user);
    }

    sendMessage(message, sender) {
        this.users.forEach(user => {
            if (user !== sender) {
                user.receive(message);
            }
        });
    }
}

class User {
    constructor(name, mediator) {
        this.name = name;
        this.mediator = mediator;
    }

    send(message) {
        console.log(`${this.name} sends: ${message}`);
        this.mediator.sendMessage(message, this);
    }

    receive(message) {
        console.log(`${this.name} receives: ${message}`);
    }
}

// 사용 예
const mediator = new ChatMediator();
const john = new User('John', mediator);
const jane = new User('Jane', mediator);
mediator.addUser(john);
mediator.addUser(jane);
john.send('Hello!');
```

### 7.19.3 이벤트 집합 패턴의 활용

- 구독 관계가 많아지거나 전혀 관련 없는 객체들 간의 소통이 필요할 때 사용

### 7.19.4 중재자 패턴의 활용

- 두 개 이상의 객체가 간접적인 관계를 가지고 있거나, 비즈니스 로직에 따라 상호 작용 및 조정이 필요할 때 사용

### 7.19.5 이벤트 집합 패턴과 중재자 패턴 결합하기

- 중재자를 이벤트 집합의 구독자로 만들기
- 이벤트가 발생하면 구독한 중재자에게 메세지가 전달 되고 이를 중재자가 객체들에게 통신을 하여 처리
- 객체들간의 통신이 없는 구조는 유지하면서 새로운 이벤트가 객체 추가를 간단한 형태로 만들 수 있음
- 비즈니스 로직과 통신 로직의 분리가 가능

### 7.19.6 최신 자바스크립트에서의 중재자 패턴과 미들웨어

### 7.19.7 중재자 패턴 vs 퍼사드 패턴

- 중재자는 양방향 통신이 가능, 퍼사드는 단방향

### 7.20 커맨드 패턴

- 작업을 단이 객체로 캡슐화하여 추후에 실행하도록 하는 패턴
- 객체 간의 결합도를 낮추고, 요청 기록 / 커맨드 취소 등으 장점을 가진다
- 기존 코드를 수정하지 않고 새로운 커맨드를 추가할 수 있다
  - 커맨드 자체를 클래스로 만들어 기존 클래스가 상속하는 개념으로 사용

## 7.21 마치며

# Chapter 08. 자바스크립트 MV\* 패턴

## 8.1 MVC 패턴

- Model / View / Controller

### 8.1.1 Smalltalk-80 의 MVC 패턴

- GUI 도입으로 인해 MVC 패턴이 탄생

## 8.2 자바스크립트의 MVC

- 모던 프론트 프레임워크는 대부분 MVC 패턴을 차용

### 8.2.1 모델

- 모델은 어플리케이션의 데이터를 관리하는 역할

### 8.2.2 뷰

- 사용자와 상호 작용하는 기능

### 8.2.3 템플릿

### 8.2.4 컨트롤러

- 모델과 뷰 사이의 중재자 역할

> [p.189] 여기서 프론트와 백의 MVC 패턴에 대한 차이가 발생하는 듯 합니다. 프론트에서는 결국 View 가 사용자와 상호 작용을 하는 엔드포인트가 되지만 백에서는 결국 백엔드 API 즉 컨트롤러가 상호 작용의 엔드포인트가 되고 이 요청에 대한 결과를 View 로 리턴하는 형태가 되기때문에 명확하게 같다고 보면 안되는 것 같습니다. (프론트는 MVVM 으로 보는게 맞다 봅니다)

> [p.189] 실제 스프링 씬에서는 Controller(요청에 대한 처리) -> Service(요청에 대한 실제 비즈니스 로직) -> Repository(or Mapper, 데이터 베이스와 통신) 레벨로 사용을 한다

## 8.3 MVC 를 사용하는 이유는

- 관심사 분리로인한 장점이 존재
  - 전반적인 유지보수의 단순화
  - 모델과 뷰의 분리
  - 협업에서 가지는 장점

## 8.4 자바스크립트와 Smalltalk-80의 MVC

## 8.6 MVP 패턴

- P 는 프리젠터이다
- View 의 의 사용자 요청을 Presenter 가 모델에 전달하는 형태

## 8.7 MVVM 패턴

- Model View ViewModel 패턴
- MVVM 의 뷰는 능동적 의미를 담고 있다
- ViewModel UI 뒤에서 View가 필요로 하는 데이터를 처리
- ViewModel 은 View 에 독립적이나 View 는 ViewModel 에 종속 된다
- ViewModel 과 View 는 데이터 바인딩 또는 이벤트를 통해 소통

> [p.200] ViewModel 이 존재하지 않던 MVC 패턴에서 View 의 상호 작용이 중요해지자 ViewModel 이 도입된 SPA 프레임워크의 시대로 변환이 되었다가, 결국 SEO 와 속도를 위해 SSR 을 위한 MVC 패턴이 추가 된 것이 현재의 NEXT 가 되겠군요

# 이야기 거리

> Chapter 7

- 패턴의 경우 기존 컴퓨팅의 한계와 객체 지향의 구조 한계를 극복하기 위해 옛날에 나온 것들이 대부분이다 보니 요즘 쓰는 것들에는 그러한 개념들이 기본적으로 녹아 있는 경우가 많은 것 같습니다

> Chapter 8

- [p.189] 여기서 프론트와 백의 MVC 패턴에 대한 차이가 발생하는 듯 합니다. 프론트에서는 결국 View 가 사용자와 상호 작용을 하는 엔드포인트가 되지만 백에서는 결국 백엔드 API 즉 컨트롤러가 상호 작용의 엔드포인트가 되고 이 요청에 대한 결과를 View 로 리턴하는 형태가 되기때문에 명확하게 같다고 보면 안되는 것 같습니다. (프론트는 MVVM 으로 보는게 맞다 봅니다)
- [p.189] 실제 스프링 씬에서는 Controller(요청에 대한 처리) -> Service(요청에 대한 실제 비즈니스 로직) -> Repository(or Mapper, 데이터 베이스와 통신) 레벨로 사용을 한다
- [p.200] ViewModel 이 존재하지 않던 MVC 패턴에서 View 의 상호 작용이 중요해지자 ViewModel 이 도입된 SPA 프레임워크의 시대로 변환이 되었다가, 결국 SEO 와 속도를 위해 SSR 을 위한 MVC 패턴이 추가 된 것이 현재의 NEXT 가 되겠군요

- 요즘 다시 노란 머리를 하시는 분들 + 나시 입는 분들이 많이 보면서 아 정말 세상을 돌고 도는구나 싶었는데, 항상 발전을 지향한다고 생각하는 개발 씬에서도 SSR 을 보면서 비슷한 감정을 느끼게 될 줄은 몰랐습니다
