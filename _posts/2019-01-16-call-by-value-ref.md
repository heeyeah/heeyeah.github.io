---
layout: post
title:  "[Programming Basic] Pass by value, Pass by reference에 대해"
date:   '2019-01-16 11:40:00'
author: Heeye
categories: ProgrammingBasic
tags: programming basic
---

pass by value, pass by reference가 보던 책에 나오는데, call by value, call by reference와 비슷한 것 같아서 정리한다. ~~(결론은 똑같다. pass나 call이나)~~

<br/>
### Call by Value
**함수에 넘겨준 어떤 argument든 함수가 반환될 때 caller의 스코프에선 아무것도 변하지 않는다.**<br/>
*Call by value (also referred to as pass by value) is the most common evaluation strategy, used in languages as different as C and Scheme. In call by value, the argument expression is evaluated, and the resulting value is bound to the corresponding variable in the function (frequently by copying the value into a new memory region). If the function or procedure is able to assign values to its parameters, only its local variable is assigned—<span style="color:#ff6d70">that is, anything passed into a function call is unchanged in the caller's scope when the function returns.
</span>*


예를 들어 A와 B 함수가 있고, A는 B를 호출한다고 가정하자.
 A는 Caller function, B는 Callee function, A가 B에 전달하는 파라미터는 actual argument, B의 파라미터는 formal argument이다. call by value의 경우는 actual argument는 formal argument를 초기화하는 데 사용되고, formal argument는 스택 상에 지역 변수와 같이 할당돼서 호출자로 재전달될 수 없다.

+ 장점은 actual argument가 변경없이 유지 된다는 것이고
+ 단점은 저장공간 할당에 의한 비효율성, 값 복사에 의한 비효율성, 객체와 배열에 대한 복사는 비용이 많이 들어간다는 것이다.

```java
public static void main(String[] args) {
  int x = 3, y = 5;
  func(x, y);
  System.out.printf("x: %d, y: %d \n", x, y);
}

public static void func(int a, int b) {
  a = a + b;
  b = 7;
  System.out.printf("a: %d, b: %d \n", a, b);
}
```
결과 값은
```x: 3, y: 5```
```a: 8, b: 7```

main에서 값을 넘겨주고 func이라는 callee function에서 받은 값이 연산이 돼도, main인 caller function에는 값의 변화가 없다는 사실😌

<br/>
### Call by reference
**Call by reference는 값 자체를 전달하는 게 아니라, argument로 사용된 변수의 레퍼런스(address)를 전달하는 것이다. 그래서 함수가 argument로써 사용한 variable를 변경시킬 수 있다는 개념이다. 그게 Caller의 variable이라도.**<br/>
*Call by reference (also referred to as pass by reference) is an evaluation strategy where a function receives an implicit reference to a variable used as argument, rather than a copy of its value. <span style="color:#ff6d70">This typically means that the function can modify (i.e. assign to) the variable used as argument—something that will be seen by its caller. </span>Call by reference can therefore be used to provide an additional channel of communication between the called function and the calling function. A call-by-reference language makes it more difficult for a programmer to track the effects of a function call, and may introduce subtle bugs. A simple "litmus test" for whether a language supports call-by-reference semantics is if it's possible to write a traditional swap(a,b) function in the language.*

<br/>
### Call by value, Call by reference 예제
```java

public class Main {

	public static void main(String[] args) {

		int val1 = 10;
		int val2 = 20;
		RefClass ref1 = new RefClass(10);
		RefClass ref2 = new RefClass(20);

		System.out.printf("[before] val1 : %d, val2 : %d, ref1 : %d, ref2 : %d \n",
     val1, val2, ref1.value, ref2.value);

		swapVal(val1, val2);
		swapRef(ref1, ref2);

		System.out.printf("[after ] val1 : %d, val2 : %d, ref1 : %d, ref2 : %d \n",
     val1, val2, ref1.value, ref2.value);

	}
	public static void swapVal(int x, int y) {
		int temp = x;
		x = y;
		y = temp;
	}

	public static void swapRef(RefClass x, RefClass y) {
		int temp = x.value;
		x.value = y.value;
		y.value = temp;
	}
}

class RefClass {

	public int value;

	public RefClass(int value) {
		this.value = value;
	}
}
```
```java
// 결과값
[before] val1 : 10, val2 : 20, ref1 : 10, ref2 : 20
[after ] val1 : 10, val2 : 20, ref1 : 20, ref2 : 10
```
<br/>
### 정리하자면
>pass by value는 actual value를 넘겨주는 것이고, pass by reference는 value값이 저장된 memory address를 넘겨주는 것

*The terms "pass by value" and "pass by reference" are used to describe the two ways that variables can be passed on. Cliff notes version: : pass by value means the actual value is passed on. Pass by reference means that a number (called a memory address) is passed on, this address defines where the value is stored.*

<br/>
### Java는 pass by value, pass by reference?
결론은 pass by value 이고, 그럼 위에서 pass by reference를 자바 예제로 설명한 건 뭔가 싶겠지만 위 예제에서 ```swapRef``` 메소드의 내용을 아래와 같이 바꾸면 이해할 수 있다.
```java
public static void swapRef(RefClass x, RefClass y) {
	RefClass tempClass = x;
	x = y; // y를 assign하지만 이 scope 안에서만 사용. x에 직접 접근해서 값을 변경하지 않음.
	y = tempClass;
}
```
이 글을 보면 조금 더 이해가 잘 될 것 같다. [자바는CallByValue인가요뭔가요](https://hashcode.co.kr/questions/4/자바에서-메소드-호출할때-매개변수가-call-by-reference인가요-아니면-call-by-value-인가요)

[참고]
+ https://en.wikipedia.org/wiki/Evaluation_strategy
+ 코딩인터뷰 퀘스천
