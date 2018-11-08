---
layout: post
title:  "MySQL 테이블이 존재 여부 확인 쿼리"
tags: [DB]
categories: [DB]
description: "테이블이 존재하는지 아닌지 쿼리를 통해 확인 해 보자"
---

테이블이 존재 여부 확인  
===================  

```sql
SELECT 1 FROM Information_schema.tables
WHERE table_schema = 'DB명'
AND table_name = '테이블명'
```  
결과에 따라 1 또는 아무것도 반환 하지 않습니다.   

```sql
SELECT COUNT(*) FROM Information_schema.tables
WHERE table_schema = 'DB명'
AND table_name = '테이블명'
```  
결과에 따라 1 또는 0을 반환 합니다.  

```sql
SELECT EXISTS (
  SELECT 1 FROM Information_schema.tables
  WHERE table_schema = 'DB명'
  AND table_name = '테이블명'
) AS flag
```  

결과에 따라 flag = 1 또는 flag = 0을 반환 합니다.  

**COUNT**의 경우 이름에서 유추 할 수 있다시피 갯수를 세고, **EXISTS**의 경우 **ROW**가 존재하는지 아닌지를 확인 하는 차이가 있습니다.

---

만약 테이블 생성 때 확인 하는 것이라면  

```sql
create table if not exists "table name" values;
```  

`if not exists`를 통해 테이블이 없을 때만 생성 하면 됩니다!  

[제타위키](https://zetawiki.com/wiki/MySQL_%ED%85%8C%EC%9D%B4%EB%B8%94_%EC%9E%88%EB%8A%94%EC%A7%80_%ED%99%95%EC%9D%B8), [EXISTS와 IN의 차이](http://sjs0270.tistory.com/50)  
