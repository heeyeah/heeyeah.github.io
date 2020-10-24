---
title: 'using webflux 1. 개발환경 셋팅 (라이브러리 등)'
date: 2020-09-21 00:00:00
category: spring
draft: false
---

## 0. START
spring webflux를 활용하여 RestAPI를 만드는 과정을 기록하고자 한다.


## 1. 개발환경 구성

개발환경은 아래와 같습니다.
- mac / intellij
- maven build
- java 1.8


### 프로젝트 생성

인텔리제이는 익숙한 툴은 아니지만, 익숙해지려고 많이 사용하는 중이다. 😊

`File > New > Project..` 에서 Java8과 Maven만 선택 후 기본 프로젝트를 생성했다. maven의 dependency가 정의되는 `pom.xml`에는 필요한 라이브러리를 넣어서 빌드했다. 아래는 `pom.xml`에서 dependency 부분만 기재했다!

``` xml

<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.3.2.RELEASE</version>
  <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>
</dependencies>
```


## 2. 도메인

스타벅스 커피 주문하기를 도메인으로 잡아도 되긴 하지만, 기왕하는 거 재밌는 걸로 하고 싶어서 정한 건 `특정 지역을 기준으로 은행을 검색했을 때 주변 은행 정보`이다.

특정 지역이면 `집`이나 `회사` 등, 특정해서 반경 어느 정도 범위 안에 있는 은행 목록들을 출력해주는 것!
카카오 개발자 API를 예-전에 한번 써본 적이 있는데, 재밌었던 경험이어서 다시 끄집어내서 해보려고 한다😎


## 3. blah

기록해놔야겠다고 마음 먹었으나 ~~귀찮아서~~ 얼마나 해놓을 진 모르겠다.
