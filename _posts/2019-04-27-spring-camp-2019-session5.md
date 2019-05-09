---
layout: post
title: "[Spring Camp 2019] 당신도 할 수 있는 레거시 프로젝트 개선 이야기"
date: '2019-04-27 10:40:00'
author: Heeye
categories: Spring
tags: Spring
---

### 레거시 코드란 무엇일까요?
- 테스트 코드로 커버되지 않으며
- 유지 보수가 되고 있지 않은 코드 (=clean하지 않은 코드)

### 레거시 코드를 다시 보자!
- 오랜 시간 자신의 역할을 실행한 안정적인 코드
- 조직의 축적된 비즈니스 로직의 구현체
- 코드를 개선하고 내 것으로 만들어야 한당!

### 레거시 코드를 어떻게 개선해야 할까요?
- DDD로 바꾸세요 (Domain Driven Development)
- MSA하세요

### 개선해보장!

예로 드는!
* BizOCR Project
  - 영수증, 사업자 등록증 인식

파악해보기
- 모든 로직이 한 Class 안에 있는 구조
- ...디버깅하기 힘든 코드


* Spock Framework 테스트를 해뵴!
* 카나리 release - 하나 배포해보고 괜찮으면 다 배포하는 것

### 어떻게 할 것인가
* 코드 패키지 분리
* 각 기능마다 패키지 분리하고 config,service넣어놓고오

memory 사용량 절약
heap memory커지면 gc가 ....





by 네이버 이경일