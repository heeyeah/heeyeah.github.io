---
layout: post
title:  "[Work] Presentation Plann"
date:   '2020-03-24 14:00:00'
author: Heeye
categories: Spring, Work
tags: Work
---

## 스터디 주제

1.  webflux intro :)

-   reactive programming의 간---단한 개념
-   blocking IO와 nonblocking IO의 차이점
-   그래서 MSA에서는 Spring5 reactor 모듈을 사용해야 한다?
    -   장점
        -   익히 나온 장점들 - 성능, MSA에 이상적으로 적합함
    -   단점
        -   어렵다. debugging 쉽지 않다. blocking 구간이 있으면 어차피 똑같다.

2.  non-reactive VS reactive application 성능 테스트

-   단순한 프로젝트 1개로 테스트 (only test)
-   가시적인 그래프나 수치가 나와야 함.

3.  reactive application sample code

-   3개의 어플리케이션, 아키텍쳐 짜기 (DDD ...?) -> 라이센스 그걸로
-   기술셋 사용 명세 작성
-   하나의 프로젝트 당, non-reactive/reactive api 1개 씩

4.  Result...!

-   나아가야할 방향에 대하여
    -   learning curve (java, kotlin..?, spring, springboot, reactive, functional programming, ddd, msa, reactive db, clean code, git...)
    -   reactor module을 적극적으로 사용하지 않는다고 했을 때의 차선책 또는 방안(?)
        -   messaging queue, kafka (?), 아키텍처 설계로 커버하기, infra 빠방하게 하기...?
        -   reactive module에 대한 레퍼런스가 많이 없기 때문에, 다른 걸 사용해서 커버 가능한가를 고려 (그렇다 해도 DB는 분산DB를 써야한다고 알고있나 내가)

## 발표 Plan

4월 1째주 - 2020.04.02 (목) : 1
4월 2째주 - 2020.04.09 (목) : 2
4월 3째주 - 2020.04.16 (목) : 3. Cloud Unit 내부 webflux 샘플코드 시연
4월 4째주 - 2020.04.23 (목) : 4. 부서 단위 webflux 샘플코드 시연

## 패러다임

-   프로세스를 효율화하기 위한 것
    애자일(agile)/폭포수(waterfall) 개발 방법론(methodology) 등
-   프로그래밍 패러다임
    절차 지향 프로그래밍(Procedural Programming)/객체 지향 프로그래밍(Object-Oriented Programming)/함수형 프로그래밍(Functional Programming) 등
-   아키텍처 패러다임
    Monolitic Architecture/SOA(Service-Oriented Architecture)/MSA(Microservice Architecture) 등
    많은 소프트웨어 프로젝트들은 단순한 개발이 아니라 이러한 패러다임 속에서 탄생합니다.

## meetUp

outer architecture 때문에 MSA가 어려워진 것. 원래 method call을 하다가 네트워크로 call을 해버리니까
<https://blogs.gartner.com/gary-olliffe/2015/01/30/microservices-guts-on-the-outside/>

꿀밋업 예전꺼 다시보기
<https://www.facebook.com/pg/vmwaretanzukorea/videos/>

## ppt code sample (1)

```java
    public static void fluxSample() {

        Flux<String> seq1 = Flux.just("hello", "world", ":)");

        List<String> iterable = Arrays.asList("foo", "bar", "foobar");
        Flux<String> seq2 = Flux.fromIterable(iterable);

        seq1.subscribe(); // Subscribe and trigger the sequence.
        seq2.subscribe(i -> System.out.println(i)); //	Do something with each produced value.


        Flux<Integer> ints = Flux.range(1, 4)
                .map(i -> {
                    if (i <= 3) return i;
                    throw new RuntimeException("Got to 4");
                });
        ints.subscribe(i -> System.out.println(i),
                error -> System.err.println("Error: " + error)); //Deal with values but also react to an error.

        // subscribe 하지 않으면,
        Flux<Integer> ints2 = Flux.range(1, 4);
//        ints2.subscribe(i -> System.out.println(i),
//                error -> System.err.println("Error " + error),
//                () -> System.out.println("Done"));
    }


```

\---console------
foo
bar
foobar
1
2
3

## Error: java.lang.RuntimeException: Got to 4
