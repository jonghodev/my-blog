---
title: MongoDB vs DynamoDB
date: 2020-11-04 23:11:66
update: 2020-11-04 23:11:66
tags:
 - db
description:
---

## MongoDB vs DynamoDB

MongoDB 는 JSON 구조의 자유로운 스키마를 사용한다. 스키마가 자유롭다.

Expressive query language 와 strong consistency 를 포함한다.

RDBMS 와 비교를 해보면,

```bash
Table -> Collection

Column -> Key

Value -> Value

Document -> Document
```

라고 할 수 있다.

## Infra

MongoDB 는 오픈소스로 어디에든 설치될 수 있으나 DynamoDB 는 AWS 에서만 설치될 수 있다.

DynamoDB 는 Fully Managed Service 로 하드웨어 프로비저니닝 클러스터 스케일링, 복제 등을 고려하지 않고 사용할 수 있다.

MongoDB 는 Atlas 의 Managed Service 를 이용할 수 있으며, Infra 와 백업과 같은 서비스를 제공한다.

비교해보면, 관리의 편의성은 DynamoDB 가 더 좋다고 할 수 있다.

DynamoDB 는 다른 AWS Service 와 결합성이 좋아 개발하기 편리하다.

DynamoDB 는 IAM 과 같은 AWS 인증 서비스를 사용해 관리가 된다.

## 결론

전체적으로 AWS 서비스들은 관리의 포인트를 적게 둘 때 좋은 선택인 것 같다.

인프라적인 측면만 고려했을 때는 관리 포인트를 적게 두려면 DynamoDB 가 좋은 것 같다.

하지만 AWS 에만 설치될 수 있다는 단점을 고려해야 한다.
