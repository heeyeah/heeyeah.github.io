

1. 지정 위치에 하위에 있는 모든 \*.xml 파일 가져오기.
2. 모든 \*.xml 파일 정보 Queue에 offer.
3. Thread 당 \*.xml 파일 parsing 후 data object에 저장.
4. data object를 모아서 (Queue?) 정해진 단위로 insert batch 실행.
5. TABLE에 데이터 저장.
6. QA팀에 데이터 볼 수 있게 Query 제공.

※일주일에 한 번씩 실행시켜서, 최근 데이터를 업데이트 해야 함. 해당 API를 \*.jar 파일로 만들어서 server에 crontab 설정(주말 새벽에 실행)

+ 서비스콜트리가 너무 느려서, 이번엔 multi-thread로 만들 예정인데 속도가 빠를 지는 테스트해봐야 알 것 같다:<

### LinkedBlockingQueue와 ConcurrentLinkedQueue 차이
LinkedBlockingQueue is intended for use cases when consumers and producersconcurrently consume and produce. In this case, for example the consumers might get ahead of producers and empty the queue, so the queue needs to block while the producers come up with some more work. 프로듀서-컨슈머( producer-consumer ) 패턴을 구현할 때 굉장히 편리하게 사용.

The ConcurrentLinkedQueue is useful for example when producers first produce something and finish their job by placing the work in the queue and only after the consumers starts to consume, knowing that they will be done when queue is empty. (here is no concurrency between producer-consumer but only between producer-producer and consumer-consumer)

Simple, Fast, and Practical Non-Blocking and Blocking Concurrent Queue Algorithms by Maged M. Michael, Michael L. Scott
