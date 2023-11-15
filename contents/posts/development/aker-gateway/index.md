---
title: aker-gateway
date: 2021-08-19 15:08:30
update: 2021-08-19 15:08:30
tags:
 - development
description:
---

회사에서 사용하고 있는 솔루션인데 팀원이 소개해줘서 알게되었다.

하나의 Bastion 에서 다른 서버들로 접속하는 것을 편하게 해주는 솔루션이다.

https://github.com/aker-gateway/Aker

팀원의 말에 따르면 이 솔루션은 다음의 장점이 있다.

"bastion 서버에서 모든 사용자의 커맨드나 접속 기록을 다 로그로 남기고있어서 기록 관리가 편하고 접근할 서버를 사용자 혹은 그룹별로 지정할수 있어서 접근제어도 편합니다."

다만 Bastion 이 뚫리면 모든 서버가 뚫린다는 단점이 있기 때문에 Bastion 서버에 대한 보안을 강하게 해야한다. 예를들어 OTP 를 건다던지...
