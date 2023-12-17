---
title: Default Value
date: 2023-12-16
categories: [MySQL,MySQL]
tags: [mysql]		# TAG는 반드시 소문자로 이루어져야함!
---

# **Default Value**

* 테이블을 생성하고 열을 추가할 때, 기본값을 설정할 수 있다.

```sql
-- name의 기본값은 'no name provided'이고 age의 기본값은 99
CREATE TABLE cats3  (    
    name VARCHAR(20) DEFAULT 'no name provided',    
    age INT DEFAULT 99  
);

-- age에는 2가 들어가지만 name에는 아무런 값이 없어서 기본값인 no name provided으로 설정된다.
INSERT INTO cats3(age)
VALUES (2);
```

* 여기서 기본값을 설정한다고 null이 될 수 없다는 뜻은 아니다.

* 기본값이 들어가 있어서 NULL이 기본적으로 될 수는 없지만, 원한다면 NULL로 설정할 수도 있다.

```sql
-- name에도 NULL, age에도 NULL이 들어간다.
INSERT INTO cats3(name, age) VALUES(NULL, NULL);
```

<br>

* 그래서 NULL도 허용하지 않으면서 기본값을 설정하는 것도 가능하다.

```sql
CREATE TABLE cats4  (
        name VARCHAR(20) NOT NULL DEFAULT 'unnamed',
        age INT NOT NULL DEFAULT 99
);

-- Error!
-- INSERT INTO cats4(name, age) VALUES(NULL, NULL);
```