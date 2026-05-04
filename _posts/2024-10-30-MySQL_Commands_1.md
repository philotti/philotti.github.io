---
title: "MySQL #1 | 기본 명령에서 CRUD까지"
author: 'philotti'
date: 2024-10-30 20:09:00 +0900
last_modified_at: 2024-11-03 21:54:00 +0900
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
sitemap:
  changefreq: daily
  priority: 1.0
---

> MySQL 기본 명령들과 CRUD 명령들을 정리한 글입니다. 
{: .prompt-info }

---

## Basic Commands
SQL은 `Structured Query Language` 의 줄임말이고, 한국어로는 `구조화 질의어` 라고 정의합니다. 사실 기초 질의어와 CRUD 명령어만 알아도 데이터베이스의 어느 정도 흉내를 내는 것이 가능합니다. 그러나 그저 흉내만 내는 것이 아닌 다양한 조건문이나 여러가지 테이블의 조인을 통해서 더 복잡하고 실전에서 많이 쓰이는 데이터베이스를 구축하는 것이 목표이므로 여기 있는 기초 데이터베이스는 당연히 알고 넘어가야 한다고 생각합니다. 서론이 길었던 것 같습니다. 아래는 관계형 데이터 베이스나 테이블을 생성하는 명령들과 제가 생각하기에 그 외의 설정할 때 같이 하면 도움이 되는 기본 팁들에 대해 정리한 목차입니다.

> 이 포스트에서 작성된 데이터베이스는 제가 임의로 만든 cats 데이터베이스입니다. 또한 저는 MySQL Workbench와 맥의 터미널을 이용하였습니다.
{: .prompt-warning }

![MySQL Workbench](/assets/post_img/2024-10-30-1.webp){: width="800"}
_Using By MySQL Workbench_

<br>

![MAC Terminal](/assets/post_img/2024-10-30-2.webp){: width="800"}
_Using by MAC Terminal_

<br>

### <u>DB 생성(Create Databases)</u>
- 아래는 각각 데이터베이스를 `나열` , `생성` , `삭제` , `사용` 할 때 사용하는 명령입니다.

```sql
SHOW DATABASES;

CREATE DATABASE 'DB_NAME';

DROP DATABASE 'DB_NAME';

USE 'DB_NAME';
```

<br>

### <u>테이블 생성(Create Tables)</u>

- 아래는 각각 테이블을 `생성` , `삭제` , `확인` 하는 명령입니다.
- SHOW나 DESC(DESCRIBE)는 테이블의 `속성` 값등을 확인할 수 있는 명령입니다.

```sql
CREATE TABLE 'TABLE_NAME' ('COL1' DATA_TYPE, 'COL2' DATA_TYPE ...);

DROP TABLE 'TABLE_NAME';

SHOW TABLES;

DESC 'TABLE_NAME';
```
> Preview :
> ```sql
> CREATE TABLE cats (name VARCHAR(50), age INT);
> DROP TABLE cats;
> DESC cats;
> ```

<br>

### <u>테이블 변경(Alter Tables)</u>
- 데이터베이스에서 기존의 테이블을 유지하면서 원하는 부분만 `수정` 할 수 있는 명령입니다.
- 아래는 각각 `열 추가` , `열 이름 변경` , `데이터 타입 변경` , `열 삭제` , `테이블 이름 변경` 명령입니다.
- ~~깃허브 CSS 오류로 코드블럭이 안보이게 되었습니다. 사용시에는 언더바를 제거해주세요~~

```sql
ALTER TABLE 'TABLE_NAME'
ADD_COLUMN 'TO_ADD_COL_NAME''DATA_TYPE';

ALTER TABLE 'TABLE_NAME'_
RENAME_COLUMN 'ORIGINAL_NAME'TO'TO_CHANGE_COL_NAME';

ALTER TABLE 'TABLE_NAME'
MODIFY_COLUMN 'COL_NAME''NEW_DATA_TYPE';

ALTER TABLE 'TABLE_NAME'
DROP_COLUMN 'COL_NAME';

ALTER TABLE 'TABLE_NAME'
RENAME_TO_'NEW_TABLE_TYPE';
```

<br>

### <u>기본 키(Primary Key)</u>
- SQL 데이터베이스에는 기본 키외에 고유 키와 외래 키도 존재합니다. ~~고유 키와 외래 키는 나중에...~~
- 기본 키가 적용된 열은 테이블 내의 각 행을 `고유` 하게 만드는 열로 볼 수 있습니다. 즉, SQL 데이터베이스에서 특정 레코드를 유일하게 식별하기 위해 사용되는 키입니다.
- 기본 키를 삭제하기 전에는 반드시 기본 키가 다른 테이블에서 참조되지 않게 확인을 하고 삭제를 해야 합니다.
- Primary Key는 무조건 존재해야하므로 NOT NULL을 안해줘도 NOT NULL를 삽입한 것과 같으므로 안해줘도 됩니다.
- 아래는 각각 `기본 키 생성` , `다중 기본 키` , `삭제` 를 명령하는 방법입니다.

```sql
CREATE TABLE 'TABLE_NAME' ('COL1' DATA_TYPE, 'COL2' DATA_TYPE ..., PRIMARY KEY('COL1'));

CREATE TABLE 'TABLE_NAME' ('COL1' DATA_TYPE, 'COL2' DATA_TYPE ..., PRIMARY KEY('COL1', 'COL2'));

ALTER TABLE 'TABLE_NAME' DROP PRIMARY KEY;
```

<br>

### <u>이스케이프(Escape)</u>
- MySQL에서 `''` 와 같은 문자들을 사용할 때에는 앞에 `\` 를 붙여서 사용합니다.
- MySQL에서는 문자열은 `''` 를 사용하는 것을 추천합니다. 그 이유는 MySQL뿐 아니라 다른 SQL을 지원하는 프로그램`(PostgreSQL 등)` 에서 SQL을 작성할 때 햇갈리지 않기 위해 모두가 사용하는 `''` 를 추천합니다.
- `''` 안에 `""` 가 있는것은 괜찮습니다.

> Preview :
>```sql
>'She said "haha" and gave Mats\'s phone'
>```

<br>

### <u>DEFAULT</u>

> DEFAULT 값이 존재한다고 그것이 NULL이 아니라고는 볼 수 없습니다. INSERT로 NULL값을 따로 넣어줄 수 있기 때문입니다.
{: .prompt-warning }

- 설정해주지 않은 값에 설정한 `기본 값`을 저장해주는 명령입니다. 

> Preview :
> ```sql
>CREATE TABLE cats (
>name VARCHAR(20) NOT NULL DEFAULT 'unnamed',
>age INT NOT NULL DEFAULT 99);
> ```

<br>

### <u>AUTO_INCREMENT</u>
- `자동으로 기본 키의 값을 1부터 올려서 저장` 해주는 명령입니다.
- 직접 입력하지 않는 `Primary Key` 에 자주 사용합니다.

```sql
CREATE TABLE 'TABLE_NAME' ('COL1' DATA_TYPE AUTO_INCREMENT, 'COL2' DATA_TYPE ..., PRIMARY KEY('COL1'));
```

<br>
<br>

---

## CRUD Commands
데이터베이스 개발이나 관리에 관심이 있으신 저와 같은 분들이라면 누구나 `CRUD` 작업을 다루었을 것입니다. `CRUD` 는 `CREATE` , `READ` , `UPDATE` , `DELETE` 의 약자로, 데이터에 대한 4가지 기본 작업인 `삽입` , `검색` , `수정` , `삭제` 로 구성되어 있습니다. 일반적으로 데이터는 현재 가장 많이 사용되는 관계형 데이터베이스에 저장되고 사용되고 있습니다. SQL에서 CRUD 명령은 데이터베이스 작업을 수월하게 도와주며 **필수**로 알아야 합니다. 

<br>

### <u>삽입(CREATE)</u>
- 데이터베이스에서 행을 `삽입` 하는 명령을 의미합니다.

```sql
INSERT 'TABLE_NAME' INTO ('COL1', 'COL2' ....) VALUES ('VAL1', 'VAL2' ....);
```

> Preview :
> ```sql
> INSERT INTO cats (name, age) VALUES ('Marco', 11);
> ```

<br>

### <u>조회(READ)</u>
- 데이터베이스에서 행을 `조회` , `선택` 하는 명령을 의미합니다.
- 테이블에서 모든 데이터를 페치하거나 `WHERE` 조건문을 사용하여 선택한 조건을 기준으로 조회할 수 있습니다.
- 모든 열이나 특정 열을 조회하거나 `WHERE` 명령을 이용하여 조건 조회를 할 수 있습니다.
- `AS` 를 사용해 결과의 열에 별명을 설정할 수 있습니다. **테이블의 열 이름을 실제로 변경하는 것은 아닙니다.**
- 아래는 각각 `전체 열 조회` , `특정 열 조회` , `여러개의 특정 열들 조회` , `조건이 존재하는 열 조회` , `별명 설정` 입니다.

```sql
SELECT * FROM 'TABLE_NAME'; 

SELECT 'COL' FROM 'TABLE_NAME'; 

SELECT 'COL1', 'COL2' ... FROM 'TABLE_NAME';

SELECT * FROM 'TABLE_NAME' WHERE 'COND';  

SELECT 'COL' AS 'ALIAS' FROM 'TABLE_NAME'; 
```

> Preview : 
> ```sql
> SELECT * FROM cats;
>
> SELECT age FROM cats; 
>
> SELECT name, breed FROM cats; 
>
> SELECT * FROM cats WHERE age = 4;
>
> SELECT cat_id AS id, name FROM cats; 
> ```

<br>

### <u>수정(UPDATE)</u>
- 데이터베이스에서 행을 `수정` 하는 명령을 의미합니다.
- UPDATE는 때론 큰 `실수` 를 범하게 될 수도 있으므로 먼저 `SELECT - WHERE` 명령을 이용해서 업데이트 대상을 먼저 `조회`한 후 UPDATE를 `실행`하는 편을 추천합니다.

```sql
UPDATE 'COL' SET 'VAL' WHERE 'COND';
```

> Preview : 
> ```sql
> UPDATE cats SET age = 15 WHERE name = 'Mats';
> ```

<br>

### <u>삭제(DELETE)</u>
> DELETE 명령을 한다고 해서 테이블이 삭제되지는 않습니다. 그저 빈 깡통이 될 뿐입니다. 삭제는 DROP 명령을 이용하면 됩니다. 
{: .prompt-tip }

- 데이터베이스에서 행을 `삭제` 하는 명령을 의미합니다.
- 테이블에서 모든 레코드나 특정 레코드를 제거할 수 있습니다. 후자의 경우 `WHERE` 명령으로 제거할 행을 지정할 수 있습니다.
- 위의 UPDATE처럼 큰 `실수` 를 범하게 될 수도 있으므로 `SELECT - WHERE` 명령을 이용해서 DELETE 대상을 먼저 `조회`한 후 DELETE를 `실행`하는 편을 추천합니다.
- 아래와 같이 모든열을 삭제하거나 , `WHERE` 명령을 이용하여 조건 삭제를 할 수 있습니다.

```sql
DELETE FROM 'TABLE_NAME'; 

DELETE FROM 'TABLE_NAME' WHERE 'COND'; 
```

> Preview : 
> ```sql
> DELETE FROM cats; 
>
> DELETE FROM cats WHERE name = 'Roman';
> ```

<br>
<br>

---

## Reference
- [MySQL Reference Manual](https://dev.mysql.com/doc/refman/8.4/en/) <sup>site</sup>