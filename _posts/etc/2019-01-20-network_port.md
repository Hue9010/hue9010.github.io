---
layout: post
title:  "netstat, lsof를 통해 특정 포트(port)를 사용 중인 프로세스의 pid 찾기"
tags: [리눅스, shell, etc]
categories: [etc]
description: "리눅스, 맥, 윈도우에서 특정 포트를 사용 중인 프로세스의 pid를 알아보자"
---

리눅스  
=====

```shell
$ netstat -nap | grep 8080

$ netstat -nap | grep LISTEN | grep 8080

$ lsof -i TCP:8080
```  

8080 위치에 확인하려는 포트 번호를 입력하면 됩니다.  
`netstat` 같은 경우는 두 가지를 적어뒀는데 `LISTEN` 상태만 확인을 하려면 두 번째 것을 사용하시면 됩니다.  

- netstat 옵션  

  ```
  -n, --numeric            don't resolve names
  -a, --all, --listening   display all sockets (default: connected)
  -p, --programs           display PID/Program name for sockets
  ```

- lsof 옵션  

  ```
  -i select IPv[46] files
  ```

추가로 `lsof`는 `list open files`를 의미합니다.

---

Mac OS  
========

```shell
$ lsof -i TCP:8080
```  

맥의 경우 `netstat`의 `p`옵션이 없기 때문에 `lsof`를 이용하시면 됩니다.  

---

윈도우  
======  

```shell
$ netstat -nao | findstr 8080
```

o 옵션 : 리눅스의 `p` 옵션처럼 pid 출력  

findstr : `grep`을 생각하면 된다  

**윈도우의 경우 리눅스에서의 명령어를 기억한 다음 `o` 옵션과 `findstr`만 기억하면 됩니다.**
