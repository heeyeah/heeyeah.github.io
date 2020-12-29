---
title: 'memory leak 1. JVM memory 구조 (1.8)'
date: 2020-12-10 00:00:00
category: java
draft: true
---

## Java 1.8부터 JVM 메모리 구조 변화 (permGen → metaspace)
java 1.8부터 permGen (permenant Generation) 영역이 사라지고, 대신에 Metaspace 영역이 생겼다.
간단히 설명하자면, permGen은 jvm에 의해 크기가 강제되던 영역(permenant heap)으로 OOM을 발생시키는 원인 중 하나였다고 한다.
metaspace 영역이 permGen의 대체 역할을 하는데, metaspace는 native memory영역으로 OS가 자동으로 크기를 조절한다.
그 결과 기존과 비교해 큰 메모리 영역을 사용할 수 있게 되었다!
또한 metaspace는 heap이 아닌 native 메모리 영역이기 때문에, 개발자가 영역 확보의 상한을 크게 의식하지 않아도 되게 되었다(?)고 한다.
metaspace로 바뀌고 나서는 gc에 의해 발생하는 OutOfMemory Error가 줄었다고 한다.

> Perm 영역은 보통 Class의 Meta 정보나 Method의 Meta 정보, Static 변수와 상수 정보들이 저장되는 공간으로 흔히 메타데이터 저장 영역이라고도 한다. 이 영역은 Java 8 부터는 Native 영역으로 이동하여 Metaspace 영역으로 변경되었다. (다만, 기존 Perm 영역에 존재하던 Static Object는 Heap 영역으로 옮겨져서 GC의 대상이 최대한 될 수 있도록 하였다)


+ Java7 HotSpot JVM

<----- Java Heap ----->             <--- Native Memory --->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Permanent | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+
                        <--------->
                       Permanent Heap
S0: Survivor 0
S1: Survivor 1


+ Java8 HotSpot JVM

<----- Java Heap -----> <--------- Native Memory --------->
+------+----+----+-----+-----------+--------+--------------+
| Eden | S0 | S1 | Old | Metaspace | C Heap | Thread Stack |
+------+----+----+-----+-----------+--------+--------------+


[출처](https://johngrib.github.io/wiki/java8-why-permgen-removed/)



## JVM 구조 (1.8)
0. JVM은 Classloader, Execution engine, runtime data areas 로 나뉜다.
1. runtime data areas는 heap과 metasapce(=method area), threads(pc register, stack, native method stack)로 구성된다.
2. heap은 young generation과 old generation으로 나뉜다.
3. young generation은 eden, survival 0, survival 1으로 나뉜다.
4. old generation은 old generation이다.
5. metaspace는 class, method meta 정보를 저장하고 static 키워드로 선언된 변수를 저장한다. (기본형이 아닌 static 변수는 레퍼런스 변수만 저장되고 실제 인스턴스는 heap에 저장.)

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


---

💡참고자료
- https://johngrib.github.io/wiki/java8-why-permgen-removed/
- https://yaboong.github.io/java/2018/06/09/java-garbage-collection/
- https://www.waitingforcode.com/off-heap/on-heap-off-heap-storage/read
- https://www.baeldung.com/java-memory-leaks
- [(Naver D2)Garbage Collection 튜닝](https://d2.naver.com/helloworld/37111)
- https://jithub.tistory.com/40
