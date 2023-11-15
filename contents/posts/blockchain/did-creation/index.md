---
title: did 생성
date: 2021-05-08 18:00:00
update: 2021-05-08 18:00:00
tags:
 - blockchain
description:
---

1. 사용자가 App 을 설치한다.
2. App 에서 DID 를 발급받는다.
3. 핸드폰 인증, 이메일 인증을 통해 VC 를 받급 받는다.

### DID 발급

1. DID 서명에 사용할 Private Key 를 생성한다.
2. Delegator 에 요청을 보내서 DID 를 만든다.

Delegator 는 Gas 비를 대신 내주고, Smart Contract Call 을 해서 DID 를 만들어준다.
