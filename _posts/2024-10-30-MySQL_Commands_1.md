---
title: "MySQL #1 | 기본 명령에서 CRUD까지"
author: 'jinjehr'
date: 2024-10-30 08:09:00 +0900
last_modified_at: 2024-10-30 23:50:00 +0900
categories: [MySQL, Commands]
tags: [MySQL, Commands]
description: MySQL Commands
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

## Basic Commands
데이터 베이스나 테이블을 생성하는 명령들과 그 외의 기본 팁들에 대해 정리한 목차입니다.


> 이 포스트에서 작성된 데이터베이스는 제가 임의로 만든 cats 데이터베이스입니다.  
{: .prompt-warning }

<br>

### <u>데이터베이스 만들기(Create Databases)</u>
- 아래는 각각 데이터베이스를 나열, 생성, 삭제, 사용할 때 사용하는 명령입니다.

```Sql
SHOW DATABASES;
CREATE DATABASE DB_NAME;
DROP DATABASE DB_NAME;
USE DB_NAME;
```

<br>

### <u>테이블 만들기(Create Tables)</u>

- 아래는 각각 테이블을 생성, 삭제, 확인하는 명령입니다.
- SHOW나 DESC(DESCRIBE)는 테이블의 속성 값등을 확인할 수 있는 명령입니다.

```Sql
CREATE TABLE TABLE_NAME (COL1 DATA_TYPE, COL2 DATA_TYPE ...);
DROP TABLE TABLE_NAME;
SHOW TABLES;
DESC TABLE_NAME;
```
> Preview :
> ```Sql
> CREATE TABLE cats (name VARCHAR(50), age INT);
> DROP TABLE cats;
> ```

<br>

### <u>테이블 변경하기(Alter Tables)</u>
- 데이터베이스에서 기존의 테이블을 유지하면서 원하는 부분만 수정할 수 있는 명령입니다.
- 아래는 각각 열 추가, 열 이름 변경, 데이터 타입 변경, 열 삭제, 테이블 이름 변경 명령입니다.

```Sql
ALTER TABLE TABLE_NAME ADD COLUMN 'TO_ADD_COL_NAME' 'DATA_TYPE';
ALTER TABLE TABLE_NAME RENAME COLUMN 'ORIGINAL_NAME' TO 'TO_CHANGE_COL_NAME';
ALTER TABLE TABLE_NAME MODIFY COLUMN 'COL_NAME' 'NEW_DATA_TYPE';
ALTER TABLE TABLE_NAME DROP COLUMN 'COL_NAME';
ALTER TABLE TABLE_NAME RENAME TO 'NEW_TABLE_TYPE';
```


<br>

### 기본 키(Primary Key)
- SQL 데이터베이스에는 기본 키외에 고유 키와 외래 키도 존재합니다. ~~이들은 나중에...~~
- 기본 키가 적용된 열은 테이블 내의 각 행을 고유하게 만드는 열로 볼 수 있습니다.
- 즉, SQL 데이터베이스에서 특정 레코드를 유일하게 식별하기 위해 사용되는 키입니다.
- 기본 키를 삭제하기 전에는 반드시 기본 키가 다른 테이블에서 참조되지 않게 확인을 하고 삭제를 해야 합니다.
- 아래는 각각 기본 키 생성, 다중 기본 키, 삭제를 설정하는 방법입니다.

```Sql
CREATE TABLE TABLE_NAME (COL1 DATA_TYPE, COL2 DATA_TYPE ..., PRIMARY KEY(COL1));
CREATE TABLE TABLE_NAME (COL1 DATA_TYPE, COL2 DATA_TYPE ..., PRIMARY KEY(COL1, COL2));
ALTER TABLE TABLE_NAME DROP PRIMARY KEY;
```

<br>
<br>

## About CRUD Commands
데이터베이스 개발이나 관리에 관심이 있으신 저와 같은 분들이라면 누구나 `CRUD` 작업을 다루었을 것입니다. `CRUD` 는 `CREATE` , `READ` , `UPDATE` , `DELETE` 의 약자로, 데이터에 대한 4가지 기본 작업인 `삽입` , `검색` , `수정` , `삭제` 로 구성되어 있습니다. 일반적으로 데이터는 현재 가장 많이 사용되는 관계형 데이터베이스에 저장되고 사용되고 있습니다. SQL에서 CRUD 명령은 데이터베이스 작업을 수월하게 도와줍니다. 

<br>

### <u>CREATE</u>
- 데이터베이스에서 행을 `추가` 하는 명령을 의미합니다.

```Sql
INSERT TABLE_NAME INTO (COL1, COL2 ....) VALUES (VAL1, VAL2 ....);
```

> Preview :
> ```Sql
> INSERT INTO cats (name, age) VALUES ('Marco', 11);
> ```

<br>

### <u>READ</u>
- 데이터베이스에서 행을 `조회` , `선택` 하는 명령을 의미합니다.
- 테이블에서 모든 데이터를 페치하거나 `WHERE` 조건문을 사용하여 선택한 조건을 기준으로 조회할 수 있습니다.
- 모든열이나 특정열을 조회하거나 `WHERE` 명령을 이용하여 조건 조회를 할 수 있습니다.
- `AS` 를 사용해 결과의 열에 별명을 설정할 수 있습니다. **테이블의 열 이름을 실제로 변경하는 것은 아닙니다.**

```Sql
SELECT * FROM TABLE_NAME; 
SELECT COL FROM TABLE_NAME; 
SELECT COL1, COL2 ... FROM TABLE_NAME;
SELECT * FROM TABLE_NAME WHERE COND;  
SELECT COL AS ALIAS FROM TABLE_NAME; 
```

> Preview : 
> ```Sql
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

```Sql
UPDATE COL SET VAL WHERE COND;
```

> Preview : 
> ```Sql
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

```Sql
DELETE FROM TABLE_NAME; 
DELETE FROM TABLE_NAME WHERE COND; 
```

> Preview : 
> ```Sql
> DELETE FROM cats; 
> DELETE FROM cats WHERE name = 'Roman';
> ```