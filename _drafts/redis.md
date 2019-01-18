
Redis ? REmote DIctionary System의 약자로 key/value구조의 비정형 데이터를 저장하고 관리하기 위한 오픈 소스 기반의 비관계형 데이터베이스 관리 시스템이다. 모든 데이터를 메모리로 불러와서 처리하는 메모리기반 DBMS.
Redis는 단순한 메모리 기반의 key-value store인 memcached와 차이점이 있다. 이 차이점은 다양한 데이터 타입을 지원한다는 것이다. String, Set Sorted Set, Hashes, List가 있다. Sorted Set은 가중치가 있는 집합체이고 Hashes는 map같은 개념인 것 같다. List는 Double Linked List를 생각하면 될 것 같다! 저장된 데이터에 대한 연산이나 추가 작업도 가능하다. (set에서 교집합, 합집합 같은 연산들 등)
