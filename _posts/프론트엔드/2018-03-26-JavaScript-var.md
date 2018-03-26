---
layout: post
title:  "[JavaScript] - var 선언 유무에 따른 차이"
tags: [프론트엔드, JavaScript]
categories: [프론트엔드]
description: "var를 선언하는 위치에 따라 어느 스코프에 생성이 되는지 보자"
---

var를 통한 변수 선언과 없는 경우의 차이
=================================

자바스크립트를 사용하다 보면 var 선언 없이도 변수를 사용 할 수 있습니다. 그런데 var 선언이 있는 경우와 없는 경우는 어떤 차이가 존재할까요??  

한번 보도록 하죠.  

---

첫번째 경우  
=========

일단 간단한 예제 코드와 출력 결과입니다.  

코드만 보고 먼저 결과를 생각해보세요.

```js
function foo(){
    var x = "foo";
    console.log("foo : " + x);
}

function bar(){
    x = "bar";
    console.log("bar : " + x);
}
foo();
bar();
console.log("global : " + x);
```

**출력**  

```
foo : foo
bar : bar
global : bar
```

예상하신 결과가 나왔나요??  

위의 코드에서 foo와 bar의 차이는 **var**로 x가 선언이 되어 있는지 아닌지입니다.  

일단 결론부터 말씀드리면 foo의 경우 foo 스코프안에 x가 생기지만, var가 없는 bar의 경우 현재 상태에서는 전역에 x라는 변수가 생성이 됩니다.  

```js
var x;
function bar(){
    x = "bar";
    console.log("bar : " + x);
}
```  

이런 상태인거죠.  

짧은 제 자바스크립트 지식으로 생각해보면 스코프 체인을 고려하여, x를 bar에서 사용하려고 하면  

- 현재 스코프에 x가 있는지 찾아보고 있으면 사용한다.  

- 없으면 상위 스코프에서 찾는다.(찾을 때까지 반복)   

- 최상위인 전역에서 찾아보고 여기도 없으면 x를 생성한다.  

위와 같은 과정을 걸치는 것 같습니다.

그럼 다시 처음 코드를 생각해보면

```js
var x;
function foo(){
    var x = "foo";
    console.log("foo : " + x);
}

function bar(){
    x = "bar";
    console.log("bar : " + x);
}
foo();
bar();
console.log("global : " + x);
```

이런 경우  "global : bar"가 출력 되는게 자연스럽죠.  

---

두번째 경우  
=========

```js
function foo(){
    var x = "foo";
    function bar(){
        console.log("bar : " + x);
    }   
    bar();
}

foo();
console.log("global : " + x);
```

**출력**  

```
bar : foo
console.log("global : " + x);
                           ^

ReferenceError: x is not defined
```

이번 같은 경우는 bar가 사용한 x는 foo의 스코프 안에 선언 되어 있는 x입니다.  

위에서 설명 드린 것 처럼 상위 스코프를 찾아 가보니 x가 존재하기 때문에 전역에 새로 x를 만들지도 않고 애초에 전역 스코프까지 가지도 않습니다.  

그렇기때문에 전역에는 x가 존재하지 않아서 에러가 발생합니다.  

bar에 있는 `var` 키워드를 지우면 아래처럼  

```js
function foo(){
    x = "foo";
    function bar(){
        console.log("bar : " + x);
    }   
    bar();
}

foo();
console.log("global : " + x);
```

```
bar : foo
global : foo
```

정상적으로 출력을 합니다.

---

마치며  
=====

var를 선언이 있으면 해당 스코프에 변수를 선언하여 사용 하는 것이고, 없다면 스코프 체인을 통해 가장 가까운 스코프에서 해당 변수를 찾고 만약 전역 스코프에서도 찾지를 못하면 동적으로 변수를 만들어 사용하게 됩니다.  

그렇기 때문에 거꾸로 생각하면 전역에서 var없이 사용을 하면 스코프 체인이 안 일어날 수도 있을 것 같네요.

혹시 헷갈리는 부분이 있으면 스코프(scope) 체인을 공부 해보시면 생각보다 쉽고 당연한 개념이란 걸 알수 있을겁니다. 그렇기 때문에 스코프 체인을 공부 하시는걸 추천드립니다.  

그리고 혹시 아직 아리송 한 부분이 있다면 아래 코드와 결과를 보여드리는 것으로 마무리 하도록 하겠습니다.  

```js
var x = "global";
function foo(){
    x = "foo";
    function bar(){
        x = "bar";
        console.log("bar : " + x);
    }   
    console.log("foo1 : " + x);
    bar();
    console.log("foo2 : " + x);
}

console.log("global1 : " + x);
foo();
console.log("global2 : " + x);
```

**출력**  

```
global1 : global
foo1 : foo
bar : bar
foo2 : bar
global2 : bar
```
