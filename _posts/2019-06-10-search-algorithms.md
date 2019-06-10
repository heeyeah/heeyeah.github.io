---
layout: post
title: "[Algorithm] Search Algorithm Basic"
date: '2019-06-10 18:00:00'
author: Heeye
categories: Algorithm
tags: Algorithm Search Java
---

## 출처
Search algorithm에 대해 찾다가 Java로 자세히 설명한 [Posting](https://stackabuse.com/search-algorithms-in-java/)이 있어서 내가 필요한 수준으로 정리한다.

## Search algorithm
뭔가를 찾는 알고리즘은 어디든 많이 쓰인다. Arrays, List, Map과 같은 자료구조에 저장된 데이터들을 찾을 때도 그렇다. Java API에서 사용하는 search algorithm들과 유명한 search algorithm 두, 세개만 정리하고자 한다.

* [Java Collections API](#java-collections-api)
* [Linear Search](#linear-search)
* [Binary Search](#binary-search)

* 그 외는 정말 잘 정리된 포스팅을 볼 것! [Search Algorithm](https://stackabuse.com/search-algorithms-in-java/)



### Java Collections API
자바 API에서 사용하고 있는 검색 알고리즘이 무엇인지를 살펴본 후 그 알고리즘에 대해 공부하면 좋을 것 같다.

#### Arrays
Arrays API에는 `java.util.BinarySearch` 메소드가 존재한다. OpenJDK version에서는 iterative form의 서치를 사용하고 있다. (binary search에는 iterative, recursive form 2가지가 존재한다.)

#### Ths List Interface
리스트 인터페이스는 searching하는 두가지 메소드가 있는데, `indexOf()`와 `contains()` 이다.
`indexOf()` 메소드는 리스트에 element가 존재하면 index를 반환하고 없으면 -1을 반환한다.
`contains()` 메소드는 element 포함 유무에 따라 boolean 값을 반환하는데, 내부적으로는 `indexOf()` 메소드를 call한다.

이 리스트 인터페이스는 Sequential Search를 사용하며, 그래서 time complexity는 `O(N)`이다.


#### The Map Interface
맵은 key-value 쌍으로 구성된 데이터 구조이다. 자바에서 맵 인터페이스는 `HashBased` 검색과 Binary Search Tree를 사용한다.

`java.util.HashMap` 클래스는 key의 hash-value를 사용한다. hash된 올바른 key값과 good hashing algorithm을 사용한 맵에서 element를 조회하는 것은 `O(1)`이다.

`java.tuil.TreeMap` 클래스는 내부적으로 **Red-Black Tree** 알고리즘을 사용한다. Red-Black Tree 알고리즘은 self-balancing 바이너리 서치 트리 형태이다. 이 tree에 추가되는 element들은 자동적으로 sorting된 방식으로 저장된다. time complexity는 `O(log(N))`이다.


#### The Set Interface
셋은 unique element를 가지는 데이터 구조인데, 내부적으로 Map 인터페이스레 wrapping되어 있다. 맵과 같이 `Binary`와 `Hash-based` 서치알고리즘을 사용한다.


**간단하 보면 Sequential Search(=Linear Search)와 Binary Search에 대한 이야기가 주이다. 이 두가지 알고리즘은 최소한 제일 기본이라는 말!**


### Linear Search
Linear Search 또는 Sequential Search라고 불리는 이 알고리즘은 세상 간단한 검색 알고리즘이다. 순차 검색 알고리즘이라고 부르는데, 찾고자 하는 값을 맨 앞에서부터 끝까지 차례대로 찾아 나가는 것이다. brute-force 알고리즘이다. 다 해보는 것! 전체 검색!

- Time complexity : O(N)
- Space complexity : O(1)
- 활용
small 사이즈거나 정렬되지 않은 데이터에서 사용하기 적합하다. 집합의 크기가 커질수록 time complexity가 늘어나기 때문에 비효율적이다.

### Binary Search
Binary Search 또는 Logarithmic Search라고 불리는 이 알고리즘은 빠른 검색 시간을 장점으로 아주 많이 쓰이는 검색 알고리즘이다. 이 검색 알고리즘은 **Divide and Conquer**, 분할정복 알고리즘을 사용하고 데이터가 미리 sort된 상태여야 한다.

과정은 이렇다.
똑같이 반으로 나누고! 목표 element를 향해서 하나씩 비교하면서 중간점을 향해 달려간다!

만약 내가 찾고자하는 element를 찾으면 거기서 끝이다. **목표 요소가 중간 요소보다 작거나 큰지 여부에 따라 배열의 적절한 파티션을 나누고 선택해서 요소를 계속 찾는다. 이게 핵심이다!**

firstIndex와 lastIndex가 만나는 지점에서 검색은 끝나고, 이떄 찾지 못하면 그 element는 없다고 봐야 한다.

binary search를 구현하는 방법은 iterative와 recursive 2가지 방법이 존재한다.

- Time complexity : O(log(N))
- Space complexity : O(1) (recursive의 경우 worst case는 O(log(N)))
- 활용
대부분의 라이브러리에 이 알고리즘이 쓰인다. sort되어있고 데이터 양이 많을 때 사용된다.
