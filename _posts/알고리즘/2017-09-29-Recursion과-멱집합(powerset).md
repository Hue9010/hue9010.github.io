---
layout: post
title:  "Recursion과 멱집합 (powerset)"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

멱집합 (powerset)
==================================

### 어떤 집합의 모든 부분 집합의 집합

- {a,b,c,d,e,f}의 모든 부분집합을 나열하려면  

  - a를 제외한 {b,c,d,e,f}의 모든 부분집합들을 나열하고  

  - {b,c,d,e,f}의 모든 부분집합에 {a}를 추가한 집합들을 나열한다.  

- {b,c,d,e,f}의 모든 부분집합에 {a}를 추가한 집합들을 나열하려면  

  - {c,d,e,f}의 모든 부분집합들에 {a}를 추가한 집합들을 나열하고  

  - {c,d,e,f}의 모든 부분집합에 {a,b}를 추가한 집합들을 나열한다.  

- {c,d,e,f}의 모든 부분집합에 {a}를 추가한 집합들을 나열하려면  

  - {d,e,f}의 모든 부분집합들에 {a}를 추가한 집합들을 나열하고  

  - {d,e,f}의 모든 부분집합에 {a,c}를 추가한 집합들을 나열한다.  

---

수도코드
------

```
powerSet(S)
if S is an empty set
  print nothing;
else
  let t be the first element of S;
  find all subsets of S-{t} by calling powerSet(S-{t});
  print the subsets;
  print the subsets with adding t;

```

위의 코드는 약간의 오류가 있다. 현재의 요구사항은 모든 부분집합을 출력하기만 하는 것인데, 현재는 찾은 부분집합들의 전체를 return으로 받아와서 출력함으로써 필요없는 메모리를 사용하게 된다.  

그래서 아래처럼 return 없이 내부에서 출력하게 바꾼다.

```
powerSet(P, S)
if S is an empty set
  print P;
else
  let t be the first element of S;
  powerSet(P, S-{t});
  powerSet(P U {t}, S-{t});
```

### mission:

- S의 멱집합을 구한 후 각각에 집합 P를 합집합하여 출력하라

- recursion 함수가 두 개의 집합을 매개변수로 받도록 설계해야 한다는 의미이다. 두 번째 집합의 모든 부분집합들에 첫번째 집합을 합집합하여 출력한다.

---

java 코드  
--------

```java
private static char data[] = {'a','b','c','d','e','f'};  
private static int n = data.length;
private static boolean[] include = new boolean[n];

public static void powerSet(int k){
  if (k == n){
    for(int i = 0; i < n; i++ ){
      if(include[i]){
        System.out.print(data[i] + " ");
      }
    }
    System.out.println();
    return;
  }
  include[k] = false;
  powerSet(k+1);
  include[k] = true;
  powerSet(k+1);
}
```

### mission:  
- data[k], ... , data[n-1]의 멱집합을 구한 후 각각에 include[i]=true, i=0, ..., k-1인 원소를 추가하여 출력하라.

- 처음 이 함수를 호출할 때는 powerSet(0)로 호출한다. 즉 P는 공집합이고 S는 전체집합이다.

---

상태공간 트리
-----------

![상태공간트리](/images/algorithm/멱집합_상태공간트리.png)  
(그림에 include와 exclude가 잘못 되어있다. 다 반대)  

- 해를 찾기 위해 탐색할 필요가 있는 모든 후보들을 포함하는 트리  

- 트리의 모든 노드들을 방문하면 해를 찾을 수 있다.

- 루트에서 출발하여 체계적으로 모든 노드를 방문하는 절차를 기술한다.  
