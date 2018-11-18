---
layout: post
title:  "SELECT CONT(*), COUNT(1)의 차이는?"
tags: [DB]
categories: [DB]
description: "SELECT COUNT(*)와 COUNT(1)의 차이를 알아보자"
---

COUNT(\*)의 COUNT(1) 차이는??
===========================  

결론부터 말하면 **차이가 없다**입니다.  

> ### You Asked  
> What is the difference between count(1) and count(\*) in a sql query  
> eg.  
> select count(1) from emp;  
> and  
> select count(\*) from emp;  
>
> ### and we said...  
> nothing, they are the same, incur the same amount of work -- do the same thing, take the same amount of resources.  

출처: [ORACLE Ask TOM](https://asktom.oracle.com/pls/asktom/f?p=100:11:0::NO::P11_QUESTION_ID:1156159920245)  

위 출처를 보면 동일한 수의 블록 읽기/쓰기/처리와 같은 CPU 사용 시간, 수행 시간을 갖는다고 합니다.  

---

그렇다면 COUNT(\*)과 COUNT(컬럼)은?  
========================  

이것도 결론부터 말하면 **차이가 존재한다**입니다. NULL 값에 대해서 카운팅을 하냐 안하냐의 차이가 존재합니다.  

> COUNT(컬럼) - NULL 값이 들어간 행은 카운트하지 않습니다.  
> COUNT(\*) - NULL 값에 상관없이 모든 행을 카운트합니다.  

사실 이 차이는 컬럼을 지정하여 COUNT 하는 경우 값이 NULL이면 세지 않는 게 당연합니다. 즉, 전체 행의 개수를 세려고 했다면 다른 결과가 나오는 게 정상입니다. 만약 NOT NULL인 컬럼이였다면 COUNT(*)과 동일한 결과 값(과정이 아닌 결과에 대해서만)이 나올 겁니다.  

다만, COUNT(*)과 COUNT(컬럼)은 사용 목적을 달리하는 게 맞습니다. COUNT(\*)의 경우 전체 행이 몇 개인지 세는 경우, COUNT(컬럼)은 해당 컬럼의 행이 몇 개인지 세는 경우로 구분하여 사용하는 게 옳은 사용법입니다.  

아무리 해당 컬럼이 NOT NULL인 경우라 해도 본인을 제외 한 다른 사람이 해당 쿼리를 봤을 때 전체 행의 갯수를 세기 위해 만든 쿼리란 걸 모를 확률이 높습니다.(심지어 반년 뒤의 자신 또한 포함해서 말이죠)

출처: [초보개발자꽁쥐](http://ggmouse.tistory.com/156)
