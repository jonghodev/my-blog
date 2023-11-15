---
title: Redis Server Install
date: 2020-12-11 07:00:00
update: 2020-12-11 07:00:00
tags:
 - development
description:
---

Linux 에 Redis 를 설치하는 방법을 알아보자.

## Install

아래 명령어를 통해 Redis 서버를 설치한다.

```bash
sudo apt-get update
sudo apt-get install redis-server
```

## Start

아래 명령어를 통해 Redis server 를 실행한다.

```bash
redis-server
```

## redis CLI

아래 명령어를 통해 Redis Server CLI 에 접근할 수 있다.

```bash
# 버전 확인
redis-server --version

# 포트 확인
netstat -nlpt | grep 6379

# Redis CLI 접속
redis-cli

# 상태 체크
127.0.0.1:6379: ping
PONG
```

CLI 에 접속해 ping 을 입력했을 떄 PONG 이라고 출력되면 서버가 잘 동작됨을 알 수 있다.

## System Command

```bash
service redis-server start
service redis-server status
```

## 외부 접속 허용

```bash
# 포트확인
netstat -nlpt | grep 6379
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN                                                                                                                                                                   -
tcp6       0      0 ::1:6379                :::*                    LISTENz`
```

포트를 확인했을 때 127.0.0.1:6379 로 열려있는 것을 확인할 수 있다.

이것은 내부 (local) 에서만 접속이 가능하고 외부에서는 접속이 안 되므로

외부에서 접속을 가능하게 하기 위해서 `redis.conf` 를 수정해야 한다.

### Redis.conf

```bash
sudo vi /etc/redis/redis.conf

# 암호를 설정하기 위해 requirepass 로 검색해서 아래와 같이 변경해준다.
requirepass <원하는 password>

# 외부 접속을 허용하기 위해 bind 로 검색해서 아래와 같이 변경해준다.
bind 0.0.0.0 ::1
```
