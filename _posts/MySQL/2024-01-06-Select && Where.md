---
title: SELECT && WHERE
date: 2024-01-06
categories: [MySQL,MySQL]
tags: [mysql]		# TAG는 반드시 소문자로 이루어져야함!
---

**SELECT**
===============

### **SELECT * FROM**

* `SELECT * FROM <테이블 이름>`에서 `*`는 `테이블의 모든 행을 표시한다는 뜻으로 사용`된다.

```sql
-- 테이블의 모든 행 표시하기
SELECT * FROM cats; 
```

<br>

### SELECT name FROM

* `SELECT <열 이름> FROM <테이블 이름>`에서 name은 `테이블에서 해당 열만 사용한다는 뜻`이다.


```sql
-- cats 테이블의 age 열만 가져오기
SELECT age FROM cats;
```


* `,`를 이용해서 한 번에 여러 열을 표시할 수도 있다

```sql
-- cats 테이블의 name, breed 한번에 가져오기
SELECT name, breed FROM cats;
```

<br>

# **WHERE**

* `WHERE 조건`을 사용해서 `조건에 해당하는 행만 사용할 수 있다.`

 ```sql
-- cats 테이블에서 age가 4인 행
SELECT * FROM cats WHERE age = 4;
-- cats 테이블에서 name이 Egg인 행
SELECT * FROM cats WHERE name ='Egg';
 ```