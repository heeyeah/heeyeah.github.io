---
layout: post
title: "[Spring Camp 2019] 실전에 써먹는 스프링 부트"
date: '2019-04-27 10:40:00'
author: Heeye
categories: Spring
tags: Spring
---

##Spring Boot란 무엇인가
스프링 프레임워크를 기반으로 한 개발 플랫폼


### 구성요소
- 빌드도구(gradle or maven)
- 스프링 부트 (스프링 프레임워크 기반)
- 프링 부트 스타터

Build Deploy in Coodeee

### Build
Gradle
kotlin DSL? groovy DSL?
Gradle Kotlin DSL - 4가지 강점
1. 빠른 문서보기 가능
2. 코드 자동완성
3. 에러 강조표시
4. 코드 리팩토링
 - 대신 단점은 한번 compile한 후

Springboot gradle plugin
 1. 의존성 관리
 2. 실행가능한 아카이브 패키징
 3. 애플리케이션 배포
 4. 애플리케이션 실행 (boot run)
 5. 액츄에이터 지원???
 - 원래 의존성 버전을 명시해야 하는데 gradle script에! 하지만 버전을 명시하지 않아도 이제 스프링부트가 알아서 해줌. (bom이 들어오고 부터)

 ### Code
 Domain : @Entity, @Repository
 Service : @Service, @Component
 Controller : @Controller, @RestController

 profile을 사용해서 모듈이나 운영환경에서 활성화/비활성화할지 구분할 수 있음
 * 모듈(==기능)
 - auth, message, redis
 * 운영환경
 - local, test, develop, beta(or stage), prod
redis를 예로들면, user나 db정보를 dev나 prod 다르게 설정해서 테스트! 이런거

```yaml
#default
io:
  honeymon:
    age: 20
---
spring:
  profiles: beta
io:
  honeymon:
    age: 30
---
spring:
  profiles: develop
io:
  honeymon:
    age: 33
```

#### 어노테이션 프로세스
@ConfigurationProperties 사용해서 외부 주입해서 jar 실행 시키는 것 가능
gradle script에 kapt? 추가해줘야 함 (annotation 관련)

$ ```java -jar aaaa.jar --io.honeymon.age=20 --server.port=9092```
> 이런식으로 가능

#### 애플리케이션 구성 파일
 application.yml or application.properties



### Deploy
- 빌드도구 동작 재정의
1.x: bootRepackage
2.x: bootJar, bootWar

- 배포방식
전통적 배포방식: war
자기완비적 실행가능한 배포방식: bootJar, bootWar
- 메이븐 deploy 플러그인을 통한 라이브러리 배포
- 애플리케이션 실행: bootRun

#### Spring Actuator
접근방식 : http or JMX (Java management eXtension)
Endpoints : 애플리케이션 종단점 제공
Metrics: 다양한 모니터링 시스템 지원
Audit: 애플리케이션 이벤트 감시
