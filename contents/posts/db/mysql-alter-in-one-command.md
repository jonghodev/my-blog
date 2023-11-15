---
title: Mysql alter in one command
date: 2022-01-25 14:01:15
update: 2022-01-25 14:01:15
tags:
 - db
description:
---

아래 쿼리를 날리면 그 테이블의 character set 및 collation 을 볼 수 있다.

그런데 collation 이 잘못 설정되어 있어서 수정해줘야 했다.

```sql
SHOW CREATE TABLE 'your-table-name'
```

아래 쿼리를 통해서 한 스키마에 있는 모든 테이블의 alter 문을 조회할 수 있다.

```sql
USE INFORMATION_SCHEMA;
SELECT
CONCAT('ALTER TABLE `', TABLE_SCHEMA,'`.`', TABLE_NAME, '` CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;')
AS MySQLCMD FROM TABLES
WHERE TABLE_SCHEMA = 'your-schme-name';
```

그런데 에러가 나는 경우가 있었다.

한 테이블에서 utfmb3 를 사용하고 있었는데 utfmb4 로 변경하려니 에러가 나는 것이다.

그 이유는 한 컬럼이 인덱스가 걸려있었는데 그 컬럼이 `varchar(1024)` 였다. 근데 mysql 설정 상 최대 index 길이가 3072 byte 였다.

utf8 에서는 한 글자당 3byte 를 차지하므로 괜찮았는데 utf8mb4 는 4byte 를 차지해서 이미 인덱스가 걸려있으므로 변경할 수 없었던 것이다.

이것은 여러 방법으로 해결이 가능하니 Pass...

## References

https://dba.stackexchange.com/questions/35073/modify-all-tables-in-a-database-with-a-single-command

https://foxybearkim.tistory.com/3
