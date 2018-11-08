---
layout: post
title:  "우분투 내의 설치 된 패키지(package) 리스트 보기"
tags: [리눅스, 우분투]
categories: [etc]
description: "이전에 설치 했던 패키지 목록을 보자"
---

패키지 목록 보기    
=============  

```shell
dpkg -l  
```  

## Ubuntu 14 이상에서는  

```shell
apt list
```  

위와 같이도 확인이 가능 합니다.  

## 특정 패키지 찾기  

```shell
dpkg -l | grep package-name
```  

자신이 설치 한 패키지의 버전을 확인 할 떄 사용하면 유용 합니다.  

[출처](https://askubuntu.com/questions/17823/how-to-list-all-installed-packages)  
