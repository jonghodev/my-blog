---
title: pip install 시 openssl 문제 날 때
date: 2021-09-10 08:09:02
update: 2021-09-10 08:09:02
tags:
 - development
description:
---

낮은 버전의 mysqlclient 라이브러리를 설치하거나 cryptography 라이브러리를 설치할 때 생기는 문제다.

openssl 라이브러리 문제가 나는 경우가 많다.

다음 코드를 통해서 openssl 폴더 경로를 지정해서 라이브러리를 설치해주자.

```bash
LDFLAGS=-L/usr/local/opt/openssl/lib pip install mysqlclient
LDFLAGS=-L/usr/local/opt/openssl/lib pip install cryptography
```
