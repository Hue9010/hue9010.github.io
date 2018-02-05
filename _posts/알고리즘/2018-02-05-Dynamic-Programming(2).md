---
layout: post
title:  "Dynamic Programming(2)"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

행렬 경로 문제  
============

- 정수들이 저장된 nxn 행렬의  좌상단에서 우하단까지 이동한다. 단 오른쪽이나 아래쪽 방향으로만 이동할 수 있다.  

- 방문한 칸에 있는 정수들의 합이 최소화되도록 하라.  

  ![행렬 경로](/images/algorithm/행렬_경로.png)  

- ## Key Observation

  ![key observation](/images/algorithm/key_observation.png)  

- ## 순환식  

  ![순환식](/images/algorithm/순환식.png)  

- ## Recursive Algorithm  

  ```java
  int mat(int i, int j){
    if ( i == 1 && j == 1)
      return m[i][j];
    else if ( i == 1)
      return mat(1, j-1) + m[i][j];
    else if ( j == 1)
      return mat( i - 1, 1) + m[i][j];
    else
      return Math.min(mat( i - 1, j ), mat( i, j - 1 )) + m[i][j];
  }
  ```

- ## Memoization  

  ```java
  int mat(int i, int j){
    if ( L[i][j] != -1 ) return L[i][j];
    if ( i == 1 && j == 1 )
      L[i][j] = m[i][j];
    else if ( i == 1)
      L[i][j] = mat( 1, j-1 ) + m[i][j];
    else if ( j == 1 )
      L[i][j] = mat( i - 1 , 1 ) + m[i][j];
    else
      L[i][j] = Math.min( mat( i - 1 , j ), mat( i, j - 1 ) ) + m[i][j];
    return L[i][j];
  }
  ```  

- ## Bottom-up  

  ![bottom-up 방식](/images/algorithm/bottom-up_방식.png)  

  ```java
  int mat(){
    for ( int i = 1; i <= n; i++ ){
      for ( int j = 1; j <= n; j++ ){
        if ( i == 1 && j == 1 )
          L[i][j] = m[1][1];
        else if ( i == 1 )
          L[i][j] = m[i][j] + L[i][j-1];
        else if ( j == 1 )
          L[i][j] = m[i][j] + L[i-1][j];
        else
          L[i][j] =  m[i][j] + Math.min( L[i-1][j] , L[i][j-1] );
      }
    }
    return L[n][n];
  }
  ```  

  - Common Trick  

    ```java
    // initialise L with L[0][j]=L[i][0]=무한대 for all i and j
    int mat(){
      for ( int i = 1; i <= n; i++ ){
        for (int j = 1; j <= n; j++ ){
          if ( i == 1 && j == 1 )
            L[i][j] = m[1][1];
          else
            L[i][j] =  m[i][j] + Math.min( L[i-1][j] , L[i][j-1] );
        }
      }
      return L[n][n];
    }
    ```

---

경로 구하기  
==========

![경로구하기](/images/algorithm/경로구하기.png)  

```java
// initialise L with L[0][j]=L[i][0]=무한대 for all i and j
int mat(){
  for ( int i = 1; i <= n; i++ ){
    for ( int j = 1; j <= n; j++ ){
      if ( i == 1 && j == 1 ){
        L[i][j] = m[1][1];
        P[i][j] = '-';
      }
      else{
        if ( L[i-1][j] < L[i][j-1] ){
          L[i][j] = m[i][j] + L[i-1][j];
          P[i][j] = '←';
        }
        else{
          L[i][j] = m[i][j] + L[i][j-1];
          P[i][j] = '↑';
        }
      }
    }
  }
  return L[n][n];
}
```

## 출력  

```java
void printPath(){
  int i =n, j = n;
  while ( P[i][j] != '-' ) {
    print( i + " " + j );
    if ( P[i][j] == '←' )
      j = j-1;
    else
      i = i-1;
  }
  print( i + " " + j );
}
```

```java
void printPathRecursive(int i, int j){
  int i = n, j = n;
  if( P[i][j] == '-' )
    print( i + " " + j );
  else{
    if ( P[i][j] == '←' )
      printPathRecursive( i, j - 1 );
    else
      printPathRecursive( i - 1 , j );
    print( i + " " + j );
  }
}
```
