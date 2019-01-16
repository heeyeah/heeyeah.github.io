


Window 10 Home, Window 7에서는 Docker Toolbox 설치해서 사용한다. (https://docs.docker.com/toolbox/toolbox_install_windows/)

그 이상 버전은 Docker Desktop for Windows를 사용한다. (64bit Windows 10 Pro, with Enterprise and Education (1607 Anniversary update, Build 14393 or later), consider using Docker Desktop for Windows instead.)

docker 가이드에 나온대로 toolbox를 설치했다. install 할 때 나오는 설정은 그대로 사용했다.
install만 했더니 docker가 떴다. 핵쉽다 여기까지.

```docker-machine ls``` 명령어로 보면 현재 Running 중인 docker들이 나온다. (not containers)


### docker get started 문서보고 따라하기
https://docs.docker.com/get-started/
1. Set up your Docker environment (on this page)
2. Build an image and run it as one container
3. Scale your app to run multiple containers
4. Distribute your app across a cluster
5. Stack services by adding a backend database
6. Deploy your app to production


## Set up your Docker environment

### Docker concepts
Docker는 developer와 sysadmin이 컨테이너를 사용해 어플리케이션을 개발,배포,운영을 위한 플랫폼이다. 어플리케이션을 배포하는 리눅스 컨테이너의 사용을 containerization라고 부른다. 컨테이너는 새로운 게 아니고, 쉽게 어플리케이션을 배포할 수 있게 해주는 것이다.

containerization이 인기있는 이유는
Flexible: Even the most complex applications can be containerized.
Lightweight: Containers leverage and share the host kernel.
Interchangeable: You can deploy updates and upgrades on-the-fly.
Portable: You can build locally, deploy to the cloud, and run anywhere.
Scalable: You can increase and automatically distribute container replicas.
Stackable: You can stack services vertically and on-the-fly.
이래서 :P

### Images and Containers
A container is launched by running an image. An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

A container is a runtime instance of an image--what the image becomes in memory when executed (that is, an image with state, or a user process). You can see a list of your running containers with the command, docker ps, just as you would in Linux.

### Containers and virtual machines
컨테이너와 VM의 차이점 그림으로 설명. 컨테이너는 host machine의 kernel을 공유한다. 실행가능한 그 어떤것보다 더 메모리를 필요로 하지 않으니 lightweight~ 라고 할만하다. 그와는 반대로 VM은 각각의 Guest OS가 동작하기 때문에, 일반적으로 어플리케이션이 자원을 더 필요로 할수록 더 많은 자원이 든다.

### Prepare your Docker environment
install~

### Test Docker version
```docker --version```
```docker info```

### Test Docker installation
```docker run hello-world```
```docker image ls```
```docker container ls --all```

### Recap and cheat sheet






Docker is a platform for developers and sysadmins to develop, deploy, and run applications with containers. The use of Linux containers to deploy applications is called containerization.
