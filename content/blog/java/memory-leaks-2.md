---
title: 'memory leak 1. JVM 구조'
date: 2020-12-10 00:00:00
category: java
draft: true
---

# Index

1. memory leak이 발생하는 이유?
2. jvm memory 구조 (1.8~)
3. jvm monitoring
4. garbage collection 튜닝



## Java 1.8부터 JVM 메모리 구조 변화 (permGen → metaspace)
java 1.8부터 permGen (permenant Generation) 영역이 사라지고, 대신에 Metaspace 영역이 생겼다.
간단히 설명하자면, permGen은 jvm에 의해 크기가 강제되던 영역으로 OOM을 발생시키는 원인 중 하나였다고 한다.
metaspace 영역이 permGen의 대체 역할을 하는데, metaspace는 native memory영역으로 OS가 자동으로 크기를 조절한다.
그 결과 기존과 비교해 큰 메모리 영역을 사용할 수 있게 되었다!
또한 metaspace는 heap이 아닌 native 메모리 영역이기 때문에, 개발자가 영역 확보의 상한을 크게 의식하지 않아도 되게 되었다(?)고 한다.

> Perm 영역은 보통 Class의 Meta 정보나 Method의 Meta 정보, Static 변수와 상수 정보들이 저장되는 공간으로 흔히 메타데이터 저장 영역이라고도 한다. 이 영역은 Java 8 부터는 Native 영역으로 이동하여 Metaspace 영역으로 변경되었다. (다만, 기존 Perm 영역에 존재하던 Static Object는 Heap 영역으로 옮겨져서 GC의 대상이 최대한 될 수 있도록 하였다)


## JVM 구조
1. JVM은 heap과 metaspace로 나뉜다.
2. heap은 young generation과 old generation으로 나뉜다.
3. young generation은 eden, survival 0, survival 1으로 나뉜다.
4. old generation은 old generation이다.

![출처:https://www.waitingforcode.com/off-heap/on-heap-off-heap-storage/read](./images/jvm_heap_java8.png)


### 생성주기 - Generation in JVM
새로 생긴 class instance들은 제일 먼저 eden 영역에 할당된다. 그리고 여기서 계속 gc가 일어난다.
eden에서 살아남은 class instance들은 survivor space로 옮겨지며, jvm 전체를 봤을 때 작은 영역이다.
survivor space는 2개가 있는데, 2개 중 꼭 1개의 space는 empty로 유지가 되고, 이 이유는 fragmentation work를 피하기 위함이라고 한다.

하여튼, young generation은 말 그대로 갓 생성된 instance들의 메모리 영역이라고 한다면, old generation은 생명주기가 오래된 instance들의 메모리 영역이다.
생명주기는 어떻게 체크를 하냐면, young generation에서 garbage action에서 4번을 살아 남았다고 가정하면, 이 instace의 나이는 4라고 볼 수 있다.
old generation에서 발생하는 건 major collection이라 하고, young generation은 minor collection이라고 한다.
young generation에 비해 대체적으로 큰 사이즈를 가지고, 더 긴 collection process를 가진다.

minor collection에서의 gc는 'stop-the-world'에 큰 영향을 주진 않으며, eden영역이 full로 차고 새로운 객체가 생성되지 않을 때 minor gc가 발생한다.
major collection에서의 gc가 우리가 흔히 아는 그 gc이며, major gc가 일어날 때엔 GC Thread만 running 상태이다. 모든 thread는 동작을 하지 않고 멈춘다.
major gc의 실행 주기는 old generation의 크기에 의해 결정된다.


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
