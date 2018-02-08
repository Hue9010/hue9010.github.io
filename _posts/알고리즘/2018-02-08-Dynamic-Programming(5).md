---
layout: post
title:  "Dynamic Programming(5)"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

Longest Common Subsequence(LCS)  
================================

- \<bcdb>는 문자열 \<a**bc**b**d**a**b**>의 subsequence이다.  

- \<bca>는 문자열 \<a**bc**bd**a**b>와 \<**b**d**ca**ba>의 commonsubsequence이다.  

- Longest common subsequence(LCS)  

  - common subsequence들 중 가장 긴 것  

  - \<bcba>는 \<abcbdab>와 \<bdcaba>의 LCS이다  

---

Optimal_Substructure  
=====================  

![Optimal Substructure](/images/algorithm/Optimal_Substructure.png)  

### 순환식  

![Optimal_Substructure_순환식](/images/algorithm/Optimal_Substructure_순환식.png)  
![Optimal_Substructure_순환식2](/images/algorithm/Optimal_Substructure_순환식2.png)  

![Optimal_Substructure_예](/images/algorithm/Optimal_Substructure_예.png)  

```java
int lcs( int m, int n ){
  for ( int i = 0; i <= m; i++ )
    c[i][0] = 0;
  for ( int j = 0; j <= n; j++ )
    c[0][j] = 0;
  for ( int i = 0; i <= m; i++) {
    for ( int j = 0; j <= n; j++ ) {
      if ( x[i] == y[j] )
        c[i][j] = c[i-1][j-1] + 1;
      else
        c[i][j] = Math.max( c[i - 1][j], c[i][ j - 1]);
    }
  }
  return c[m][n];
}
```

시간복잡도 : θ(mn)  
