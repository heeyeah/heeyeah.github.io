---
layout: post
title:  "[Docker] Docker Get Started 따라하기 - 4"
date:   '2019-01-21 18:00:00'
author: Heeye
categories: Docker
tags: docker
---
image를 만드려고 Dockerfile, app.py, requirements.txt를 생성해서 docker build를 하고 docker run.
docker container에 image가 올라가면서 실행됨.
이 image를 container 여러개에 deploy할 수 있음. docker-compose.yml 에 설정한 값만큼 container가 띄워짐.

Swarms.
여러 개의 컨테이너, 여러 개의 docker machine에 어플리케이션을 만들 수 있다. swarm이라고 불리는 Dockerized cluster에 다중 머신을 join해서
you deploy this application onto a cluster, running it on multiple machines. Multi-container, multi-machine applications are made possible by joining multiple machines into a “Dockerized” cluster called a swarm.

#Understanding Swarm clusters
swarm : docker running 하고 cluster를 join하는 group of machines.
swarm manager : execute docker on a cluster.
node : swarm에 join된 machine들을 node라고 부른다.


잘 이용안하는 machines으로 컨테이너를 채움.
machine 하나가 특정 container 하나를 맡음.
?????


compose file에 이런 전략들을 사용해서 swarm manager에게 지시할 수 있다.

single-host mode / swarm mode

#Set up your swarm
A swarm is made up of multiple nodes.
swarm을 setup 하는건 쉽다. docker swarm init 해서 swarm mode를 만든 후, docker swarm join을 다른 머신에서 실행시켜서 worker 로써 swarm에 포함시키는 것이다.

##Create a cluster

- vm on your local machine
일단 vm을 여러개 만들기 위해서 ```docker-machine create --driver virtualbox myvm1``` 을 하면 myvm1이란 이름을 가진 virtualbox가 실행된다.

- list the vms and get their ip addresses
. ```docker-machine ls```으로 확인하기.

- initialize the swarm and add nodes
docker swarm init 한 노드는 manager가 되고, default에서 다른 machine도 매니저시키고 싶으면 ```docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"``` 를 사용하면 된다.
docker machine을 swarm에 worker로 join시키고 싶다면 대상 machine에 대해 ```docker-machine ssh myvm2 "docker swarm join --token <token> <ip>:2377"``` 실행한다. 여기서 token과 ip는 swarm manager의 것.

#Deploy your app on the swarm cluster

- Configure a docker-machine shell to the swarm manager
. ```docker-machine ssh``` 를 사용해서 command를 할 수도 있지만, ```docker-machine env myvm1``` 을 치면 설정들이 나오고, shell configure 하려면 아래 명령어를 실행하세요! 이런게 나온다. 환경마다 다 다른 것 같은데, 알아서 잡아 주는 듯? 나같은 경우는 ```eval $("C:\Program Files\Docker Toolbox\docker-machine.exe" env myvm1)``` 이거여서, 실행시켜 주면 ```docker-machine ls```로 ACTIVE가 myvm1에 체크되있는 걸 확인할 수 있다.

- Deploy the app on the swarm manager
- Accessing your cluster
- Iterating and scaling your app
- Cleanup and reboot
- Unsetting docker-machine shell variable settings
.  ```eval $(docker-machine env -u)```
- Restarting Docker machines
. ```docker-machine start <machine-name>```
