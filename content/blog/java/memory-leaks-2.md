---
title: 'memory leak 2. JVM monitoring & Garbage collection 튜닝'
date: 2020-12-10 00:00:00
category: java
draft: true
---

## Garbage Collection 튜닝

>모든 Java 기반의 서비스에서 GC 튜닝을 해야 할까?

결론부터 이야기하면 모든 Java 기반의 서비스에서 GC 튜닝을 진행할 필요는 없다.

GC 튜닝이 필요 없다는 이야기는 운영 중인 Java 기반 시스템의 옵션과 동작이 다음과 같다는 의미이다.

- -Xms 옵션과 –Xmx 옵션으로메모리크기를지정했다.
- -server 옵션이포함되어있다.
- 시스템에 Timeout 로그와같은로그가남지않는다.
다시 말하면, 메모리 크기도 지정하지 않고 Timeout 로그가 수도 없이 출력된다면 여러분의 시스템에서 GC 튜닝을 하는 것이 좋다.

그런데 한 가지 꼭 명심해야 하는 점이 있다. GC 튜닝은 가장 마지막에 하는 작업이라는 것이다.

### GC 튜닝에 자주 사용하는 옵션
- -Xms 옵션
- -Xmx 옵션
- -XX:NewRation 옵션


간혹 Perm 영역의 크기는 어떻게 설정해야 하는지 문의하는 분들이 있다. Perm 영역의 크기는 OutOfMemoryError가 발생하고, 그 문제의 원인이 Perm 영역의 크기 때문일 때에만 -XX:PermSize 옵션과 -XX:MaxPermSize 옵션으로 지정해도 큰 문제는 없다.


### 튜닝 절차
1. GC 상황 모니터링
GC 상황을 모니터링하며 현재 운영되는 시스템의 GC 상황을 확인해야 한다. "Garbage Collection 모니터링 방법" 글에 다양한 GC 모니터링 방법을 소개했으니 참조하기 바란다.

2. 모니터링 결과 분석 후 GC 튜닝 여부 결정
GC 상황을 확인한 후에는, 결과를 분석하고 GC 튜닝 여부를 결정해야 한다. 분석한 결과를 확인했는데 GC 수행에 소요된 시간이 0.1~0.3초 밖에 안 된다면 굳이 GC 튜닝에 시간을 낭비할 필요는 없다. 하지만 GC 수행 시간이 1~3초, 심지어 10초가 넘는 상황이라면 GC 튜닝을 진행해야 한다.

>참고
> heap dump는 jmap 이라는 명령어로 생성가능. jdk에 포함되어 있다.
> gc 체크는 jstat -gcutil 명령어를 사용.

기준이 절대적이진 않으나, 다음과 같은 일반적인 기준이 있다. (어플리케이션 성격, 시스템마다 다르기 때문에 절대적인 기준이 아님)

GC가 수행되는 시간을 확인했을 때 결과가 다음의 조건에 모두 부합한다면 GC 튜닝이 필요 없다.

Minor GC의처리시간이빠르다(50ms내외).
Minor GC 주기가빈번하지않다(10초내외).
Full GC의처리시간이빠르다(보통1초이내).
Full GC 주기가빈번하지않다(10분에 1회).

---

💡참고자료
- https://johngrib.github.io/wiki/java8-why-permgen-removed/
- https://yaboong.github.io/java/2018/06/09/java-garbage-collection/
- https://www.waitingforcode.com/off-heap/on-heap-off-heap-storage/read
- https://www.baeldung.com/java-memory-leaks
- [(Naver D2)Garbage Collection 튜닝](https://d2.naver.com/helloworld/37111)
