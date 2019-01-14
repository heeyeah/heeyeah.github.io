

1. 지정 위치에 하위에 있는 모든 \*.xml 파일 가져오기.
2. 모든 \*.xml 파일 정보 Queue에 offer.
3. Thread 당 \*.xml 파일 parsing 후 data object에 저장.
4. data object를 모아서 (Queue?) 정해진 단위로 insert batch 실행.
5. TABLE에 데이터 저장.
6. QA팀에 데이터 볼 수 있게 Query 제공.

※일주일에 한 번씩 실행시켜서, 최근 데이터를 업데이트 해야 함. 해당 API를 \*.jar 파일로 만들어서 server에 crontab 설정(주말 새벽에 실행)

+ 서비스콜트리가 너무 느려서, 이번엔 multi-thread로 만들 예정인데 속도가 빠를 지는 테스트해봐야 알 것 같다:<
