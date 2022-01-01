---
title: '외부WAS와 Springboot의 내장 톰캣'
date: 2021-08-06 22:00:00
category: java
draft: false
---

## 외장WAS(like apache)와 내장WAS(like embedded tomcat)

tomcat은 WAS라고 불리긴 하지만 정확히 말하면 servlet container이다.
servlet container라는건 java의 servlet 스펙을 만족하는 서버라는 뜻이다.
java는 J2EE(Enterprise Edition), J2SE(Standard Edition), J2ME(Micro Edition) 총 3개의 스펙으로 나눠져 있고, J2EE는 servlet, jsp 등을 포함하여 그 외의 기업용 기술을 포함한 스펙이다.

엄밀히 말해 tomcat은 J2SE 스펙을 만족하는 컨테이너인 것이다.

springboot의 embedded tomcat은, 하나의 서블릿 컨테이너에 하나의 어플리케이션만 들어간다. 톰캣의 컨셉에 virtual host는 없다. 만약에 tomcat 인스턴스를 다양한 도메인으로 띄우고 싶다면, 내장 컨테이너가 없는 WAR를 빌드해서 외장 WAS에 배포하는게 최선일 것이다!
