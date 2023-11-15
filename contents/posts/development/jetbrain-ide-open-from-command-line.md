---
title: Jetbrain IDE open from command line
date: 2021-09-24 16:09:70
update: 2021-09-24 16:09:70
tags:
 - development
description:
---

Command + Shift + A > Create Command-line Launcher

혹은 메뉴 상단 바에서 아래 경로로 찾아가자.

Tools > Create Command-line Launcher

/usr/local/bin 경로에 커맨드를 만들어주기 때문에 그 명렁을 사용해서 Jetbrain 계열의 IDE 를 열 수 있다.

Webstorm, Intellij, Pycharm, Goland 모두 가능하다.

```bash
# 현재 경로에서 Goland 열기
goland .
```
