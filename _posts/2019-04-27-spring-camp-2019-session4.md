---
layout: post
title: "[Spring Camp 2019] 무엇을, 어떻게 테스트할 것인가?"
date: '2019-04-27 10:40:00'
author: Heeye
categories: Spring
tags: Spring
---

## 테스트로 얻을 수 있는 것
안정감과 자신감!😌
현재와 미래의 나! 현재와 미래의 동료를 위해!
<br/>

## 무엇을 테스트할 것인가?
- 로또를 구현하라
  - 6개의 숫자를 반환
  - 중복되지 않은 숫자
  - 랜덤하게 반환

1. 구현 vs 설계
  구현은 언젠가 어떻게든 변할 수 있다.
  그렇기 때문에, 설계를 기준으로 테스트코드를 짜야 한다.
    * 구현 테스트
    public A actA() - void actATest()
    public B actB() - void actBTest()
    private C actC() - ????

    > 구현을 테스트하는 게 아니라, 설계를 테스트해야 한다. (중복되지 않은 숫자도 테스트 해야지!)

2. 테스트 가능한 것, 불가능한 것
  * Non-Testable!
    - Random, Shuffle, LocalDate.now()
    - 외부세계
      - HTTP
      - 외부 저장소

    > 제어할 수 없는 영역은 멱등한 결과를 보장할 수 없다. (랜덤하게 반환)
    > 그럼 ~~랜덤하게 반환~~ 이 아니라, 의도한 전략대로 반환! 이라고 재정의

**항상 성공할 수 있는 것, 항상 동일한 결과가 나올 수 있는 것을 테스트**

<br/>

## 어떻게 테스트할 것인가?

1. 테스트할 수 없는 영역을 어떻게?
테스트할 수 없는 영역이 method call tree 하위에 있을 때, 이를 분리해서 끌어올려야 함(boundary layer까지).
~~현재 시간에 해당하는 할인 금액 합산~~ -> 특정 시간에 해당하는 할인 금액 합산
boundary layer는 한 모듈로서의 의미를 지니는 가장 바깥 쪽!
> 테스트 불가능한 영역을 boundary Layer로 올려서 테스트 가능하도록 변경

2. Java, Spring Framework
- 로또를 웹으로 구현하라! 라는 요구조건으로 업데이트 됐다.
- 웹구현에 SpringBoot를 썼다고 @SpringBootTest 를 꼭 써야할까? spring context가 필요할까?
- Spring Context는 느리다 -> 빠른 피드백을 받을 수 없다.
- ```Java
  @Autowired
  private ShuffleStrategy shuffleStrategy;
  ```
  - Spring Framework에서 이 필드의 의미는? 필수 의존성
  - Java에서 이 필드의 의미는? Nullable
  > Spring Context의 오용은 언어의 본질을 망각할 수 있다.

>Context, Framework 에 의존적이지 않은 테스트를 하자.

3. Test Double
  - 테스트할 수 없는 영역에 대한 외부 요인을 부여할 수 있또록 도와줌. ex) mockito
  - 무엇을 Test Double로 처리해야 할까?
  - Test Double을 사용하는 것은 테스트가 구현을 알아야 한다는 것이다. (테스트 더블이 아마 어떤 고정값을 리턴하게 해서 신경쓰지 않게 해놓는 것 같음)
> Boundary Context까지 끌어올려진 Non-Testable한 것만 Test Double처리


4. Embedded
* 순수 자바 어플리케이션으로는 테스트할 수 없는 것
  - 저장소 입출력 검증
  - SPEC 검증
    - 내부 Controller
    - 외부 API
> Embedded 사용!

테스트 정확도 Local > Embedded
테스트 피드백 속도 Local < Embedded
테스트 안정성 Local < Embedded

5. Endpoint Test 도구 사용하여 내부 API Spec 테스트
* Spring Framework Test
  - MockMvc
  - REST Assured
  - WebTestClient

테스트의 목적은 요청과 응답 스펙 검증만으로 제한하는 게 정신 건강에 좋을 것이라고 생각!
요청 -> @Controller -> @MockBean -> 응답

by 이호진 - 우아한형제들 기술블로그

6. Spring Cloud Contract로 외부 API Spec 테스트


## Tip & Rule
1. 테스트는 상호 독립적으로 작성
  모든 테스트의 순서와 관계를 생각하며 테스트를 작성하기 어려움
  공유되는 자원은 초기화하여 다른 테스트에 영향을 받지 않도록 한다!
  DynamicTest (JUnit5) 로 순서가 있는 테스트에 유용

2. 테스트 안에서 의도와 목적이 모두 드러나도록 작성
어떠한 조건에서 //given
무엇을 수행했을 때 // when
어떤 결과가 // then

3. 테스트 코드로 리팩토링 대상



## 안정감과 자신감!!!!!!

by 배달의민족 권용근
