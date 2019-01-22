

##tocmat

$docker pull tomcat:8.5-jre8
bxm4.0 떠있는 spec이 이래서, tomcat8.5에 java8로 pull
Server version: Apache Tomcat/8.5.24
Server built:   Nov 27 2017 13:05:30 UTC
Server number:  8.5.24.0
OS Name:        Linux
OS Version:     2.6.32-358.el6.x86_64
Architecture:   amd64
JVM Version:    1.8.0_121-b13
JVM Vendor:     Oracle Corporation

$docker image ls
> 확인

$docker run -d -i -p --name bxm_tomcat 8021:8080 tomcat:8.5-jre8
> -d : 데몬으로 실행
> -i : 표준 입력을 활성화하여 컨테이너와 연결되어 있지 않더라도 표준 입력을 유지 (무슨소린지 모르겠음)
> -p : 8021:8080. 이미지를 8080으로 실행하지만, 호스트에서 접근할 때 8021로 접근하겠다는 의미.
=> 192.168.99.100:8021 해서 tomcat index 페이지가 뜨면 정상작동
> ```docker container ls``` 확인가능
> ```docker container stop <container Id 구분가능한 앞자리들> ``` 로 스탑가능

준비한 springboot sample을 docker tomcat webapps 하위에 copy
$docker cp ./bxm-install/docker-springboot-demo-0.0.1-SNAPSHOT.war bxm_tomcat:/usr/local/tomcat/webapps/


##Oracle 이나 MySql (일단 Oracle!)
> oracle 이미지 받아서 ddl 실행한 뒤 다시 image 만들기

$docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password --name bxm_mysql mysql
> -e MYSQL_ROOT_PASSWORD=password : 컨테이너를 생성하면서 환경변수를 지정. root계정의 비밀번호 생성.

$docker exec -i -t bxm_mysql bash
> mysql 컨테이너 접속

$mysql -u root -p
> 외부에서 mysql 접속이 안돼서 ```ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password';``` 함.
> mysql에 user를 생성하지 않고 root로 일단 접근.
> mysql_native_password is normally the default authentication method. 뭔소린지.



##Springboot 어플리케이션을 war로 tomcat에 배포하기
BXM 을 tomcat에 올리고 scale out 하는 거에 앞서서, docker tomcat에 웹어플리케이션을 어떻게 올리는지, 동작은 어떤 방식으로 하는 지 이해를 해야 할 수 있을 것 같아서 springboot 어플리케이션을 간단하게 만들어서 war 형태나 path를 잡아서 deploy하기로 함.


* docker container에 접근하려면 ```docker exec -it <container-name> bash``` 로 가능!
