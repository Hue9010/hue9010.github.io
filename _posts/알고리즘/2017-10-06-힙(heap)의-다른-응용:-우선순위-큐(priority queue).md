---
layout: post
title:  "힙(heap)의 다른 응용: 우선순위 큐 (priority queue)"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

힙의 응용: 우선순위 큐
===================

- 최대 우선순위 큐(maximum priority queue)는 다음의 두 가지 연산을 지원하는 자료 구조

  - INSERT(x): 새로운 원소 x를 삽입

  - EXTRACT_MAX(): 최대값을 삭제하고 반환

- 최소 우선순위 큐 (minimum priority queue)는 EXTRACT-MAX 대신 EXTRACT-MIN을 지원하는 자료구조

- MAX HEAP을 이용하여 최대 우선순위 큐를 구현  

FIFO: 먼저 들어온 값이 우선순위가 높은 큐  

***이번 예제에서는 값 자체를 우선순위라고 본다.(큰 값이 더 우선순위가 높다고)***

---

INSERT
------

INSERT(x): 새로운 원소 x를 삽입

![queue_insert](/images/algorithm/queue_insert.png)


수도코드
-------

```
HEAP-INSERT(A, key){
  heap_size = heap_size+1;
  A[heap_size] = key;
  i = heap_size;
  while( i > 1 and A[PARENT(i)] < A[i]){
    exchange A[i] and A[Parent(i)];
    i = PARENT(i);
  }
}
```

### 시간복잡도 : O(logn)

시간복잡도는 트리의 높이(LEVEL)에 비례한다.

---

EXTRACT_MAX()
--------------

EXTRACT_MAX(): 최대값을 삭제하고 반환

![EXTRACT_MAX](/images/algorithm/EXTRACT_MAX.png)

1. 가장 큰 값인 root의 값을 반환.

2. 가장 마지막 노드를 root로 옮긴다.

3. HEAPIFY를 한다.(현재는 MAX-HEAPIFY)

수도코드
-------

```
HEAP-EXTRACT-MAX(A)
  if heap-size[A] < 1
    then error "heap underflow"
  max <- A[1]
  A[1] <- A[heap-size[A]]
  heap-size[A] <- heap-size[A] - 1
  MAX-HEAPIFY(A,1)
  return max
```

### 시간복잡도 : O(logn)
