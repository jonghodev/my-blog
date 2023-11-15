---
title: Java Testing 5 - Testcontainers
date: 2021-06-21 21:00:00
update: 2021-06-21 21:00:00
tags:
 - java
description:
---

## 1. 소개

테스트에서 도커 컨테이너를 실행할 수 있는 라이브러리입니다.

### 장점

- 매번 테스트를 할 때마다 테스트할 DB를 설정하거나 별도의 프로그램 또는 스크립트 실행할 필요가 없습니다.
- 보다 Production 에 가까운 테스트를 만들 수 있습니다.
  → H2 와 같은 In-memory DB 를 사용하면 개발 서버에서는 문제가 생기지 않던 것이 Production 에 가서는 에러가 날 수 있습니다. 왜냐면 DBMS 가 틀리기 때문에 Transaction Isolation Level, Propagation, SQL 문법이 상이하기 때문입니다.

### 단점

- 테스트가 느려집니다.
  → 매 테스트 마다 Docker Container 를 생성하고 삭제해야 하므로 테스트가 느려집니다.

## 2. 설치

1. Testcontainers JUnit 5 지원 모듈을 설치합니다.
2. 여러 모듈을 지원하는데, 자신이 사용하려는 모듈을 설치합니다.
   모듈에는 MaraiaDB, PostgresSQL 과 같은 DBMS가 있고 Nginx, Kafka 와 같은 다양한 컨테이너도 있습니다. 모듈과 하나의 이미지의 컨테이너와 매핑된다고 보시면 됩니다.

만약 자신이 컨테이너 모듈이 없을경우 GenericContainer로 파라미터에 이미지 이름을 넘겨서 생성 가능합니다.

이때 Local 에서 캐싱된 이미지를 먼저 찾습니다.

### build.gradle

```groovy
testImplementation 'org.testcontainers:testcontainers:1.15.3'
testImplementation 'org.testcontainers:mariadb:1.15.3'
```

### application.yml

이미 host가 사용 중인 port에 매핑되는 것을 피하기 위해 Exposed 되는 Port 는 랜덤으로 설정 됩니다.

따라서 직접 설정할 수는 없고 확인은 가능합니다.

Port를 미리 알 수 없기 때문에, 다음과 같이 설정 파일에서 jdbc url 에 tc라는 옵션과 함께 특이한 문법이 사용되는 것을 볼 수 있습니다.

```yaml
spring:
  jpa:
    open-in-view: true
    generate-ddl: true
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    hibernate:
      ddl-auto: update
  datasource:
    driver-class-name: org.testcontainers.jdbc.ContainerDatabaseDriver
    hikari:
      jdbc-url: jdbc:tc:mariadb:///yogurt
```

> 도커를 실행하므로 테스트 실행 시 잠시동안 Docker Container 가 뜨게 됨.
