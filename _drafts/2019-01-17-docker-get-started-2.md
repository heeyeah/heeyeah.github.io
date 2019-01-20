
Docker 방식으로 app을 build하려면, 제일 밑부터 쌓아야 하는데 그게 Container이다. Container > Service > Stack 순으로 밑부터 쌓여 올라간다라고 생각하면 될 것 같다.

#Your new development environment
기존에 개발 환경에서 Python 앱을 실행시키고 싶다 하면 먼저 파이썬 런타임을 설치해야한다. 환경구성도 맞춰야하고 OS가 뭔지에 따라 필요한 것도 다 달라서 맞춰줘야 하는 것이지. 하지만 도커는 뭐 설치할 필요없이 파이썬런타임 이미지만 가지고 있으면 된다. 이미지로 build를 하면 거기에 종속성, 런타임, 코드 등 다 갖춰져 있는 것이다. 이런 portable images를 정의하는 데가 Dockerfile이다.

#Define a Container with Dockerfile
Dockerfile은 내 컨테이너에서 뭔 일이 일어나는 지를 정의하는 파일이다. docker 환경 안에서 리소스에 접근하는 건 가상화되고, 내 시스템의 나머지와 독립된다. 그냥 할 일은 outside world에 port를 맵핑하는 일과 환경으로 copy in 할 파일을 명확히 하는 것이다. 이렇게 하면 Dockerfile에 정의된 build 정보로 어디서든 실행시킬 수 있다.

Dockerfile, app.py, requirements.txt 3가지 파일을 생성한 뒤

+ create docker image
docker build --tag=friendlyhello

+ list docker images
docker image ls

+ friendlyhello image 실행. 파이썬이 http://0.0.0.:80 에서 app을 serving한다. 이 메시지는 컨테이너 내부에서 나온 메시지이고 컨테이너의 80포트랑 4000이 맵핑됐는 지 난 알 수 없지만, 정확한 URL은 localhost:4000 이다. (toolbox는 localhost대신에 docker machine IP를 사용해야 한다. http://192.168.99.100:4000/ 과 같이. IP 주소 찾으려면 docker-machine ip )
docker run -p 4000:80 friendlyhello

+ Ctrl + C 만으로 종료가 안되니, docker Container ls 에서 목록보고 docker Container stop <Container NAME or ID> 해야 함

+ run backgroud mode
docker run -d -p 4000:80 friendlyhello

#Share your image
registry는 repository의 집합이고, repository는 image들의 집합이다-github repo랑 비슷한데 여긴 코드가 이미 다 built 돼 있는 상태.

#Log in with your Docker ID
hub.docker.com 에서 id, pw 만들어서 접속

#Tag the image
이미지에 태그 따자.
repo에 올리는 이미지는 ```username/repository:tag``` 를 사용하면 좋다. 태그는 옵션이긴 한데 추천하고, 버전관리 이런거에도 좋다. repository와 tag는 의미있느 이름으로 한다. 예를 들면 ```get-started:part2``` = ```get-started``` repository에 ```part2``` 태그로 image를 put한단 의미이다. ```docker tag friendlyhello dockerhee/get-started:part2``` 이 명령어는 ```docker tag image username/repository:tag``` 이거 그대로 사용.

#Publish the image
docker push dockerhee/get-started:part2
![push_image](../imgs/docker_push_images.png)


part3.
[Service]
#About Service
service는 container > service > stack 중에 service를 말하는 것이다. 분산된 어플리케이션에서 각기 다른 앱조각들을 service라 한다. 예를 들어 video sharing site가 있다고 가정하면, 여긴 데이터베이스에 어플리케이션을 저장하는 서비스, 사용자가 뭔갈 업로드 후 백그라운드에서 video transcoding하는 서비스, frontend 서비스 등 많은 것이 합쳐져있다. ~~Service are really just "containers in production."~~ 서비스는 하나의 이미지를 실행시키고 이미지가 어떻게 run하는지 코디한다. service scaling은 컨테이너 수를 바꾸는거다. scaling은 docker platform에서 매우 쉬운데, docker-compose.yml만 작성하면 된다.

#Run new load-balanced app
docker-compose.yml 파일을 설정한 후 ```docker swarm init``` 을 해주면 swarm (load-balancing) 이 동작하고, ```docker stack deploy -c docker-compose.yml getstartedlab``` 하면 appname을 ```getstartedlab``` 이라고 주게 된다. 여기서 docker-compose.yml을 참고해서 image를 deploy하는데, docker-compose.yml은 indentation이 중요하기 때문에 띄워쓰기 주의해야 한다. ```docker service ls``` 로 확인!

#신기한게
똑같은 url인 localhost:4000 을 띄웠을 때 swarm init해서 container 5개로 서비스한 이미지는 hostname이 계속 바뀐다. ```docker container ls -q``` 에서 리스트업된 container IDS들이 뜬다. 이게 load-balancing인데 각 요청에 5개의 태스크 중 하나가 round-robin 방식으로 선택되고 응답을 준다.

#Scale the app
docker-compose.yml 에 replicase 수정해서 docker stack deploy 다시 하면 update 되면서 scale the app!
#Take down the app and the swarm
docekr stack rm getstartedlab && docker swarm leave --force
