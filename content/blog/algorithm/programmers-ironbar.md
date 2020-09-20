---
title: '프로그래머스 쇠막대기 문제'
date: 2019-01-14 12:20:00
category: algorithm
draft: false
---

### 프로그래머스 쇠막대기

#### 문제
여러 개의 쇠막대기를 레이저로 절단하려고 합니다. 효율적인 작업을 위해서 쇠막대기를 아래에서 위로 겹쳐 놓고, 레이저를 위에서 수직으로 발사하여 쇠막대기들을 자릅니다. 쇠막대기와 레이저의 배치는 다음 조건을 만족합니다.
- 쇠막대기는 자신보다 긴 쇠막대기 위에만 놓일 수 있습니다.
- 쇠막대기를 다른 쇠막대기 위에 놓는 경우 완전히 포함되도록 놓되, 끝점은 겹치지 않도록 놓습니다.
- 각 쇠막대기를 자르는 레이저는 적어도 하나 존재합니다.
- 레이저는 어떤 쇠막대기의 양 끝점과도 겹치지 않습니다.

이러한 레이저와 쇠막대기의 배치는 다음과 같이 괄호를 이용하여 왼쪽부터 순서대로 표현할 수 있습니다.
(a) 레이저는 여는 괄호와 닫는 괄호의 인접한 쌍 '()'으로 표현합니다. 또한 모든 '()'는 반드시 레이저를 표현합니다.
(b) 쇠막대기의 왼쪽 끝은 여는 괄호 '('로, 오른쪽 끝은 닫힌 괄호 ')'로 표현됩니다.
위 예의 괄호 표현은 그림 위에 주어져 있습니다.
쇠막대기는 레이저에 의해 몇 개의 조각으로 잘리는데, 위 예에서 가장 위에 있는 두 개의 쇠막대기는 각각 3개와 2개의 조각으로 잘리고, 이와 같은 방식으로 주어진 쇠막대기들은 총 17개의 조각으로 잘립니다.
쇠막대기와 레이저의 배치를 표현한 문자열 arrangement가 매개변수로 주어질 때, 잘린 쇠막대기 조각의 총 개수를 return 하도록 solution 함수를 작성해주세요.

#### HOW?
이 문제는 프로그래머스>코딩테스트연습>스택/큐 파트에 분류된 연습문제이다. 스택 문제의 기본인 '괄호짝짓기' 개념이나 문제를 풀어봤다면 쉽게 풀 수 있다.
이 문제 풀기 전에 괄호 짝짓기 문제 까먹어서, 다시 풀어봤는데 역시 알고리즘 문제는 한 번 풀었다고 끝! 이 아닌 것 같다😭

아래는 풀었던 문제 소스고, 레이저로 치환할 때 LinkedList를 굳이 쓸 필요는 없었던 것 같다. 그래도 삽입/삭제가 빠르니 그냥 써보기로.


```java
public static int solution(String arrangement) {

		int answer = 0;
		char[] laser = arrangement.toCharArray();
		LinkedList<Character> list = new LinkedList<Character>();

		// 닫힌 괄호 () 를 레이저 포인트 *로 치환해준다.
		list.add(laser[0]);
		for (int i = 1; i < laser.length; i++) {
			char curr = laser[i];
			char bef = laser[i - 1];
			if (bef == '(' && curr == ')') {
				list.removeLast();
				list.add('*');
			} else {
				list.add(curr);
			}
		}

		Stack<Character> stack = new Stack<Character>();
		int idx = 0;
		while (idx < list.size()) {

			int laserCnt = 0;
			char c = list.get(idx);
			if (c == '(' || c == '*') {
				stack.push(c);
			} else {

				while (true) {
					char el = stack.pop();
					if (el == '*') {
						laserCnt++;
					} else if (el == '(') {
						break;
					}
				}
				for (int i = 0; i < laserCnt; i++) {
					stack.push('*');
				}
				answer = answer + laserCnt + 1;
			}
			idx++;
		}

		return answer;
	}

```
