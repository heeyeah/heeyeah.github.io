---
title: "Optional이란?"
date: '2019-06-19 18:00:00'
category: java
draft: false
---

### 자바에서 null을 안전하게 다루는 방법
Spring Camp 2019에서 들었던 세미나 중 유익했던 세션 중 하나이다. java에서 null은 특별한 값이 없음을 나타내기 위해 설계됐고 이 값을 사용하려고 할 때 오류를 내도록 설계했다. 여기서 null을 다루는 자바의 기본 장치를 소개하는데

- 단정문(assertion)
- method 지원(isNull, nonNull, req)
- Optional
  - 모든 메소드는 null을 절대 반환하지 마라.
  - Optional을 필드, 메서드 매개변수, 집합 자료명에 쓰지마라.

이 항목 중에 Optional Interface에 대한 짧은 소개가 나오고, 사실 Optional 설계는 잘 안된 편이라 문제가 많다고도 연설자가 리뷰를 했었다. Optional이 뭔지를 알아야 잘못됐네 안됐라도 따질 수 있기 때문에 기록해둔다.

<br/>
### 개념

java8에서  `java.util.Optional<T>` 라는 새로운 클래스를 도입했다.

 **Optional은 존재할 수도 있지만 안할수도 있는 객체, 즉 null이 될 수도 있는 객체를 감싸고 있는 일종의 래퍼 클래스** 이다.
 Optional 은 만약에 값이 있으면  isPresent() 는 true를 반환하고  get()  메소드는 값을 반환한다. 추가적인 메소드로  orElse() 와 같이 값이 있는지 없는지 모를 때 쓰는 메소드도 존재한다.

<br/>

### Optional instance 만드는 법
Optional instance는 어떻게 만들까? 면접 질문으로 받았었는데, Optional 클래스를 알고 있었다면 너무 쉬운 질문이었다...

Optional의 instance는 Optional 클래스 내부에 **static method** 3방법으로 생성할 수 있다. 종류는 다음과 같다.

- **static <T> Optional<T> empty()**

empty Optional instance를 반환한다. null을 담고 있는, 비어있는 Optional 객체를 얻어온다.

- **static <T> Optional<T> of(T value)**

non-null value인 특정 값을 가지고 있는 Optional 객체를 반환한다. value가 null이면  NullPointerException 을 던진다.

- **static <T> Optional<T> ofNullable(T value)**

특정 value를 담은 Optional 객체인데 만약 값이 non-null이면 value를 반환하고 그렇지 않으면 empty Optional을 반환한다.  empty()  메소드와  of(value)  메소드가 합쳐진 형태라고 생각하면 된다.

<br/>

### Optional이 담고 있는 객체 접근 방법

Optional 클래스의 instance method를 사용해 접근할 수 있으며

- get()
- orElse(T other)
- orElseGet(Supplier<? extends T> other)
- orElseThrow(Supplier<? extends X> exceptionSupplier)
- etc
등의 메소드가 존재한다.

<br/>
### 참고
[Optional Reference Docs](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)
