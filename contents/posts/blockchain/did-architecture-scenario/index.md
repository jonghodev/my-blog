---
title: did 아키텍처 시나리오
date: 2021-05-08 18:00:00
update: 2021-05-08 18:00:00
tags:
 - blockchain
description:
---

1. 사용자가 DID 를 생성합니다.
2. AA 에 요청해서 VC 를 발급받습니다.
3. SP 로부터 검증 요청이오면 사용자는 VP 보내고, SP 는 VP 를 검증합니다.
4. 서비스 서버에 Rest API 요청할 때도 Request 관련 값을 DID 를 서명해서 보내고, 서비스 서버는 검증합니다.

![](./images/did-아키텍처-시나리오-1.png)

- [did 에 대해](../did-outline/index)
- [did 생성](../did-creation/index)
- [vc vp 검증](../verify-vc-and-vp/index)
- [Rest API DID 서명](../sign-did-in-rest-api/index)
- [Rest API DID 검증](../verify-did-in-rest-api/index)

## 용어 정리

AA 서버: 발급서버

SP 서버: 검증서버
