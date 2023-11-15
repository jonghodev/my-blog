---
title: Troubleshooting tcp_rw_reuse
date: 2021-10-18 11:10:92
update: 2021-10-18 11:10:92
tags:
 - linux
description:
---

## 상황

회사에서 부하 테스트를 하는데 TPS 1500 을 유지하다보면 10초 정도가 지나면 클라이언트에서 서버로 네트워크 요청 자체가 아예 안 가게 됨.

## 해결

Client 의 Local tcp 포트가 모자라서 생긴 문제이므로 tcp_tw_reuse 값을 1로 설정해서 포트를 재활용하게 만듦.

```bash
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
```

위 방법의 경우 재부팅 할 때 저것이 초기화 되므로 아래 방법으로 재부팅 시에도 적용하게 만들 수 있다.

```bash
$ vi /etc/sysctl.conf
net.ipv4.tcp_tw_reuse = 1
```

tcp_tw_reuse

https://tech.kakao.com/2016/04/21/closewait-timewait/
