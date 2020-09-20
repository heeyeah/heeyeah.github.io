---
title:  "[Spring] @ControllerAdvice와 AOP"
date:   '2019-03-24 16:00:00'
category: spring
draft: false
---


### ControllerAdvice

Our goal is to not handle exceptions explicitly in Controller methods where possible. They are a cross-cutting concern better handled separately in dedicated code. <br/>
[출처-Exception Handling in Spring MVC](https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc)

<br/>
Spring의 기본적인 개념(IoC, DI, AOP) 중에 하나인 AOP를 잘 이해할 수 있는 기능이라고 생각한다. todo-list-project를 단시간에 구현하면서, logging에 대한 것도 AOP 개념을 이해하고 사용하면 Controller마다 logging을 작성해야하는 수고로움을 덜수 있을 것이었다. ~~그건 추후에 고쳐나가기로 한다.💭~~

Spring 3.2부터 제공하는 @ControllerAdvice 는 클래스의 경로를 검색해서 오류를 캐치할 구현 클래스를 만들게 도와주는 것이다. 일반적으로 @Controller 류가 선언된 클래스들에서 발생한 예외를 감지하고 적절한 응답을 만들어 낼 때 사용한다.

**간단하게 생각하면, 비즈니스 로직에서 Exception 던질 때 마다 메세지, 공통로직 처리 등 공통으로 해야할 부분을 @ControllerAdvice 가 달린 클래스에 정의해서 처리해준다고 생각하는 게 쉽다.** Exception을 handling하는 방법에는 per Exception, per controller or globally 3가지가 존재한다. 내가 사용하고 구현했던 건 Globally한 방법이고 **정말 간단하게** 구헌했고 소스는 여기있다. [github](https://github.com/heeyeah/todo-list-project)

예외처리 케이스들을 모아놓은 클래스- ControllerAdvice 어노테이션이 붙어있다!
```

@ControllerAdvice
public class GlobalExceptionHandling {

	private Logger logger;

	public GlobalExceptionHandling() {
		logger = LoggerFactory.getLogger(getClass());
	}

	@ExceptionHandler(value = { InternalServerException.class })
	@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
	@ResponseBody
	public ResponseEntity<?> handleInternalServerException(Exception ex) {
		logger.error(" handleInternalServerException. {}", ex.getMessage());
		return errorResponse(ex, HttpStatus.INTERNAL_SERVER_ERROR);
	}


  ...(생략)...

	public ResponseEntity<TodoResponseDto> errorResponse(Throwable throwable, HttpStatus status) {
		TodoResponseDto res = new TodoResponseDto();
		res.setResponseCode(TodoResponse.FAIL);
		res.setResponseMessage(throwable.getMessage());
		return new ResponseEntity<TodoResponseDto>(res, status);
	}
}

```

*InternalServerException 클래스는 Exception을 extend한 커스텀 클래스이다. (내가 만든)*


Service 단에서 ```throw new InternalServerException("미완료인 태그가 존재합니다. => " + incompleteTodoSet);``` 이렇게 Exception을 던지면 해당 예외를 catch해서 처리한다. `@ResponseStatus`에 정의해둔 대로 500 error를 return할 것이며, ```@ResponseBody```에 정의해놓은 대로 ResponseEntity에 status와 Exception을 발생시킬 때 입력한 message인 ```미완료인 태그가 존재합니다. => [tagset]```을 함께 response로 반환해 줄 것이다.

AOP의 개념을 누군가에게 설명해줄 만큼 명확하게 정리되어 있진 않지만, ```@ControllerAdvice```를 사용하면서 어떤 개념인지 감을 잡아서 일단 그걸로 됐다,,❤️

<br/>
<br/>
<br/>

### Spring의 기본개념
*대략적인 Spring의 기본 개념을 정리하고 싶어 간단히 메모를 남긴다😌*

#### IoC : Inversion of Control - 제어의 역전

Spring 컨테이너를 보고 IoC 컨테이너라고 많이들 정의한다. 그럼 Spring 컨테이너가 뭔지 알면 IoC를 이해할 수 있지 않을까?

컨테이너는 보통 인스턴스의 생명주기를 관리하며, 생성된 인스턴스들에게 추가적인 기능을 제공하도록하는 것이라 할 수 있다. 다시말해, 컨테이너란 당신이 작성한 코드의 처리과정을 위임받은 독립적인 존재라고 생각하면 된다. 컨테이너는 적절한 설정만 되어있다면 누구의 도움없이도 프로그래머가 작성한 코드를 스스로 참조한 뒤 알아서 객체의 생성과 소멸을 컨트롤해준다. [출처](https://limmmee.tistory.com/13)

내가 이해하기로 Spring컨테이너는 Bean을 관리하는 컨테이너라고 생각하면 이해가 쉽다. 어떤 기능을 구현하고 싶어서 Bean으로 구현하고 어플리케이션을 구동시켰을 때, 컨테이너는 내가 구현한 Bean을 생성하고 소멸시키며 책임을 지는 아이이다. 빈 자체가 필요하기 전까지는 인스턴스화를 하지 않는다. *(lazy loading 설정을 다르게 할 수도 있지만)*

내가 구현한 Bean을 호출해서 사용하고 싶을 때, 어떻게 할까?
```
MyCustomBean customBean = ApplicationContext.getBean(MyCustomBean.class);
```

이런식으로 사용하는데, 여기서 **의존성 주입** 의 개념을 볼 수 있다. IoC와 DI는 따로 떨어진 개념보다는 연관이 된 개념이라고 생각하고 봐야 한다. 그럼 의존성 주입은 뭐지?


#### DI : Dependency Injection - 의존성 주입
Dependency Injection은 Spring Framework에서 지원하는 IoC의 형태.
*객체를 직접 생성하는 게 아니라 외부에서 객체를 주입시켜 사용하는 방식* 으로 이해해도 될 것 같기도 하다. 의존성을 주입해서 Bean을 만들 때, 각 Bean의 생성자가 팩토리 내부에 있는 게 아닌 getBean할 때 **이거 생성해줘!** 를 부탁해서 스프링 컨테이너가 생성된 인스턴스의 생명주기를 제어할 수 있게 해주는 것이 아닐까?

DI도 IoC와 같이 Spring에서만 쓰는 개념이 아니다. 프로그래밍에서 구성요소간의 의존 관계가 소스코드 내부가 아닌 외부의 설정파일 등을 통해 정의되게 하는 디자인 패턴 중의 하나이다. [위키피디아 정의](https://ko.wikipedia.org/wiki/%EC%9D%98%EC%A1%B4%EC%84%B1_%EC%A3%BC%EC%9E%85)


#### AOP : Aspect Oriented Programming - 관점 지향 프로그래밍
AOP는 관점 지향 프로그래밍으로 "기능을 핵심 비즈니스 기능과 공통 기능으로 '구분'하고, 공통 기능을 개발자의 코드 밖에서 필요한 시점에 적용하는 프로그래밍 방법"이다.


간단히라도 정리하니 기분이 좋당🤩🤩🤩
