---
title: "MySQL #1 | 기본 명령에서 CRUD까지"
author: 'jinjehr'
date: 2024-10-30 08:09:00 +0900
last_modified_at: 2024-10-30 23:50:00 +0900
categories: [MySQL, Commands]
tags: [MySQL, Commands]
description: MySQL Commands #1
toc: true 
math: true
pin: false
published: true
image:
  path: /assets/post_img/sql_logo.webp
  alt: MySQL
---

> MySQL 기본 명령들과 CRUD 명령들을 정리한 글입니다. 
{: .prompt-info }

## About CRUD Commands
데이터베이스 개발이나 관리에 관심이 있으신 저와 같은 분들이라면 누구나 `CRUD` 작업을 다루었을 것입니다. `CRUD` 는 `CREATE` , `READ` , `UPDATE` , `DELETE` 의 약자로, 데이터에 대한 4가지 기본 작업인 `삽입` , `검색` , `수정` , `삭제` 로 구성되어 있습니다. 일반적으로 데이터는 현재 가장 많이 사용되는 관계형 데이터베이스에 저장되고 사용되고 있습니다. SQL에서 CRUD 명령은 데이터베이스 작업을 수월하게 도와줍니다. 

<br>

> 이 포스트에서 작성된 데이터베이스는 제가 임의로 만든 cats 데이터베이스입니다.  
{: .prompt-warning }

<br>

### <u>CREATE</u>
- 데이터베이스에서 행을 `추가` 하는 명령을 의미합니다.

```sql
INSERT 테이블명 INTO (COL1, COL2 ....) VALUES (VAL1, VAL2 ....);
```

> Preview :
> ```sql
> INSERT INTO cats (name, age) VALUES ('Marco', 11);
> ```

<br>

### <u>READ</u>
- 데이터베이스에서 행을 `조회` , `선택` 하는 명령을 의미합니다.
- 테이블에서 모든 데이터를 페치하거나 `WHERE` 조건문을 사용하여 선택한 조건을 기준으로 조회할 수 있습니다.
- 모든열이나 특정열을 조회하거나 `WHERE` 명령을 이용하여 조건 조회를 할 수 있습니다.
- `AS` 를 사용해 결과의 열에 별명을 설정할 수 있습니다. **테이블의 열 이름을 실제로 변경하는 것은 아닙니다.**

```sql
SELECT * FROM TABLE_NAME; 
SELECT COL FROM TABLE_NAME; 
SELECT COL1, COL2 ... FROM TABLE_NAME;
SELECT * FROM TABLE_NAME WHERE COND;  
SELECT COL AS ALIAS FROM TABLE_NAME; 
```

> Preview : 
> ```sql
> SELECT * FROM cats; 
> SELECT age FROM cats; 
> SELECT name, breed FROM cats; 
> SELECT * FROM cats WHERE age = 4;
> SELECT cat_id AS id, name FROM cats; 
> ```

<br>

### <u>UPDATE</u>
- 데이터베이스에서 행을 `수정` 하는 명령을 의미합니다.
- UPDATE는 때론 큰 `실수` 를 범하게 될 수도 있으므로 먼저 `SELECT - WHERE` 명령을 이용해서 업데이트 대상을 먼저 조회한 후 UPDATE를 실행하는 편을 추천합니다.

```sql
UPDATE COL SET VAL WHERE COND;
```

> Preview : 
> ```sql
> UPDATE cats SET age = 15 WHERE name = 'Mats';
> ```

<br>

### <u>DELETE</u>
> DELETE 명령을 한다고 해서 테이블이 삭제되지는 않습니다. 그저 빈 깡통이 될 뿐입니다. 삭제는 DROP 명령을 이용하면 됩니다. 
{: .prompt-tip }

- 데이터베이스에서 행을 `삭제` 하는 명령을 의미합니다.
- 테이블에서 모든 레코드나 특정 레코드를 제거할 수 있습니다. 후자의 경우 `WHERE` 명령으로 제거할 행을 지정할 수 있습니다.
- 위의 UPDATE처럼 큰 `실수` 를 범하게 될 수도 있으므로 `SELECT - WHERE` 명령을 이용해서 DELETE 대상을 먼저 조회한 후 DELETE를 실행하는 편을 추천합니다.
- 아래와 같이 모든열을 삭제하거나 , `WHERE` 명령을 이용하여 조건 삭제를 할 수 있습니다.

```sql
DELETE FROM TABLE_NAME; 
DELETE FROM TABLE_NAME WHERE COND; 
```

> Preview : 
> ```sql
> DELETE FROM cats; 
> DELETE FROM cats WHERE name = 'Roman';
> ```