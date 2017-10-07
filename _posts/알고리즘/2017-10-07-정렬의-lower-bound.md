---
layout: post
title:  "정렬의 lower bound"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

Comparison Sort
================

- 데이터들간의 상대적 크기관계만을 이용해서 정렬하는 알고리즘

- 따라서 데이터들간의 크기 관계가 정의되어 있으면 어떤 데이터에든 적용 가능(문자열, 알파벳, 사용자 정의 객체 등)

- 버블소트, 삽입정렬ㄹ, 합병정렬, 퀵소트, 힙정렬 등

Non-comparison sort
--------------------


- 정렬한 데이터에 대한 사전지식을 이용 - 적용에 제한  

- Bucket sort

- Radix sort  

---

하한(Lower bound)
-----------------

- 입력된 데이터를 한번씩 다 보기위해서 최소 Θ(n)의 시간복잡도 필요

- 합병정렬과 힙정렬 알고리즘들의 시간복잡도는 Θ(nlogn)

- 어떤 comparison sort알고리즘도 Θ(nlogn)보다 나을수 없다.  


decision_tree
--------------

![decision_tree](/images/algorithm/decision_tree.png)  
(1,2,3의 의미는 숫자 값이 아니라 정렬할 데이터들의 첫번째, 두번째 값을 의미)

- Leaf노드의 개수는 n!개    
  왜냐하면 모든 순열(permutation)에 해당하므로

- 최악의 경우 시간복잡도는 트리의 높이

- 트리의 높이는
  - height >= logn! = Θ(nlogn)
