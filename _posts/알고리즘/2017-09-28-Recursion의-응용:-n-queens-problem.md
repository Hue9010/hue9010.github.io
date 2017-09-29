---
layout: post
title:  "Recursion의 응용: n queens problem"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

Recursion의 응용: n queens problem
==================================


상태 공간 트리
------------

![상태공간트리](/images/algorithm/상태공간트리.png)

상태공간 트리의 모든 노드를 탐색해야 하는 것은 아니다.

### 되추적 기법(Backtracking)  

- 내가 해온 과정을 돌아간다. 결정들을 하다가 막다른 결과에 도달하면 가장 최근에 내린 결정을 번복하고 다시 한다.

- 상태공간 트리를 깊이 우선 방식으로 탐색하여 해를 찾는 알고리즘을 만한다.

  ### 구현 방법  

  1. recursion  
    일반적으로 recursion으로 구현하는게 더 쉽다.  

  2. stack  

---

### \<구현 과정>

### 1.  

수도코드를 작성한다. (Design Recursion)  

```java
return-type queens(arguments){
  if non-promisiong
    report failur and return;
  else if success
    report answer and return;
  else
    visit children recursively;
}

```

### 2.   

매개변수 level은 현재 노드의 행을 표현하고, 1번에서 level 말이 어디에 놓였는지는 전역변수 배열 cols로 표현하자.  
cols[i] = j는 i번 말이 (i행, j열)에 놓였음을 의미한다.  

```java
int[] cols = new int[N+1];
return-type queens(int level){
  if non-promisiong
    report failur and return;
  else if success
    report answer and return;
  else
    visit children recursively;
}
```  

### 3.  

queens 메소드의 매개변수와 리턴타입 결정.  

```java
int[] cols = new int[N+1];
boolean queens(int level){
  if non-promisiong
    report failur and return;
  else if success
    report answer and return;
  else
    visit children recursively;
}
```

### 4.  

이전 행과 비교하여 놓을수 없는 곳인지 확인하는 메소드인 promising를 통해(아직 미구현) 놓을 수 없는 곳이면 false를 리턴하게 한다.

```java
int[] cols = new int[N+1];
boolean queens(int level){
  if(!promising(level)){
    return false;
  }
  else if success
    report answer and return;
  else
    visit children recursively;
}
```

### 5.  

성공 했을때는 true를 리턴하게 하고, 순환적으로 다음 행도 queens 메소드가 호출 되게 구현.  

```java
int[] cols = new int[N+1];
boolean queens(int level){
  if(!promising(level)){
    return false;
  }
  else if(level == N){
    return true;
  }
  for(int i=1; i <= N; i++){
    cols[level + 1] = i;
    if(queens(level+1)){
      return true;
    }
  }
  return false;
}
```

---  

### 6.

promising메소드를 구현한다. 이전에 놓은 말들과 이번 행의 말이 같은 열이면 false를 리턴.

```java
boolean promising(int level){
  for(int i = 1; i < level; i++){
    if( cols[i] == cols[level]){
      return false;
    } else if on the same diagonal{
      return false;
    }
  }
  return true;
}
```

### 7.  

동일한 대각선에 있으면 false를 리턴하는 조건을 만든다.  

> \|x - x' \| = \| y - y' \|  

두변의 길이가 같다면 동일 대각선이다.  

```java
boolean promising(int level){
  for(int i = 1; i < level; i++){
    if( cols[i] == cols[level]){
      return false;
    } else if(level-i==Math.abs(cols[level]-cols[i])){
      return false;
    }
  }
  return true;
}
```

---  

### 8.

앞서 만든 queens와 promising 메소드를 합친다.(추가적으로 출력 부분도 구현)  

```java
int[] cols = new int[N+1];
boolean queens(int level){
  if(!promising(level)){
    return false;
  }
  else if(level == N){
    for(int i = 1; i <= N; i++){
      System.out.println("(" + i + ", " + cols[i] + ")");
    }
    return true;
  }
  for(int i=1; i <= N; i++){
    cols[level + 1] = i;
    if(queens(level+1)){
      return true;
    }
  }
  return false;
}

boolean promising(int level){
  for(int i = 1; i < level; i++){
    if( cols[i] == cols[level]){
      return false;
    } else if(level-i==Math.abs(cols[level]-cols[i])){
      return false;
    }
  }
  return true;
}
```
