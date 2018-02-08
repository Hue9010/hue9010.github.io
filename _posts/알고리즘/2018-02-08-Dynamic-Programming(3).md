---
layout: post
title:  "Dynamic Programming(3)"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

동적계획법  
=========

- 일반적인 최적화문제(optimistation problem) 혹은 카운팅(counting) 문제에 적용됨  

- 주어진 문제에 대한 순환식(recurrence equation)을 정의한다.  

- 순환식을 memorization 혹은 bottom-up 방식으로 푼다.  

---

핵심은 순환식  
------------

- subproblem들을 풀어서 원래 문제를 푸는 방식, 그런 의미에서 분할정복법과 공통성이 있음  

- 분할정복법에서는 분할된 문제들이 서로 disjoint하지만 동적계획법에서는 그렇지 않음  

- 즉 서로 overlapping하는 subproblem들을 해결함으로써 원래 문제를 해결  

---

분할정복법 vs 동적계획법  
====================

![분할정복법](/images/algorithm/분할정복법.png)  

![행렬경로문제](/images/algorithm/행렬경로문제.png)  

### Optimal Substructure

- 어떤 문제의 최적해가 그것의 subproblem들의 최적해로부터 효율적으로 구해질 수 있을 때 그 문제는 optimal substructure를 가진다고 말한다.  

- 분할정복법, 탐욕적기법, 동적계획법은 모두 문제가 가진 이런 특성을 이용한다.  

---

### Optimal Substructure를 확인하는 질문  

![optimal_substructure를_확인하는_질문](/images/algorithm/optimal_substructure를_확인하는_질문.png)  

### 최장경로 문제  

- 노드를 중복 방문하지 않고 가는 가장 긴 경로  

- optimal substructure를 가지는가?

  ![최장경로문제](/images/algorithm/최장경로문제.png)  

  ![최장경로문제2](/images/algorithm/최장경로문제2.png)  
