---
title: DISTINCT && ORDERED BY && LIMIT && LIKE
date: 2024-03-16
categories: [MySQL,MySQL]
tags: [mysql]		# TAG는 반드시 소문자로 이루어져야함!
---

# **DISTINCT**
==========


* `SELECT DISTINCT <열> FROM <테이블>`를 사용하면 TABLE안에 있는 열에서 중복되는 이름을 제거할 수 있다.

```sql
SELECT DISTINCT author_name, name books;
```

<br>

## **CONTACT**


* 만약 하나의 Row에서만이 아니라 여러 가지의 Row가 전부 중복될 때 제거하고 싶다면 `CONTACT`를 사용하면 된다.

* 아래는 author_fname과 author_lname이 모두 중복되는 행만 삭제한다.

```sql
-- 1번 방법
SELECT DISTINCT CONCAT(author_fname,' ', author_lname) FROM books;

-- 2번 방법
SELECT DISTINCT author_fname, author_lname FROM books;
```

<br>

# ORDERED BY

* `SELECT <열>... FROM <테이블> ORDERED BY <열>`의 형태로 테이블에 선택된 열들은 `ORDERED BY 열`을 기준으로 정렬된다.

* 기본적으로 오름차순`(ASCE)`이지만 맨 뒤에 `DESC`를 붙이면 내림차순이 된다.

```sql
-- books에 있는 모든 열은 author_lname를 기준으로 정렬된다.
SELECT * FROM books 
ORDER BY author_lname;
 
SELECT * FROM books
ORDER BY author_lname DESC;
 
SELECT * FROM books
ORDER BY released_year; 
```

* `ORDER BY <숫자>`는 SELECT에서 숫자 순서에 해당하는 열을 기준으로 정렬한다.

* `ORDER BY <열,열..>`은 여러 열을 넣으면 맨 앞의 열부터 정렬한다.

  * 아래 2번째 예시에서는 첫 번째로 author_lname로 먼저 정렬하고 두 번째로 author_fname을 기준으로 정렬한다.

```sql
-- 2 번째인 author_fname을 기준으로 정렬
SELECT book_id, author_fname, author_lname, pages
FROM books ORDER BY 2 desc;

-- 처음에 author_lname를 기준으로 정렬, 이 후에 author_fname를 기준으로 정렬
SELECT book_id, author_fname, author_lname, pages
FROM books ORDER BY author_lname, author_fname;
```

<br>

# LIMIT

* `LIMIT <숫자>`을 이용하면 LIMIT뒤에 있는 숫자만큼의 데이터만 가져온다.

```sql
-- books 테이블안에 있는 title열에서 3번째 데이터까지만 가져옴
SELECT title FROM books LIMIT 3;

-- released_year를 기준으로 내림차순 정렬해서 5번째 데이터까지 가져옴
SELECT title, released_year FROM books 
ORDER BY released_year DESC LIMIT 5;
```

<br>

# LIKE

* 일정 부분만을 가지고 검색하여 결과를 얻고 싶을 때 사용

* `WHERE <열> LIKE '%<문자>%'` 는 열 중에서 문자를 가진 열을 검색한다.

  * 위에서 `%`는 아무 문자가 들어와도 상관이 없다는 의미

  * 만약 `%<문자>`로 앞에만 적었다면 맨 뒤가 문자인 열만 검색된다.

* `WHERE <열> LIKE '_'`는 문자열의 길이가 `_`의 개수만큼과 동일한 문자열을 검색


```sql
-- 문자열 중 da가 들어간 문자열 검색
SELECT title, author_fname, author_lname, pages 
FROM books
WHERE author_fname LIKE '%da%';
 
-- 아래 예시는 _가 4개 있으므로 길이가 4인 문자열 검색
SELECT * FROM books
WHERE author_fname LIKE '____';
 
-- 문자열 중 맨 뒤가 n이 들어간 문자열 검색
SELECT title, author_fname, author_lname, pages 
FROM books
WHERE author_fname LIKE '%n';

-- 문자열 중 맨 앞이 a가 들어간 문자열 검색
SELECT title, author_fname, author_lname, pages 
FROM books
WHERE author_fname LIKE 'a%';

-- 중간에 문자가 a가 들어가며 앞뒤로 문자가 하나씩 있는 문자열 검색
SELECT * FROM books
WHERE author_fname LIKE '_a_';
```