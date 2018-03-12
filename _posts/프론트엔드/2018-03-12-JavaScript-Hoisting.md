---
layout: post
title:  "JavaScript - 호이스팅(hoisting)"
tags: [프론트엔드, JavaScript]
categories: [프론트엔드]
description: "호이스팅에 대한 간단한 정리"
---

호이스팅(hoisting)이란??  
======================

호이스팅의 뜻은 네이버 백과사전을 보면  

> hoist : (흔히 밧줄이나 장비를 이용하여) 들어[끌어]올리다  

즉, `끌어 올리다` 입니다.  

단어의 뜻처럼 호이스팅은 변수의 선언을 위로 끌어올립니다.  

```js
console.log(foo);
var foo = "foo";
console.log(foo);
```

### 출력 :

> undefined  
> foo  

위와 같이 동작하는 원리는 자바스크립트 엔진에 의해서  

```js
var foo;
console.log(foo);
foo = "foo";
console.log(foo);
```

이와 같은 코드로 실행이 됩니다. (위의 코드를 보면 출력 결과가 납득이 될 겁니다.)  

단, 분명히 `var foo = "foo";` 로 작성했는데 `foo`가 출력 안된 점에서 의아해하실 수도 있습니다.  

그 원인은 위에서도 말했다시피 변수의 `선언`을 위로 올리는 것이지 `= "foo"`에 해당하는 할당 구문은 위로 올라가지 않습니다.  

---

Scope를 신경쓰자!!  
================

한가지 더 조심해야 할 점은 선언된 해당 Scope에서 최상위에 위치한다는 점입니다.  

```js
console.log(foo);
var foo = "foo";
console.log(foo);

goo();

function goo(){
  console.log(foo);
  var foo = "goo";
  console.log(foo);
}
```

### 출력 :

> undefined  
> foo  
> undefined  
> goo  

위와 같이 실제 어떤 식으로 실행되는지 보면  

```js
var foo;
console.log(foo);
foo = "foo";
console.log(foo);

goo();

function goo(){
  var foo;
  console.log(foo);
  foo = "goo";
  console.log(foo);
}
```

이렇게 됩니다.  

근데 `goo()`가 어떻게 정상적으로 실행됐는지 헷갈리는 분들도 있겠죠???  

바로 아래서 설명을 이어나가도록 하겠습니다.  

---

함수의 호이스팅  
============

이번엔 함수의 선언을 보죠.  

```js
foo();

function foo(){
  console.log("hi!!");
}
```

### 출력 :  

> hi!!  

이것도 위와 같이  

```js
function foo(){
  console.log("hi!!");
}

foo();
```

이렇게 실행됩니다.  

그래서 위에 있는 `goo()`가 제대로 실행될 수 있었던 겁니다.

---

var foo = function(){} 같은 경우는??  
====================================

```js
foo();

var foo = function(){
  console.log("hi!!");
}
```

이렇게 작성하면 에러가 납니다.  

> (TypeError: foo is not a function)  

원인은 맨 처음 말씀드린 것처럼 선언이 위로 올라가기 때문에  

```js
var foo;

foo();

foo = function(){
  console.log("hi!!");
}
```

이와 같은 형태가 됩니다.  

`()`는 함수를 실행시킬 때 사용하죠. 하지만 `foo`에는 아직 함수가 할당되어 있지 않습니다. 그렇기 때문에 에러가 납니다.

---

좀더 정확히는  
===========

"함수 표현식"으로 정의된 함수는 호이스트 되지 않습니다. "함수 선언문"만 호이스트 됩니다.  

- ### 함수 선언식  
  ```js
  function 함수명() {
    ...
  }
  ```  

- ### 함수 표현식  
  ```js
  var 함수명 = function () {
    ...
  };
  ```

보면 함수 표현식의 경우 `var 함수명`에 함수를 할당해 주는 형태입니다.

그럼 위에서 설명 한 것처럼 할당은 그 자리에 남아 있을 테니 구현식, 표현식이라는 단어를 떠나서 생각해봐도 호이스팅이 어떻게 일어날지 유추를 해 볼 수 있습니다.  

---

마치며  
=====

호이스팅(hoisting)은 선언되어 있는 변수 및 함수가 해당 Scope에서 최상위에 위치하는 걸 뜻합니다.  

즉, 호이스팅을 알기 위해 중요한 것은 `선언`과 `할당`의 구분과 끌어 올려지는 위치에 관여하는 `scope`입니다.  
