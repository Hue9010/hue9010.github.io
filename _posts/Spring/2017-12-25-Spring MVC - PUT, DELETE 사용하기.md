---
layout: post
title:  "Spring MVC - PUT, DELETE 사용하기"
tags: [Spring, MVC, 스프링, PUT, DELETE, HTTP, Java]
categories: [Spring]
description: "Spring MVC에서 PUT, DELETE 메소드를 사용해보자"
image:
  feature: header/java-spring-logo.png
---
### Spring에서 PUT, DELETE를 사용해보자

 HTML Form으로는 GET, POST 방식의 요청만 가능하고, PUT,DELETE의 실제 요청은 POST로 전송되기 때문에 단순히 `method="PUT"` 같이 작성한다고 PUT, DELETE 메소드 사용이 안된다.  

 이를 위해 form 내부의 <input type="hidden" name="\_method" value="PUT"> 같이 hidden 타입의 input을 작성하고, httpMethodFilter를 이용하여 request로부터 정보를 읽어와 PUT, DELETE로 구분한다.

---

### 자 그러면 PUT, DELETE 메소드가 사용 가능하게 하는 방법을 보도록 하겠다.  


- PUT, DELETE를 사용하고 싶은 form에 hidden 타입의 input을 작성 한다.

  ```html
  <form class="form-delete" action="/questions/${question.questionId}" method="post">
    <input type="hidden" name="_method" value="delete" />
    <button class="link-delete-article" type="submit">삭제</button>
  </form>
  ```

- 이후 WebApplicationInitializer 인터페이스 구현 또는 web.xml 파일을 생성한 후 HiddenHttpMethodFilter를 설정해야 한다.  

  >### WebApplicationInitializer란?   
  >ServletContext은 Servlet 3.0+ 환경에서 구현되는 인터페이스로 기존 web.xml기반 접근 방식과 반대되는 방식이다. 또는 동시에 적용 가능하다. ([docs.spring](https://docs.spring.io/spring/docs/3.1.0.M2/javadoc-api/org/springframework/web/WebApplicationInitializer.html))  

  ```java
  public class MyWebInitializer implements WebApplicationInitializer {
  	@Override
  	public void onStartup(ServletContext servletContext) throws ServletException {
      //생략
  		servletContext.addFilter("httpMethodFilter", HiddenHttpMethodFilter.class)
  				.addMappingForUrlPatterns(EnumSet.allOf(DispatcherType.class), false, "/*");
      //생략
  }
  ```

  web.xml을 이용하고 싶다면..

  ```xml
  <!-- HTTP Method Filter -->
  <filter>
      <filter-name>httpMethodFilter</filter-name>
      <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
      <filter-name>httpMethodFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

  그러면 Controller단에서 GET뿐만 아니라 PUT, DELETE까지 사용이 가능해진다.  

  ```java
  @RequestMapping(value = "/{questionId}", method = RequestMethod.GET)

  @RequestMapping(value = "/{questionId}", method = RequestMethod.PUT)

  @RequestMapping(value = "/{questionId}", method = RequestMethod.DELETE)  
  ```  

  이처럼 생각보다 간단하다.
