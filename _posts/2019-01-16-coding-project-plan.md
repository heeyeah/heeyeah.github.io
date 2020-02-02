---
layout: post
title:  "[coding-page프로젝트] toy project 만들기 계획"
date:   '2019-01-16 2:27:00'
author: Heeye
categories: ToyProject
tags: plan
---

### 토이 프로젝트를 만들고 싶다🤔
toy project를 만들어보려고 한다. 작년에 과제 형식으로 단기간에 만들어 본 프로젝트가 있었는데, 관리를 안했더니 다 까먹고 실행도 잘 되지 않고 정리도 안돼서 다시 시작!😀

내 계획은 이렇다.
- spring boot + gradle로 server 구성
- vue 사용 ( bootstrap + vue 사용할 예정 - vue를 쓰는 건, 단순히 작년에 vue로 만들어봐서 )

정말 간단한 계획이고, 하나씩 추가하면서 포스팅할 예정이다.


++ 오늘은 여기까지 완료하고 잔다😴

spring boot starter project ( with gradle) 로 backend 프로젝트 생성

Test Controller 하나 생성 후, run server 확인 + github에 repo init (https://github.com/heeyeah/coding-page)


### frontend 구성하기

frontend 구성을 해보자.
springboot로 backend 환경 구성을 했는데, frontend는 bootstrap + vue를 사용할 예정이다.
빠르게 구성하기 위해 블로그를 참고해서 만들었는데, 간단한 과정은 이렇다.
vue는 react나 angular에 비해 러닝커브가 낮아 쉽게 접근할 수 있다고 해서 선택했다.

2018년 8월에 vue-cli 3.0이 정식 릴리즈 됐다 해서 vue cli 3 installation Guide문서를 보고 따라했다.

문서에 나와 있는 과정을 요약하자면, 정말 간단하다.

```npm install -g @vue/cli```

3.0에서 package 이름이 변경됐는데 2.x 대는 vue-cli이었고, 3.x대부터 @vue/cli 라고 한다! ( 혹시 모르니 이전 버전이 설치되어 있다면 npm uninstall vue-cli -g 해줄 것)

```vue --version```으로 확인가능하다.


vue/cli 3.0이 설치 완료됐으면, ```vue create my-project-name```
my-project-name 자리에 원하는 프로젝트 이름을 입력하고, 엔터만 치면 끝이다!

설치가 끝나면 ```npm run serve``` 해보세요! 라고 안내한다.

![vue메인](https://heeyeah.github.io/imgs/vue_main.png)

localhost:8080으로 접속하면 위와 같은 화면이 나온다😁

spring 환경이랑 vue 환경도 기본적으로 셋팅해 놨으니, 이제 간단한 메인 화면부터 만들 예정이다.
내가 css까지 다 할 수 없으니, bootstrap 컴포넌트를 가져와서 사용할 예정이다.


[참고]

https://cli.vuejs.org/guide/installation.html

https://vuejsexamples.com
