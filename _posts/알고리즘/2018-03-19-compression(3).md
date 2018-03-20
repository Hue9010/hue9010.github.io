---
layout: post
title:  "압축 (compression) – 3"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

Huffman Tree
============================

- Huffman coding 알고리즘은 트리들의 집합을 유지하면서  


---

최소 힙  
=======

![huffman_tree1](/images/algorithm/huffman_tree1.png)  

![huffman_tree2](/images/algorithm/huffman_tree2.png)  

![huffman_tree3](/images/algorithm/huffman_tree3.png)  

![huffman_tree4](/images/algorithm/huffman_tree4.png)  

![huffman_tree5](/images/algorithm/huffman_tree5.png)  

---

class run 수정하기  
=================

```java
class Run implements Comparable<Run> {
  public byte symbol;
  public int runLen;
  public int freq;
  // 트리의 노드로 사용하기 위해서 왼쪽 자식과 오른쪽 자식 노드 필드를 추가한다.

  // 두 run의 크기관계를 비교하는 compareTo 메서드를 overriding하라.
  // 비교의 기준은 freq이다.
}
```

---

class Heap<Run>
================

- 기존에 작성했던 Heap클래스를 가져와서 사용한다. Generics로 수정하고, heapify, insert, extractMin등의 함수들을 min heap에 맞게 수정한다.  

- C로 구현하는 사람들은 별개의 모듈로 min heap을 구현하라.  

---

Huffman Tree 만들기  
==================

```java
public class HuffmanCoding {
  private ArrayList<Run> runs = new ArrayList<Run>();

  private Heap<Run> heap; // minimum heap이다.
  private Run theRoot = null; // root of the Huffman tree

  private void createHuffmanTree() {
    heap = new Heap<Run>();

    // 1. store all runs into the heap.
    // 2. while the heap size > 1 do
    //    (1) perform extractMin two times
    //    (2) make a combined Tree
    //    (3) insert the combined tree into the heap.
    // 3. Let the Root be the root of the tree.
  }
}
```

---

Huffman Tree 출력해보기  
=====================

```java
private void printHuffmanTree() {
  preOrderTraverse(threRoot, 0);
}

private void preOrderTraverse(Run node, int depth) {
  for (int i=0; i<depth; i++)
    System.out.print("  ");
  if (node == null){
    System.out.println("null");
  } else {
    System.out.println(node.toString());
    preOrderTraverse(node.left, depth + 1);
    preOrderTraverse(node.right, depth + 1);
  }
}
```

- class Run에 적절한 toString 메서드를 추가하여 트리를 출력해보자.  
