---
layout: post
title:  "sorting in linear time: Radix Sort"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

Radix Sort
===========

![radix_sort](/images/algorithm/radix_sort.png)

#### 매 단계는 stable sort여야 한다.

---

수도코드
-------

```
RADIX-SORT(A,d)
  for i <- 1 to d
    do use a stable sort to sort array A on digit i
```

### 시간 복잡도 O(d(n+k))

---

비교
----

![알고리즘들](/images/algorithm/알고리즘들.png)
