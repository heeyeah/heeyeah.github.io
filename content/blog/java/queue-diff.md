---
title:  'LinkedBlockingQueue와 ConcurrentLinkedQueue 차이'
date: 2019-01-15 11:59:00
category: java
draft: false
---

MultiThread 환경에서 Queue를 사용하려다 보니, 아래 두 가지를 사용하면 thread-safe하다고 나오는데 무슨 차이인지 몰라서 일단 포스팅 :) 추후 보완하자.

### LinkedBlockingQueue와 ConcurrentLinkedQueue 차이
LinkedBlockingQueue is intended for use cases when consumers and producers concurrently consume and produce. In this case, for example the consumers might get ahead of producers and empty the queue, so the queue needs to block while the producers come up with some more work. 프로듀서-컨슈머( producer-consumer ) 패턴을 구현할 때 굉장히 편리하게 사용.

The ConcurrentLinkedQueue is useful for example when producers first produce something and finish their job by placing the work in the queue and only after the consumers starts to consume, knowing that they will be done when queue is empty. (here is no concurrency between producer-consumer but only between producer-producer and consumer-consumer)



[참고] https://www.programering.com/a/MDOxYTNwATI.html
