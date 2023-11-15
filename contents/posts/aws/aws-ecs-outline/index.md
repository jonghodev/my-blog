---
title: AWS ECS 에 대해 알아보자
date: 2020-11-11 6:40:45
update: 2020-11-11 6:40:45
tags:
 - aws
description:
---

도커는 컨테이너 가상화 도구다. 그리고 컨테이너의 수가 많아지면서 컨테이너를 관리해주는 오케스트레이션 툴이 필요해진다.

이때 도커에서 만든 Docker Swram이나 구글의 Kubernates, 지금부터 소개할 Elastic Container Service 같은 것을 사용하게 된다.

ECS 는 Fully Managed Container Orchestration Service 다. AWS 에 관리해주는 컨테이너 오케스트레이션 서비스라고 할 수 있다.

Computing 환경으로는 EC2나 [AWS Fargate](https://aws.amazon.com/fargate/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc&fargate-blogs.sort-by=item.additionalFields.createdDate&fargate-blogs.sort-order=desc) 라는 Serverless coumputing engine 을 사용할 수 있다.

보안적으로, AWS VPC 안에 존재하여 안정적이며 IAM 의해 관리될 수 있다.

안정성으로는, ECS 는 Amazon 내부의 AWS SageMaker, Batch, Lex같은 내부 서비스들도 ECS 로 구현이 되어 테스트 되었고 보안, 안정성과, 가용성은 보증이 된다고 한다. 그리고 SLA 는 99.99% 다.

## 작동방식

ECS 는 단순히 EC2 를 연결한 오케스트레이션 서비스다. 그 하나로는 아무 의미가 없다.

ECS 는 EC2 인스턴스안에 있는 Docker 를 관리해주는 서비스 인 것이라고 생각하면 된다.

EC2 인스턴스에 ECS 를 연결하면, 각각의 EC2 에 Container Agent 가 설치가 된다. 그리고 그곳에서 실행중인 작업, 자원 사용율과 같은 것을 ECS 로 전송하고

ECS 가 전송한 요청에 따라서 정지하거나 시작한다.
