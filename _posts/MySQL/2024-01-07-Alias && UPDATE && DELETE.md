---
title: Aliases && UPDATE && DELETE
date: 2024-01-07
categories: [MySQL,MySQL]
tags: [mysql]		# TAG는 반드시 소문자로 이루어져야함!
---

# **Aliases**

* `SELECT <열 이름> as <바꿀 이름>`의 형태로 사용한다.

* 이 명령어는 테이블의 값을 영구적으로 바꾸는게 아닌, 출력 값을 일시적으로만 바꾸는 역할


```sql
-- 기존의 cat_id라는 열을 id라는 열으로 출력
-- 영구적인 것은 아니라 다음에 사용할 때는 기존의 cat_id로 사용
SELECT cat_id AS id, name FROM cats;
```


<Br>

# **UPDATE**

* 테이블에 있던 데이터를 바꿀 때 사용

* `UPDATE <테이블 이름> SET <바꿀 값> WHERE <조건>`의 형태로 `WHERE`은 안써도 가능하지만, 쓰지 않으면 모든 행에 적용시켜버린다.

```sql
-- cats 테이블의 breed열의 값을 모두 Shorthair로 바꿈
UPDATE cats SET breed='Shorthair';

-- breed(품종)가 Tabby인 행의 breed값을 Shorthair로 바꿈
UPDATE cats SET breed='Shorthair' WHERE breed='Tabby';

-- Misty란 이름을 가진 행의 age를 14로바꿈
UPDATE cats SET age=14 WHERE name='Misty';
```


<Br>

# **DELETE**

* 테이블에서 있는 데이터를 삭제

* `DELETE FROM <테이블 이름>`은 테이블의 모든 행을 삭제하지만,
  `DELETE FROM <테이블 이름> WHERE <조건>`으로 조건에 해당하는 행만 삭제할 수 있다.

```sql
-- cats 테이블의 name이 Egg인 행 삭제
DELETE FROM cats WHERE name='Egg';

-- 모든 행 삭제
DELETE FROM cats;
```