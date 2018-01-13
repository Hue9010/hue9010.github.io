---
layout: post
title:  "jQuery에서 Ajax 사용하기"
tags: [프론트엔드, jQuery, Ajax, JSON]
categories: [프론트엔드]
description: "자주 안쓰니 까먹는다! jQuery에서 Ajax를 사용하는 방법을 기록해 두자."
---

한두 번씩 프론트 작업을 할 때 어려운 것이 아니고, 이전에 적용해 본 것도 아직 익숙지 않다 보니 시간을 많이 쏟을 때가 있다. 그러한 시간을 줄이기 위해서 기록을 해둔다.  

jQuery의 Ajax
================

일단 간단하게 jQuery의 ajax에 설정하는 값에 대해 보자.

```js
$.ajax({
  type : `http method type`,
  url : `url`,
  data : `서버에 전송할 데이터`,
  contentType : "전송할 데이터 타입",
  //기본 값 : "application / x-www-form-urlencoded; charset = UTF-8"  
  dataType : '서버로 부터 수신할 데이터 타입',
  //아무것도 지정하지 않으면 jQuery는 응답의 MIME 유형을 기반으로 해석을 시도
  error : `에러 발생시 수행할 함수`,
  success : `성공시 수행할 함수`
});
```  

좀 더 상세한 설명은 [여기서](http://api.jquery.com/jquery.ajax/) 볼 수 있다.  

(추가로 jQuery에서 `.`은 클래스, `#`은 아이디)

---

@RequestBody 사용시 적용법
=========================

현재 만들고 있는 트렐로 프로젝트에서 사용하고 있는 코드다.

```java
@PostMapping("")
public ResponseEntity<Void> login(@RequestBody UserDto userDto, HttpSession session) {
  session.setAttribute("loginedUser", userService.login(userDto.toUser()));
  HttpHeaders headers = new HttpHeaders();
  return new ResponseEntity<Void>(headers, HttpStatus.OK);
}
```  

`@RequestBody`를 사용하여 JSON 타입의 데이터로 값을 받아온다. (참고로 `RestController`다)  

그렇기 때문에 클라이언트에서 JSON 타입으로 데이터를 보내주어야 한다.  

```js
$(".login-form button[type=submit]").click(login);

function login(e) {
  //원래 동작해야 할 이벤트를 중지 시킨다.  
	e.preventDefault();

  //유저가 입력한 정보를 js 오브젝트로 만든다.  
	var data = {
		'email' : $('#email').val(),
		'password' : $('#password').val()
	};

  //위에서 만든 오브젝트를 json 타입으로 바꾼다.
	var json = JSON.stringify(data);
	var url = $(".login-form").attr("action");

	$.ajax({
		type : 'post',
		url : url,
		data : json,
		contentType : "application/json; charset=utf-8",
		error : onError,
		success : onSuccess
	});
}
```  

서버에서 JSON 데이터를 받기 때문에 JSON으로 데이터를 변환하였고, contentType을 JSON 설정하였다.(서버 기준으로 작업)  

---  

@RequestBody 사용을 안 할때
=========================

아주 간단하게 데이터 타입들만 맞추면 된다.  

```java
@PostMapping("")
public ResponseEntity<Void> login( UserDto userDto, HttpSession session) {
  session.setAttribute("loginedUser", userService.login(userDto.toUser()));
  HttpHeaders headers = new HttpHeaders();
  return new ResponseEntity<Void>(headers, HttpStatus.OK);
}
```

```js
function login(e) {
	e.preventDefault();

	var data = $(".login-form").serialize();
	var url = $(".login-form").attr("action");

	$.ajax({
		type : 'post',
		url : url,
		data : data,
		error : onError,
		success : onSuccess
	});
}
```
