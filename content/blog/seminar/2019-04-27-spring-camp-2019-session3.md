---

title: "[Spring Camp 2019] 자바에서 null을 안전하게 다루는 방법"
date: '2019-04-27 10:40:00'
category: seminar
draft: false
---

### null에 대해

##### null 참조
- 레코드 핸들링 : 객체지향의 시초가 된 논문
- 특별한 값이 없음을 나타내려고 null을 도입했고, 이 값을 사용하려고 할 때 오류를 내도록 설계
- 두 참조값이 null일 때 두 참조는 동일하다고 판단

##### 자바의 null 참조
- 의미가 모호함
  초기화되지 않음. 정의되지 않음. 값이 없음. null값
- 모든 참조의 기본 상태(값?)
- 모든 참조는 null 가능

##### 자바 기본 장치
- 단정문(assertion)
- Method 지원 (isNull, NonNull, req)
- Optional
  - 모든 메소드는 null을 절대 반환하지마라.
  - Optional을 필드, 메서드 매개변수, 집합 자료명에 쓰지마라


### null을 안전하게 다루는 방법
1. API(매개변수, 반환값)에 null을 최대한 쓰지 말아라
  - null로 지나치게 유연한 메서드를 만들지 말고 명시적인 메서드를 만들어라
    메소드 오버라이드를 사용하던가!
  - null 반환하지 않기
    - 반환 값이 꼭 있어야 한다면 null을 반환하지 말고 예외를 던지기
    - 빈 반환값은 빈컬렉션이나 Null 객체 사용
    - 빈값이 있어야한다면 optional 쓰기
2. 사전 조건과 사후 조건을 확인하라: 계약에 의한 설계 (design by contract)
  - 개방폐쇄원칙의 하부개념
  - 에펠? 이라는 언어를 만든 사람인데, 이 언어엔 메소드의 로직 앞뒤에 require와 ensure을 반드시 호출. 이렇게 하면 null이 없을거다!
  - 스프링에선 Assert를 쓰자

3. (상태와 같이) null의 범위를 지역(클래스, 메서드)에 제한하라

4. 초기화를 명확히 하라


### null에 안전하다고 보장해주는 도구
엘비스 연산자(?:)
연쇄적인 null 값 체크를 하는건 Optional을 사용
JSR 305 JSR 308 계열
@Nonnull @nullable <- Checker Framework 사용하기



tony hoore ~~?






slides.app.goo.gl/i94DQ
