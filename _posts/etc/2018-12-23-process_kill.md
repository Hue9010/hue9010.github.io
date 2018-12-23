---
layout: post
title:  "리눅스에서 kill, pkill를 통해 특정 프로세스 종료시키기"
tags: [리눅스, 우분투]
categories: [etc]
description: "kill, pkill, pgrep을 이용해 프로세스를 죽이자"
---

## 리눅스에서 java 프로세스를 죽이려면?  

```shell
kill -9 `ps -ef | grep java | awk '{print $2}'`
pkill -9 -f java
```  

빠르게 명령어만 필요 하신 분이라면 위의 명령어를 가져다가 `java`대신 자신이 종료 시키려는 프로세스 이름을 넣어 사용하시면 됩니다 :)  

---

"내가 사용하는 명령어를 좀 더 제대로 알고 싶다.", "해당 명령어를 어떻게 변경해서 현재의 상황에 접목 시켜야 할지 모르겠다." 하시는 분들이라면 위의 명령어에 대해서 좀더 보도록 하죠!  

```shell
kill -9 `ps -ef | grep java | awk '{print $2}'`
```

## kill  

> kill -옵션 PID  

옵션 9는 SIGKILL로서 간단히는 kill명령어를 통해 무조건 강제종료 시키는 신호를 준다고 생각하시면 됩니다.  
[kill 위키](https://ko.wikipedia.org/wiki/Kill)

## ps  

> ps -옵션

현재 실행되고 있는 프로세스들을 표시하는 명령어  

-e : 모든 프로세스(-A와 같다)  
-f : full format으로 보여준다(자세히 보여준다)  

-ef에서 "-e"는 모든(every) 프로세스를 선별하고 "-f"는 완전한("full") 출력 포맷을 선택한다.  
[ps 위키](https://ko.wikipedia.org/wiki/Ps_(%EC%9C%A0%EB%8B%89%EC%8A%A4))

## grep  

텍스트 검색 기능을 가진 명령어  

> ps -ef | grep java  

위와 같은 경우는 `|` 리눅스 파이프를 통해서 앞에 있는 ps의 표준 출력이 grep의 표준 입력으로 사용 됩니다.  

1. ps의 결과로 모든 프로세스들의 목록을 가져온다.  
2. 그 목록들을 grep의 입력으로 사용한다.  
3. 입력받은 모든 프로세스 목록에서 java라는 텍스트가 있는 프로세스들에 대해서만 출력 해준다.  

[grep 위키](https://ko.wikipedia.org/wiki/Grep), [파이프](https://jdm.kr/blog/74)  

## awk  

> awk 'pattern'  
> awk '{action}'  
> awk 'pattern {action}'  


> awk '{print $2}'

아주 간단히 결과만 말하면 두번째 필드 변수만 출력하라는 것으로 `ps -ef`의 두번째 필드인 PID만 출력 해줍니다.  

[awk 위키](https://ko.wikipedia.org/wiki/AWK), [awk 제타위키](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_awk)  


합쳐서 보면  

> ps -ef | grep java | awk '{print $2}'  

1. ps의 결과로 모든 프로세스들의 목록을 가져온다.  
2. 그 목록들을 grep의 입력으로 사용한다.  
3. 입력받은 모든 프로세스 목록에서 java라는 텍스트가 있는 프로세스들만 고른다.
4. grep으로 나온 결과에서 두번째 필드(PID) 변수 값만 print한다.  

> kill -9 `ps -ef | grep java | awk '{print $2}'`

\`에 감싸여 있는 명령어로 나온 PID들을 종료 시킨다.  

결론은 실행 되어 있는 java 프로세스들을 검색하여 종료시키기 위해 여러 과정이 들어 간겁니다.  

이 복잡했던 과정을 좀 더 쉽게 할수도 있습니다.  

---  

## pkill  

```shell
pkill -9 -f PROCESS_NAME
```  

`pkill` 명령어는 확장된 정규 표현식 패턴들의 사용을 허용합니다.  

 -f : 모든 아규먼트를 비교한다.  
 (Match against full argument lists.  The default is to match against process names.)  

 kill의 경우와 같이 `ps`, `grep`의 과정 없이 바로 java 프로세스를 찾아 종료 시킬 수 있습니다.  
[pkill 위키](https://ko.wikipedia.org/wiki/Pkill)  

위에서도 간단히 언급 했지만 사실 kill, pkill이 프로세스를 종료 시키는게 아니라 특정 시그널(9는 SIGKILL로 프로세스 강제 종료)을 보내주는 역할을 합니다.  
