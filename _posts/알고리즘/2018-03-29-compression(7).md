---
layout: post
title:  "압축 (compression) – 7"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

디코딩하기
========

class HuffmanDecoder  
=====================

```java
public class HuffmanDecoder {
  static public void main(String args[]) {
    String fileName = "";
    HuffmanCoding app = new HuffmanCoding();
    RandomAccessFile fIn;
    Scanner kb = new Scanner(System.in);
    try {
      System.out.print("Enter a file name : ");
      fileName = kb.next();
      fIn = new RandomAccessFile(fileName, "r");
      app.decompressFile(fileNAme, fIn);
      fIn.close();
    } catch (IOException io ) {
      System.err.println("Cannot open " + fileName);
    }
  }
}
```

decompressFile  
================

```java
public void decompressFile(String inFileName, RandomAccessFile fIn) throws IOException {
  String outFileName = new String(inFileName + ".dec");
  RandomAccessFile fOut = new RandomAccessFile(outFileName, "rw");
  inputFrequencies(fIn);
  createHuffmanTree();
  assignCodewords(theRoot, 0, 0);
  decode(fIn, fOut);
}
```

inputFrequencies  
=================

```java
private void inputFrequencies(RandomAccessFile fIn) throws IOException {
  int dataIndex = fIn.readLong();
  runs.ensureCapacity(dataIndex);
  for ( int j = 0; j < dataIndex; j++){
    Run r = new Run();
    r.symbol = (byte) fIn.read();
    r.runLen = fIn.readInt();
    r.freq = fIn.readInt();
    runs.add(r);
  }
}
```
