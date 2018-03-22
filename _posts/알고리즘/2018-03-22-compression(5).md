---
layout: post
title:  "압축 (compression) – 5"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

Codeword 검색하기
================

- 데이터 파일을 압축하기 위해서는 데이터 파일을 다시 시작부터 읽으면서 run을 하나씩 인식한 후 해당 run에 부여된 codeword를 검색한다.  

- Huffman트리에는 모든 run들이 리프노드에 위치하므로 검색하기 불편하다.  

- 검색하기 편리한 구조를 만들어야 한다.  

---

Array of Linked Lists  
======================

![Array_of_Linked_Lists1](/images/algorithm/Array_of_Linked_Lists1.png)  

---

storeRunsIntoArray  
===================

```java
private Run [] chars = new Run [256];
// Huffman 트리의 모든 리프노드들을 chars에 recursion으로 저장한다.
private void storeRunsIntoArray( Run p ) {
  if (p.left == null && p.right == null ){
    insertToArray(p); // 배열 chars[(unsigned int)p.symbol)]가 가리키는 연결리스트의 맨 앞에 p를 삽입한다.
  }
  else {
    storeRunsIntoArray(p.left);
    storeRunsIntoArray(p.right);
  }
}

public void compressFile(RandomAccessFile fIn){
  collectRuns(fIn);
  createHuffmanTree();
  assignCodeWords(theRoot, 0, 0);
  storeRunsIntoArray(theRoot);
}
```

---

Run 검색하기  
===========

- Symbol과 runLength가 주어질 때 배열 chars를 검색하여 해당하는 run을 찾아 반환하는 메서드를 작성한다.  

  ```java
  public Run findRun(byte symbol, int length){
    // 배열 chars에서 (symbol, length)에 해당하는 run을 찾아 반환한다.
  }
  ```
