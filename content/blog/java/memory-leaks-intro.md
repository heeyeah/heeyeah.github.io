---
title: 'memory leak. 서론 - 왜 이걸 정리하는지?'
date: 2020-12-28 17:00:00
category: java
draft: false
---



## memory leak
코드만 보고 memory leak 현상의 원인을 파악하는 것은 모래밭에서 바늘찾기와 같다는 말을 본 적이 있다.
다양한 원인에 의해 발생하기도 하고, 단순히 **메모리릭**이라고 해서 하나의 이유가 아니니 그만큼 원인을 파악하기 어렵다는 말이다.

일단 memory leak인가보다 라고 인지하려면, `FullGC`가 계속 발생하지만 `heap memory`에는 변화가 없는 경우 memory leak이 발생한다고 인지할 수 있다. 이럴 땐 `heap dump`를 떠서 분석이 필요하다.

그럼 여기서 기본적으로 알고 있어야 할 게 `FullGC`, `heap memory`, `heap dump` 등이 뭔지를 알아야 하는데, 이것을 이해하려면 JVM 구조에 대한 이해가 필요하다.


## Index
memory leak을 잡으려면 JVM을 알아야하고,
JVM 위에서 동작하는 어플리케이션의 동작방식도 알아야하고,
어플리케이션의 동작방식은 결국 내가 개발한 코드에 의해서 좌우되니,

내가 개발한 코드가 어떻게 어플리케이션을 동작시키고 이게 JVM에서 어떻게 메모리를 사용해서 동작하는지까지 알아야 메모리 누수를 잡든, garbage collection 튜닝을 하든 이런 게 가능하다.

그래서 정리해야 할 개념들이 꽤 많지만,
기본적인 키워드 정리만하기로 하고 자세한 건 상황에 따라 나타나는 현상에 따라 그때그때 파악하는 게 좋다고 생각한다.


### 정리할 목록
1. JVM memory 구조 (1.8)
2. JVM monitoring & Garbage collection 튜닝

정리할 것들을 쓰고 나서 JVM 튜닝할 것을 보니, 결국 JVM 모니터링이 우선이 돼야 옵션을 바꿔서 부하테스트를 하든 뭐든 다 가능하다.
개발 환경에 JVM을 모니터링할 수 있는 환경부터 만들어 놓고, 왜 OOM이 나는지 튜닝을 어떻게 하면 좋을지 지금 어플리케이션은 잘 돌아가고 있는지를 판단해야 할 것 같다.
(그리고 Garbage collection 튜닝은, 이 모든 것의 마지막에 하는 것이라고 한다!)


---

💡참고자료
- https://woowabros.github.io/tools/2019/05/24/jvm_memory_leak.html
