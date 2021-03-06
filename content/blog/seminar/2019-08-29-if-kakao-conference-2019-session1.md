---
title: "[if kakao 개발자 컨퍼런스 2019] 오픈소스 데이터베이스, 흐르는 은행 데이터에 빨대를 꽂아보다."
date: '2019-08-29 11:00:00'
category: seminar
draft: false
---


## 오픈소스 데이터베이스, 흐르는 은행 데이터에 빨대를 꽂아보다.

은행 데이터라고 해서... 카카오뱅크에서 연설하겠지 싶어서 들었는데 카카오뱅크 **DBA** 여서 발표내용은 제대로 이해가 되지 않지만 일단 [if kakao 개발자 컨퍼런스 2019 홈페이지](https://if.kakao.com/program)에서 세션에 대한 설명을 들고 왔다.

### 세션 설명

MySQL의 트랜잭션 로그인 바이너리로그를 활용하여, 흐르는 은행 서비스 데이터를 다른 샤딩 디비에 효율적으로 분산 저장하는 방안을 소개.
과거 KakaoADT 오픈소스 프로젝트 컨셉을 공유하는 프로젝트2탄이며, "MySQL로 유입된 데이터"에 빨대를 꽂아, 타겟의 종류와는 상관없이, "서비스 특화된 데이터로 재구성"하는 방안을 공유하고자 합니다.

- 데이터 샤딩의 목적과 한계점
- MySQL 바이너리로그 특성
- Kakao ADT의 컨셉과 비교
- 온라인 서비스를 위한 고려사항과 추가 개선사항
- 대용량 트래픽 성능 테스트
- 적용 사례 및 이슈사항


	성동찬(chan.chan) / 카카오뱅크

### 정리

#### INTRO
- 1000만 고객
- 메가톤급 그래픽 703 (신한은행은 600)
- 흑자 달성
- **Mysql 사용하면서 단 한번도 장애가 난 적이 없다!**
- 고객 서비스를 확장하고 싶지만(예를 들어 통장이력에 대한 데이터로 개인화된 컨텐츠를 만들어 주던지 이런 사용자 기준의 서비스) 그러려면 데이터 분산 처리가 시급하다. ~~일단 여기서부터 이해 안감~~
- 사실 카카오뱅크는 트래픽만 보면 금융 탈을 쓴 소셜 서비스이다.

**결론이 데이터 분산 처리가 시급하니, 데이터를 분산 재배치 시켜보자** 였다.


#### 데이터 분산 처리를 위한 방안 고려
1. SQL로 데이터 퍼가기
  * Pros
    * SQL로 데이터를 자유롭게 제어
    * 로직에 포함되며, 상대적으로 구조가 간결
  * Cons
    * 소스DB에 부하 (SELECT/INDEX)
    * 지연된 커밋에 따라 데이터누락 발생 가능

2. CDC(Change Data Capture)로 변경분 퍼가기
  * Pros
    * 소스DB에 부하가 거의 없음
    * 누락없이 실시간 전송 보장
  * Cons
    * 비동기 데이터 복제 및 복제 시스템 운영
    * 분산 로직 적용이 어려움

**기존 방법만으로는 문제를 해결할 수 없다. 프로젝트 시작함!**


#### [Uldraman] 흐르는 데이터에 빨대를 꼽아, 원하는 형태로 신속 정확하게 재구성해주는 프로젝트

**01 MySQL Binary Log**

바이너리로그는 트랜잭션 커밋 시 기록하는 로그 파일, 데이터 변경 순서를 보장. (2 fails commit)
“신뢰”할 수 있는 “확정”된 변경 이력

바이너리로그는 이벤트가 많은데,
ROW Event중에
Query Event : Begin
Table map event
Row events
Table map eent
Row event
Query event : end

Table map event : 메타 및 맵핑 정보 (컬럼명 미포함)
Write/update/Delete row events : 변경된 row 정보

Https://github.com/kakao/adt 에서부터 시작

**03 Requirements**

Binary Log Row Format : Row 포맷으로 기록된느 바이너리 로그여야 한다.
Binary log Full Image :  바이너리 로그에 변경 전/후 이미지가 모두 포함되어야 한다.

모든 테이블 메타 정보 접근 권한 : Binary Log에는 컬럼명에 대한 정보 및 추가 정보(unsigned)가 포함되지 않음

시간은 밀리세컨드까지 제한

**04 Ultra & Kakao ADT**

Overwrite
Sequential
데이터를 그룹핑하고, 동일 그룹 데이터는 순차 처리한다
Parallel 데이터를 그룹핑하고

**05 Ultra Feature**

Recover Mode : (일시적으로) 데이터 버전 체크를 하면서 덮어 쓰자
Generate Shard Value
Mapping Trigger : 타겟 데이터를 마음대로 바꿔보고 싶어요.
새로운 형태로 가공하고 싶을 때, 동찬/서울/까칠의 데이터를 동찬/서울/까칠동찬 이렇게 조합해보고싶다는 뜻.
Interface

**06 Performance**

데이터 분산(스케일아웃) 성능 테스트 - 초당 30,000건
데이터 통합(스케일인) 성능 테스트 - InnoDB(4node)에서 1node로 데이터 통합하는 것 안밀림!

장사 잘되는 천만고객의 은행 + 몬스터급 트래픽 처리 고민 + 분산 확장을 위한 UldraMan!

#### 세션 마무리
Uldraman은 한마디로 아래와 같다고 한다.

- 데이터 실시간 분산 재배치
- 분산(샤드) 키 변환/생성
- 유연한 샤드 관리


- MySQL BinLog 파서
- 실시간 OLTP 서비스 용도
- 버전체크/변환/인터페이스
- 자유로운 데이터 빨대 목표
