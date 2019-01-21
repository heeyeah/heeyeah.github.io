---
layout: post
title:  "[Docker] Docker Get Started 따라하기 - 3"
date:   '2019-01-21 11:02:00'
author: Heeye
categories: Docker
tags: docker
---

#About Service
service는 container > service > stack 중에 service를 말하는 것이다. 분산된 어플리케이션에서 각기 다른 앱조각들을 service라 한다. 예를 들어 video sharing site가 있다고 가정하면, 여긴 데이터베이스에 어플리케이션을 저장하는 서비스, 사용자가 뭔갈 업로드 후 백그라운드에서 video transcoding하는 서비스, frontend 서비스 등 많은 것이 합쳐져있다. ~~Service are really just "containers in production."~~ 서비스는 하나의 이미지를 실행시키고 이미지가 어떻게 run하는지 코디한다. service scaling은 컨테이너 수를 바꾸는거다. scaling은 docker platform에서 매우 쉬운데, docker-compose.yml만 작성하면 된다.

#docker-compose.yml file
아무데나 docker-compose.yml 파일 만들자. 이 설정파일 내용을 읽고 load-balancing 한다. docker-compose.yml은 indentation이 중요하기 때문에 띄워쓰기 주의해야 한다.

#Run new load-balanced app
docker-compose.yml 파일을 설정한 후 ```docker swarm init``` 을 해주면 swarm (load-balancing) 이 동작하고, ```docker stack deploy -c docker-compose.yml getstartedlab``` 하면 appname을 ```getstartedlab``` 이라고 주게 된다. 여기서 docker-compose.yml을 참고해서 image를 deploy하는데, 동작 후 ```docker service ls``` 로 확인!

#신기한게
똑같은 url인 localhost:4000 을 띄웠을 때 swarm init해서 container 5개로 서비스한 이미지는 hostname이 계속 바뀐다. ```docker container ls -q``` 에서 리스트업된 container IDS들이 뜬다. 이게 load-balancing인데 각 요청에 5개의 태스크 중 하나가 round-robin 방식으로 선택되고 응답을 준다.

#Scale the app
docker-compose.yml 에 replicase 수정해서 docker stack deploy 다시 하면 update 되면서 scale the app!

#Take down the app and the swarm
docekr stack rm getstartedlab && docker swarm leave --force


result
서비스는 컨테이너의 동작을 compose file에 codify하고, 이 파일은 앱을 scale, limit, redeploy 에 사용된다.
