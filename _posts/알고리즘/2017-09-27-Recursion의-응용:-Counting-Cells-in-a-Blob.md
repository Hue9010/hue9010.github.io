---
layout: post
title:  "Recursion의 응용: Counting Cells in a Blob"
tags: [알고리즘, 영리한 프로그래밍을 위한 알고리즘 강좌]
categories: [알고리즘]
---

인프런의 [영리한 프로그래밍을 위한 알고리즘 강좌](https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/)를 보고 작성한 문서입니다.

Counting Cells in a Blob
=========================

![Counting Cells in a Blob](/images/algorithm/Counting_Cells_in_a_Blob.png)


- ### 입력:
  - N*N 크기의 2차원 그리드(grid)

  - 하나의 좌표 (x,y)

- ### 출력:
  - 픽셀 (x,y)가 포함된 blob의 크기,

  - (x,y)가 어떤 blob에도 속하지 않는 경우에는 0

현재 픽셀이 속한 blob의 크기를 카운트 하려면

#### - Base case:  현재 픽셀이 image color가 아니라면 0을 반환한다.  

#### - Recursice case: 현재 픽셀이 image color라면   

  1. 먼저 현재 픽셀을 카운트 한다(count=1)

  2. 현재 픽셀이 중복 카운트되는 것을 방지하기 위해 다른 색으로 칠한다.  

  3. 현재 픽셀에 이웃한 모든 픽셀들에 대해서 그 픽셀이 속한 blob의 크기를 카운트하여 카운터에 더해준다.

  4. 카운터를 반환한다.

---

### 수도 코드

Algorithm for countCells(x,y)  

```
if the pixel (x,y) is outside the grid
  the result is 0;

else if pixel(x,y) is not an image pixel or already counted
  the result is 0;
else
  set the colour of the pixel (x,y) to a red colour;
  the result is 1 plus the number of cells in each piece of
  the blob that includes a nearest neighbour;

```  

1. (x,y) pixel이 배열 범위의 값인지 확인한다. 아니라면 0을 리턴한다.

2. 해당 픽셀이 image pixel 또는 이지 count한 픽셀이면 0을 리턴한다.

3. 해당 픽셀이 image pixel이면 색을 변경한 후, count + 1을 하고 근처 픽셀들을 재귀적으로 확인한다.

### java 코드

```java
private static int BACKGROUND_COLOR = 0;
private static int IMAGE_COLOR = 1;
private static int ALREADY_COUNTED = 2;

public int countCells(int x, int y){
  int result;
  //배열의 범위인지 확인
  if(x<0 || x>N || y<0 || y>=N){
    return 0;
  }
  //이미지 픽셀인지 확인
  else if(grid[x][y] != IMAGE_COLOR){
    return 0;
  }
  //해당 픽셀을 ALREADY_COUNTED로 변경후 1을 리턴 하면서 인근 픽셀들에 대해 재귀적으로 조사한다.
  else{
    grid[x][y] = ALREADY_COUNTED;
    return 1 + countCells(x-1, y+1) + countCells(x, y+1) +
    countCells(x+1, y+1) + countCells(x-1, y) +
    countCells(x+1, y) + countCells(x-1, y-1) +
    countCells(x, y-1) + countCells(x+1, y-1);
  }
}
```
