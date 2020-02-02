---
layout: post
title:  "[Docker] Docker Get Started 따라하기 - 2"
date:   '2019-01-18 12:00:00'
author: Heeye
categories: Docker
tags: docker dockerfile
---

Docker 방식으로 app을 build하려면, 제일 밑부터 쌓아야 하는데 그게 Container이다. Container > Service > Stack 순으로 밑부터 쌓여 올라간다라고 생각하면 될 것 같다.

### Your new development environment
기존에 개발 환경에서 Python 앱을 실행시키고 싶다 하면 먼저 파이썬 런타임을 설치해야한다. 환경구성도 맞춰야하고 OS가 뭔지에 따라 필요한 것도 다 달라서 맞춰줘야 하는 것이지. 하지만 도커는 뭐 설치할 필요없이 파이썬런타임 이미지만 가지고 있으면 된다. 이미지로 build를 하면 거기에 종속성, 런타임, 코드 등 다 갖춰져 있는 것이다. 이런 portable images를 정의하는 데가 Dockerfile이다.

### Define a Container with Dockerfile
Dockerfile은 내 컨테이너에서 뭔 일이 일어나는 지를 정의하는 파일이다. docker 환경 안에서 리소스에 접근하는 건 가상화되고, 내 시스템의 나머지와 독립된다. 그냥 할 일은 outside world에 port를 맵핑하는 일과 환경으로 copy in 할 파일을 명확히 하는 것이다. 이렇게 하면 Dockerfile에 정의된 build 정보로 어디서든 실행시킬 수 있다.

Dockerfile, app.py, requirements.txt 3가지 파일을 생성한 뒤

+ create docker image ```docker build --tag=friendlyhello```

+ list docker images ```docker image ls```

+ friendlyhello image 실행. 파이썬이 ```http://0.0.0.:80``` 에서 app을 serving한다. 이 메시지는 컨테이너 내부에서 나온 메시지이고 컨테이너의 80포트랑 4000이 맵핑됐는 지 난 알 수 없지만, 정확한 URL은 localhost:4000 이다. (toolbox는 localhost대신에 docker machine IP를 사용해야 한다. http://192.168.99.100:4000/ 과 같이. IP 주소 찾으려면 docker-machine ip )
```docker run -p 4000:80 friendlyhello```

+ Ctrl + C 만으로 종료가 안되니, ```docker container ls``` 에서 목록보고 ```docker Container stop <Container NAME or ID>``` 해야 함

+ run backgroud mode ```docker run -d -p 4000:80 friendlyhello```

### Share your image
registry는 repository의 집합이고, repository는 image들의 집합이다-github repo랑 비슷한데 여긴 코드가 이미 다 built 돼 있는 상태.

### Log in with your Docker ID
hub.docker.com 에서 id, pw 만들어서 접속

### Tag the image
이미지에 태그 따자.
repo에 올리는 이미지는 ```username/repository:tag``` 를 사용하면 좋다. 태그는 옵션이긴 한데 추천하고, 버전관리 이런거에도 좋다. repository와 tag는 의미있느 이름으로 한다. 예를 들면 ```get-started:part2``` = ```get-started``` repository에 ```part2``` 태그로 image를 put한단 의미이다. ```docker tag friendlyhello dockerhee/get-started:part2``` 이 명령어는 ```docker tag image username/repository:tag``` 이거 그대로 사용.

### Publish the image
```docker push dockerhee/get-started:part2```
![push_image](https://heeyeah.github.io/imgs/docker_push_image.PNG)
