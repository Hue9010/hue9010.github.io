---
layout: post
title:  "SpringBoot에서 애노테이션을 활용한 예외 처리"
tags: [Spring, Exception, ExceptionHandler]
categories: [Spring]
description: "SpringBoot에서 ExceptionHandler를 통해 예외 처리를 해보자"
image:
  feature: header/java-spring-logo.png
---

스프링 부트에서 예외 처리 하는 세가지 방법 ([출처](http://www.ekiras.com/2016/02/how-to-do-exception-handling-in-springboot-rest-application.html))

- #### Global Level using -  @ControllerAdvice  
- #### Controller Level using - @ExceptionHandler   
- #### Method Level using - try/catch    



---

## Controller  

```java
@Controller
public class UserController {
  @ExceptionHandler(UnAuthenticationException.class)
  @ResponseStatus(value = HttpStatus.UNAUTHORIZED)
  public void unAuthentication() {
    log.debug("UnAuthenticationException is happened!");
  }
}
```

- 위의 방법은 Controller 내부에서 예외가 발생하면 처리한다.  

- `@ExceptionHandler`를 통해 UnAuthenticationException에 대한 예외 처리를 한다.  

- `@ResponseStatus`를 통해 UNAUTHORIZED(401) 상태 값을 응답 한다.  

---

## ControllerAdvice  

```java
@ControllerAdvice
public class SecurityControllerAdvice {
  @ExceptionHandler(UnAuthenticationException.class)
  @ResponseStatus(value = HttpStatus.UNAUTHORIZED)
  public void unAuthentication() {
    log.debug("UnAuthenticationException is happened!");
  }
}

```  

- 위의 방법은 모든 컨트롤러에 적용 된다.  
