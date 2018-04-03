---
layout: post
title:  "GET/POST 요청에 따른 테스트 코드 작성하기"
tags: [Spring, Spring Security]
categories: [Spring]
description: "GET/POST 테스트 코드를 작성 해보자"
image:
  feature: header/java-spring-logo.png
---

시작하며..  
=========

간단한 Post, Get 요청에 따른 테스트 코드를 보는 시간을 가지도록 하겠습니다.  

이론적인 부분은 제외하고 간단한 틀을 보는 형태로 복잡한 요청/응답이 필요한 상황이 아니라면 간단한 응용만으로 자신의 프로젝트에 이용 가능 하실 겁니다.

테스트 코드만 살펴 볼 계획이기 때문에 컨트롤러 및 스프링을 통한 웹 애플리케이션 구현 코드는 테스트 코드를 보고 직접 작성 하시기 바랍니다.(여차하면 추후 추가하도록 하겠습니다.)

**REST api 테스트를 하고 싶다면 [REST-assured](https://www.swtestacademy.com/api-testing-with-rest-assured/)의 사용을 추천드립니다. 개인적인 생각엔 api에 대한 테스트 코드를 작성하는 점에서 REST-assured가 JSON 데이터를 다루는 방법 등 여러가지 면에서 더 편리하게 사용 가능합니다.(추후 기회가 된다면 포스팅 하도록 하겠습니다.)**

요구사항  
=======

아주 간단히 로그인, 회원가입 기능만 있고, 로그인과 회원가입 성공시에는 `/`로 리다이렉트 하고 로그인 실패시에는 `login_failed`로 이동하면 됩니다. 추가로 `index` 페이지에서는 로그인, 회원가입 페이지로만 이동이 가능합니다.  

### index  

- `/users/create`로 `GET` 요청, 회원가입 페이지 이동  

- `/users/login`으로 `GET` 요청, 로그인 페이지 이동  

### 회원가입  

- `/users/create`로 `POST` 요청, 회원 가입 성공 시 `/`로 redirect  

### 로그인  

- `/users/login`로 `POST` 요청, 로그인 성공시 `/`로 redirect, 실패시 `login_failed.html` 전송

HTML form
==========

일단 보여질 페이지가 중요한게 아니기때문에 html은 단순하게 작성했습니다.

### index.html
```java
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<a href="users/login">로그인</a>
	<a href="/users/create">회원가입</a>
</body>
</html>
```

### create.html

```html
<form name="create" method="post" action="/users/create">
    <label for="username">사용자 아이디</label> <input class="form-control"
      id="username" name="username" placeholder="User ID">
    <label for="password">비밀번호</label> <input type="password"
      class="form-control" id="password" name="password"
      placeholder="Password">
    <label for="email">이메일</label> <input type="email"
      class="form-control" id="email" name="email" placeholder="Email">
  <button type="submit" class="btn btn-success clearfix pull-right">회원가입</button>
</form>
```


### login.html

```html
<form name="login" method="post" action="/users/login">
    <label for="username">사용자 아이디</label> <input class="form-control"
      id="username" name="username" placeholder="User ID">
    <label for="password">비밀번호</label> <input type="password"
      class="form-control" id="password" name="password"
      placeholder="Password">
  <button type="submit" class="btn btn-success clearfix pull-right">로그인</button>
  <div class="clearfix" />
</form>
```

가입의 경우 : `username`, `password`, `email`  

로그인의 경우 : `username`, `password`  

---

테스트 코드 작성  
==============

테스트 코드는 아래와 같은 애노테이션을 사용한 클래스에서 작성 하면 됩니다.

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class UserAcceptanceTest {
	private static final Logger log = LoggerFactory.getLogger(MemberAcceptanceTest.class);

	@Autowired
	private TestRestTemplate template;

  ...

}
```
`webEnvironment = WebEnvironment.RANDOM_PORT` : 현재 사용하지 않고 있는 port 중에서 임의로 할당 해 줍니다.  

참고로 포트를 할당 해주는 것을 보면 아시다시피 서버를 키고 실제 서버에 요청을 하는 방식으로 테스트가 진행이 됩니다. 그렇기 때문에 일반적인 테스트보다 시간이 걸리는 편입니다.


GET  
====

```java
@Test
public void createForm() {
  ResponseEntity<String> response = template.getForEntity("/users/create", String.class);
  assertThat(response.getStatusCode(), is(HttpStatus.OK));
  log.debug("body : {}", response.getBody());
}

@Test
public void loginForm() {
  ResponseEntity<String> response = template.getForEntity("/users/login", String.class);
  assertThat(response.getStatusCode(), is(HttpStatus.OK));
  log.debug("body : {}", response.getBody());
}
```

> **getForEntity(uri,반환될 객체 타입)**  


`log.debug("body : {}", response.getBody())`는 불필요시 지워도 되며, 응답의 body를 확인 하기 위한 용도입니다. 만약 Logger을 잘 모르신다면 `System.out.println`으로 출력을 해도 됩니다.  

혹은,  

```java
assertThat(response.getBody().contains("특정 내용"), is(true));

//만약 회원 프로필 상세 페이지라면 유저 이메일이 body에 있는지 확인 해 본다.
assertThat(response.getBody().contains(loginUser.getEmail()), is(true));
```

body에 특정 내용이 포함 되어 있는지 테스트 코드를 작성 할 수도 있습니다.  

POST  
=====

```java
@Test
public void create() throws Exception {
  // 헤더 세팅
  HttpHeaders headers = new HttpHeaders();
  headers.setAccept(Arrays.asList(MediaType.TEXT_HTML));
  headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

  // 자신이 작성한 form에 맞추어 값들을 할당 하면 됩니다.
  MultiValueMap<String, Object> params = new LinkedMultiValueMap<>();
  params.add("username", "testUser");
  params.add("password", "password");
  params.add("email", "tester@korea.kr");

  // request 생성
  HttpEntity<MultiValueMap<String, Object>> request = new HttpEntity<MultiValueMap<String, Object>>(params, headers);

  ResponseEntity<String> response = template.postForEntity("/users/create", request, String.class);

  assertThat(response.getStatusCode(), is(HttpStatus.FOUND));

  // 원한다면 Dao 혹은 Repository를 통해 실제 db에 데이터가 저장 되었는지도 확인 할 수 있습니다.
  // assertNotNull(userRepository.findByUserName("testUser"));

  // 메인 페이지로 redirect 하는 경우 이와 같이 확인 할 수 있습니다.
  assertThat(response.getHeaders().getLocation().getPath(), is("/"));
}

@Test
public void loginSuccess() throws Exception {
  HttpHeaders headers = new HttpHeaders();
  headers.setAccept(Arrays.asList(MediaType.TEXT_HTML));
  headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

  MultiValueMap<String, Object> params = new LinkedMultiValueMap<>();
  // testUser가 db에 저장되어 있다는 가정하에 수행
  params.add("username", "testUser");
  params.add("password", "password");

  HttpEntity<MultiValueMap<String, Object>> request = new HttpEntity<MultiValueMap<String, Object>>(params, headers);

  ResponseEntity<String> response = template.postForEntity("/users/login", request, String.class);

  assertThat(response.getStatusCode(), is(HttpStatus.FOUND));
  // 세션을 활용 한다면 jsessionid가 추가되는지 여부로 login 유무를 확인 할 수 있다.
  assertTrue(response.getHeaders().getLocation().getPath().contains("/;jsessionid="));
}
```

> **template.postForEntity(url, Request, 반환될 객체 타입)**  

GET과 다른 과정은 header와 body에 `MultiValueMap<String, Object> params`로 body에 필요한 내용들을 생성하고, 그 두개를 활용하여 Request를 만든다는 것입니다.  

현재 중복이 많이 일어나고 있긴 하지만 중복을 줄인 코드보다는 현재의 코드가 무엇이 필요한지 알기엔 더 나을 것 같아 그대로 놔뒀지만, 실제 작성 하실 때는 assert**위로는 중복을 제거하시는게 수월 하실 겁니다.

로그인 실패 케이스 만들기  
======================

### login_failed.html

```html
<h1>아이디 혹은 비밀번호 틀립니다.</h1>
<form name="login" method="post" action="/users/login">
    <label for="username">사용자 아이디</label> <input class="form-control"
      id="username" name="username" placeholder="User ID">
    <label for="password">비밀번호</label> <input type="password"
      class="form-control" id="password" name="password"
      placeholder="Password">
  <button type="submit" class="btn btn-success clearfix pull-right">로그인</button>
  <div class="clearfix" />
</form>
```

```java
@Test
public void loginOtherPassword() {
  HttpHeaders headers = new HttpHeaders();
  headers.setAccept(Arrays.asList(MediaType.TEXT_HTML));
  headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);

  MultiValueMap<String, Object> params = new LinkedMultiValueMap<>();
  // testUser가 db에 저장되어 있다는 가정하에 수행
  params.add("username", "testUser");
  //틀린 패스워드 사용
  params.add("password", "otherPassword");

  HttpEntity<MultiValueMap<String, Object>> request = new HttpEntity<MultiValueMap<String, Object>>(params, headers);

  ResponseEntity<String> response = template.postForEntity("/users/login", request, String.class);
  assertThat(response.getStatusCode(), is(HttpStatus.OK));
  assertTrue(response.getBody().contains("틀립니다."));
}
```

현재 실패 케이스는 한개만 만들었지만 실제로는 더욱 많은 케이스들을 만들어야 됩니다. 테스트 코드를 작성하는 초기엔 성공 케이스 위주로 작성하기 쉬운데 실패 케이스들을 소홀히 하다 보면 예상치 못한 예외 상황이 발생 할 확률이 높아 집니다.

---

마치며...
=========

Controller 및 웹 애플리케이션에 대한 구현 코드는 없다보니 많이 빈약하지만 해당 Test 코드에 대한 정보를 찾는 분이라면 이정도 기능을 구현하는 것 자체에는 어려움이 없을 것 같다는 판단에 작성을 하지 않았습니다.  

게다가 요구사항과 테스트 코드만 가지고 직접 만들어 보는게 더 의미도 클 것이라고 생각이 들었고, 위의 상황과 같은 경우는 너무 간단하다보니 이정도 선의 테스트 코드가 필요한 분이라면 약간의 변형을 해서 사용이 가능 할 것이라 생각됩니다.

(지속적으로 학습 하면서 더 나은 포스팅을 하도록 노력 하겠습니다.)
