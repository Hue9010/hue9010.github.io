---
layout: post
title:  "압축 (compression) – 6"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
image:
  feature: header/algorithm.png
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

---

인코딩하기
========

- 압축파일의 맨 앞부분(header)에 파일을 구성하는 run들에 대한 정보를 기록한다.  

- 이때 원본 파일의 길이도 함께 기록한다(왜 필요할까?)  

---

outputFrequencies  
==================

```java
// fIn은 입출할 파일, fOut은 압축된 파일이다.
private void outputFrequencies(RandomAccessFile fIn, RandomAccessFile fOut) throws IOException {
  // 먼저 run의 개수를 하나의 정수로 출력한다.
  fOut.writeInt(runs.size());

  // 원본 파일의 크기(byte단위)를 출력한다.
  fOut.writeLong(fIn.getFilePointer());

  // 각각의 run들을 출력한다.
  for (int j =0; j < runs.size(); j++){
    Run r = runs.get(j);
    fOut.write(r.symbol); //write a byte
    fOut.writeInt(r.runLen);
    fOut.writeInt(r.freq);
  }
}
```

---

compressFile  
=============

```java
// fIN은 압축할 파일, inFileName은 그 파일의 이름이다. 파일의 이름을 추가로 바든ㄴ 이유는 압축된 파일의 이름을 정하기 위해서이다.
public void compressFile(String inFileName, RandomAccessFile fIn) throws IOException {
  // 압축파일의 이름은 압축할 파일의 이름에 확장자를 .z를 붙인 것이다.
  String outFileName = new String(inFileName + ".z");

  // 압축파일을 여기서 생성하여 outputFrequencies와 encode메서드에게 제공한다.
  RandomAccessFile fOut = new RandomAccessFile(outFileName, "rw");

  collectRuns(fIn);
  outputFrequencies(fIn, fOut);
  createHuffmanTree();
  assignCodewords(theRoot, 0, 0);
  storeRunsIntohashMap(theRoot);
  fIn.seek(0);
  encode(fIn, fOut);
}
```

---

main  
=====

```java
public class HuffmanCoding {
  ...

  public void compressFile(String inFileName, RandomAccessFile fIn) throws IOException {
    ...
  }

  static public void main(String args[]){
    HuffmanCoding app = new HuffmanCoding();
    RandomAccessFile fIn;
    try {
      fIn = new RandomAccessFile("sample.txt", "r");
      app.copressFile("sample.txt", fIn);
      fIn.close();
    } catch (IOException io) {
      System.err.println("Cannot open " + fileName);
    }
  }
}
```

---

encode()  
=========

![huffman_encode1](/images/algorithm/huffman_encode1.png)  

```
private void encode(RandomAccessFile fIn, RandomAccessFile fOut) {
  while there remains bytes to read in the file {
    recongnise a run;
    find the codeword for the rn;
    pack the codeword into the buffer;
    if the buffer becomes full
          write the buffer into the compressed file;
  }
  if buffer is not empty {
    append 0s into the buffer;
    write the buffer into the compressed file;
  }
}
```

---

class HuffmanEncoder  
=====================

```java
public class HuffmanEncoder {
  static public void main(String args[]){
    String fileName = "";
    HuffmanCoding app = new HuffmanCoding();
    RandomAccessFile fIn;
    Scanner kb = new Scanner(System.in);
    try {
      System.out.print("Enter a file name: ");
      fileName = kb.next();
      fIn = new RandomAccessFile(fileName, "r");
      app.compressFile(fileName, fIn);
      fIn.close();
    } catch (IOException io) {
      System.err.println("cannot open " + fileName);
    }
  }
}
```
