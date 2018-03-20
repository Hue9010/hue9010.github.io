---
layout: post
title:  "압축 (compression) – 4"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

Codeword 부여하기
================

![huffman_codeword1](/images/algorithm/huffman_codeword1.png)  

- 여기서 prefix를 하나의 32비트 정수로 표현한다. 하지만 32비트 중에서 하위 몇 비트만이 실제 부여된 codeword이다. 따라서 codeword의 길이를 유지해야 한다.  

---

class Run 수정하기  
=================

```java
class Run implements Comparable<Run> {
  public byte symbol;
  public int runLen;
  public int freq;

  // 트르의 노드로 사용하기 위해서 왼쪽 자식과 오른쪽 자식 노드 필드를 추가한다.

  // 노드에 부여된 codeword를 지정하기 위한 필드들을 다음과 같이 추가한다.

  public int codeword;    //부여된 codeword를 32비트 정수로 저장
  public int codewordLen; // 부여된 codeword의 길이. 즉 codeword의
                          // 하위 codewordLen비트가 실제 codeword
}
```

---

Java에서 비트(bit) 연산  
=====================

```java
public class Test {
  public static void main(String args[]){
    int a = 60;   // 60 = 0011 1100
    int b = 13;   // 13 = 0000 1101
    int c =0;

    c = a & b;        // 12 = 0000 1100
    System.out.println("a & b = " + c);
    c = a | b;        // 61 = 0011 1101
    System.out.println("a | b = " + c);
    c = a ^ b;        // 49 = 0011 0001
    System.out.println("a ^ b = " + c);
    c = ~a;           // -61 = 1100 0011
    System.out.println("~a = " + c);
    c = a << 1;       // 120 = 0111 1000
    System.out.println("a << 1 = " + c);
    c = (a << 1) + 1; // 121 = 0111 1001
    System.out.println("(a << 1) + 1 = " + c);
    c = a << 2;       // 240 = 1111 0000
    System.out.println("a << 2 = " + c);
  }
}
```

---

codeword 부여하기  
================

![huffman_codeword2](/images/algorithm/huffman_codeword2.png)  

---

main과 compressFile 메서드  
=========================

```java
public class HuffmanCoding {
  ...

  public void compressFile(RandomAccessFile fIn) {
    collectRuns(fIn);
    createHuffmanTree();
    assignCodewords(theRoot, 0, 0);
  }

  static public void main(String args[]){
    HuffmanCoding app = new HuffmanCoding();
    RandomAccessFile fIn;
    try{
      fIn = new RandomAccessFile("sample.txt","r");
      app.compressFile(fIn);
      fIn.close();
    } catch(IOException io){
      System.err.println("Cannot open " + fileName);
    }
  }
}
```
