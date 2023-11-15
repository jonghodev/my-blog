---
title: Mysql Variable 속도 문제
date: 2022-01-25 12:01:67
update: 2022-01-25 12:01:67
tags:
 - db
description:
---

mysql 에서 variable 을 사용하면 속도가 느리고 단순 string 을 사용하면 속도가 개선되는 문제가 있다.

예를들어서 `select * from members where members.id = @session_member_id` 이 코드 보다는 `select * from members where members.id = '3'` 이 코드가 더 좋다는 것이다.

아래 링크를 참고해서 보니 mysql known 그 이유는 variable 을 쓰면 index 를 사용하지 않고 full sacn 을 하기 때문이다.

https://stackoverflow.com/questions/53462728/slow-sql-statement-when-using-variables
