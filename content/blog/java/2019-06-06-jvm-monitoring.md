---

title: "[JVM] Jmeter, VisualVM 모니터링 해보기"
date: '2019-06-06 18:20:20'
category: java
draft: false
---

## jvm-monitoring
JVM 모니터링 실습해보기🖥

## 준비
- [jmeter 설치](#jmeter-설치-및-실행)
- [visualVM 설치](#visualVM-설치)
- [springboot application build](#springboot-app-build-&-run)


#### jmeter 설치 및 실행
``` bash
# Mac, jmeter 설치
brew install jmeter

# boot
jmeter
```

#### visualVM 설치

OS에 맞게 설치하면 된다.
https://visualvm.github.io/download.html


#### springboot app build & run
``` bash
./gradlew build
./gradlew bootRun
```

<br/>
## 테스트
테스트를 위해 만들어 놓은 http api는 ```GET(/stress/go)``` 이고, totalMemory, maxMemory, freeMemory를 로그로 찍어준다. 여기서 memory는 Runtime api에서 가져온 것으로 Heap Memory 값이다.

Jvm argument를 bootRun할 때 command line으로 주고 싶은데, 도대체체가 옵션을 먹지 않아서 build.gradle에 아래와 같이 추가했다.
``` groovy
apply plugin: 'application'
applicationDefaultJvmArgs=["-Xms512m", "-Xmx1024m"]
```
파일에 Default JVM option을 바꾸면서 테스트할 것이다. jmeter로 부하테스트를 주면서 visualVM으로 모니터링하는 값을 보면 JVM에 대해 조금 더 실질적으로 이해할 수 있지 않을까 생각한다.

~~부하를 주는 어플리케이션을 springboot로 만들어 보려다가, jmeter가 있는데 굳이 만들어서 써야하나 싶어서 3시간 삽질하다가 jmeter로 테스트할 예정이다😭~~
