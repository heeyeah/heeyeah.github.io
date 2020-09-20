---

title: "[Spring Camp 2019] Monitoring with Actuator"
date: '2019-04-27 10:40:00'
category: seminar
draft: false
---

### Why & How Monitoring
* Why
장애를 예방하거나 원인 파악, 조치를 취하기 위해 모니터링을 해야 함
배포로 인한 상태 확인
성능 개선
장기적인 서비스 상태 분석을 위해서 모니터링을 해야 합니당.

어떻게 모니터링을 할까요? 지표가 필요합니다아!

다양한 도구가 있어요. prometheus, scouter, grafana 등등....

* How
**spring boot를 monitoring할 수 있 것 = Spring Boot Actuator**
Spring Boot 애플리케이션을 제어하고 상태를 확인할 수 있는 도구
spring-boot-starter-actuator를 gradle에 추가하면 됨.

Web Endpoints
http://domain:port/actucator 로 확인 가능

JMX Endpoints
MBeans -> org.springframework.boot.endpoint

#### metirics (endpoint 중에 하나)

JVM Metrics, JDBC Metrics, Web Metrics...

##### metrics Endpoints
RED Method
Three key metrics you should measure - Tom Wilkie
(Request) Rate 요청의 수
(Request) Errors 요청의 비중
(Request) Duration 요청의 응답시간
클라우드, 서버리스 이런 게 늘어나면서 서버를 모니터링하기보단 서비스를 모니터링하게 되는 게 중요해진듯.

##### metrics third-party library
* hystrix?
 Circuit breaker - 장애내성/지연내성


 ### Micrometer
 like slf4j, 추상화된 인터페이스를 제공.

 ### Actutator & Micrometer Demo
 미터 레이즈스트 리 주입받아서 사용
프로젝트에 한번  모니터링 라이브러리 넣어서 해봐야 겠음
