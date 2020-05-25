

## 5장. 비즈니스 로직 설계

### 5.1 비즈니스 로직 구성 패턴
+ 비즈니스 로직과 어댑터들로 구성
  - 비즈니스로직
  - 인바운드 어댑터
    - REST API 어댑터 : 외부에서 호출하는 api, 노출시키는 것 --connect--(비즈니스 로직)
    - CommandHandler : 메시지 채널에서 들어온 커맨드 메시지를 받아 비즈니스 로직을 호출하는 인바운드 어댑터 --connect--(비즈니스 로직)
  - 아웃바운드 어댑터
    - DB어댑터
    - 도메인 이벤트 발행 어댑터

"메시지 채널"과 "이벤트"는 다른 건가에 대한 의문점

#### 절차적 트랜잭션 스크립트 패턴
- 동작(behavior)이 구현된 클래스와 상태(state)를 보관하는 클래스가 따로 존재
- 절차적이기 때문에, 비즈니스 로직이 복잡해지면 덩치가 커지고 복잡해짐 => 단순한 어플리케이션에만 사용 권장

#### 객체 지향적 도메인 모델 패턴
- 대부분의 클래스는 동작(behavior)과 상태(state)를 가짐.
- 객체지향 설계 방식의 장점
  - 설계를 이해/관리하기 쉬움
  - 테스트하기 쉬움
  - 객체 지향 설계는 잘 알려진 설계 패턴들이 있어 응용, 확장이 쉬움 (=디자인 패턴)
- 전략 패턴, 템플릿 메서드 패턴을 적용하면 코드를 변경하지 않아도 컴포넌트를 확장할 수 있음 (OOD) => OOD에서 DDD로 나아가야 함

#### 도메인 주도 설계 개요
- DDD는 복잡한 비즈니스 로직을 개발하기 위해 OOD를 개선한 접근 방식
- DDD 방식으로 설계하면 각 서비스는 자체 도메인 모델을 가짐.
- 각 클래스가 도메인 모델에서 수행하는 역할과 클래스의 특징을 정의.
  - 엔터티(entity) : 영속적 신원을 가진 객체. 두 엔터티가 속성 값이 동일해도 엄연히 다른 객체이다. 자바 언어는 클래스에 JPA @Entity 를 붙여 DDD 엔터티를 나타냄.
  - 밸류 객체(value object) : 여러 값을 모아놓은 객체. 속성 값이 동일한 두 밸류 객체는 서로 바꾸어 사용할 수 있다. (예: 통화와 금액으로 구성된 Money 클래스)
  - 팩토리(factory) : 일반 생성자로 직접 만들기에 복잡한 객체 생성 로직이 구현된 객체 또는 메서드. 정적 메서드로 구현.
  - 리포지토리(repository) : 엔터티를 저장하는 DB접근 로직을 캡슐화한 객체
  - 서비스(service) : 엔터티, 밸류 객체에 속하지 않은 비즈니스 로직 구현 객체
  - 애그리거트(aggregate) : !!


### 5.2 도메인 모델 설계: DDD 애그리거트 패턴
애그리거트 : 한 단위로 취급 가능한 경계 내부의 도메인 객체들. 하나의 루트 엔터티와 하나 이상의 기타 엔터티 + 밸류 객체로 구성.
작업은 애그리거트 일부가 아니 전체 애그리거트에 적용하고, 보통 DB에서 통째로 가져오기 때문에 복잡한 지연 로딩 문제를 신경쓸 필요가 없다.
애그리거트를 삭제하면 해당 객체가 DB에서 모두 사라진다.

#### 애그리거트 규칙
1. 애그리거트 루트만 참조하라
2. 애그리거트 간 참조는 반드시 기본키를 사용하라
3. 하나의 트랜잭션으로 하나의 애그리거트를 생성/수정하라

#### 애그리거트 입도(granularity:뭔가를 더 작은 단위로 나타내는 정도)
작으면 작을수록 좋다.


### 5.3 도메인 이벤트 발행
도메인 이벤트는 도메인 모델에서는 **클래스** 로 표현, 대부분 어떤 상태 변경을 나타냄.

####도메인 이벤트
과거 분사형 동사로 명명한 클래스. 이벤트에 의미를 부여하는 프로퍼티가 있는데, 프로퍼티는 원시 값(primitive value) 또는 밸류 객체(value object)이다.
이벤트ID, 타임스탬프 같은 메타데이터와 변경을 일으킨 사용자 신원정보도 존재 (감사-audit용도).

#### 이벤트 구현

``` java

interface DomainEvent {} //자신을 구현한 클래스가 도메인 이벤트임을 알리는 마커 인터페이스!

interface OrderDomainEvent extends DomainEvent {} //Order 애그리거트가 발행한 OrderCreatedEvent의 마커 인터페이스

class OrderCreatedEvent implements OrderDomainEvent {}

interface DomainEventEnvelope<T extends DomainEvent> { //이벤트 객체 및 메타데이터를 조회하는 메서드가 존재. 이 인터페이스는 DomainEvent를 상속한 매개변수화 객체를 받는다.
  String getAggregateId();
  Message getMessage();
  String getAggregateType();
  String getEventId();

  T getEvent();
}

```

#### 이벤트 강화
이벤트 컨슈머가 이벤트를 받아 처리하려면 주문 내역이 필요함. 필요한 정보를 OrderService에서 가져와도 되지만, 이벤트 컨슈머가 서비스를 쿼리해서 애그리거트를 조회하는 것은 오버헤드를 유발.
그래서 컨슈머에 필요한 정보를 이벤트가 갖고 다니는 이벤트 강화(event enrichment) 기법을 적용한다.

**강화된 OrderCreatedEvent**
```java
class OrderCreatedEvent implements OrderDomainEvent {
  private List<OrderLineItem> lineItems;
  private DeliveryInformation deliveryInformation; // 컨슈머가 필요로 하는 데이터
  private PaymentInformation paymentInformation;
  private long restaurantId;
  private String restaurantName;
  ...
}

```

단점 : 컨슈머를 단순화하는 이점이 있지만, 컨슈머 요건이 바뀌면 이벤트 클래스도 함께 바꾸어야 하는 단점이 존재. => 이벤트 클래스의 안정성은 떨어진다.

#### 도메인 이벤트 식별


### 5.4 주방 서비스 비즈니스 로직
### 5.5 주문 서비스 비즈니스 로직
### 5.6 마치며




## 6장. 비즈니스 로직 개발: 이벤트 소싱

## 7장. 마이크로서비스 쿼리 구현

## 8장. 외부 API 패턴
