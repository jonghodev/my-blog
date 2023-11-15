---
title: AWS ECR 에 대해 알아보자
date: 2020-11-09 10:11:86
update: 2020-11-09 10:11:86
tags:
 - aws
description:
---

ECS 와 통합된 Fully Managed Docker Container registiry 이다.

ECR 는 사용자 업로드한 이미지를 고가용성(Highly Avaliable, HA) 과 확장성(Scalable) 을 갖춘 아키텍처에 호스팅한다.

따라서 개발자는 컨테이너를 구축하고 스케일링하는 것을 생각하지 않고 개발할 수 있다.

또한 보안적 측면에서 AWS IAM 과의 통합을 제공한다.

> Container Image 를 S3 에 저장해 고가용성을 유자한다.

단순히 생각하면 Docker Image 를 저장해주는 Docker Hub 와 비슷한 기능을 한다고 할 수 있다.

AWS Service ECS 같은 것과 더 결합이 편할 것 같다.

## 기술적 측면

- HTTPS Transfer 을 지원한다.
- IAM 을 결합해 사용자별 보안 정책을 유지할 수 있다.
- ECS 와 Docker CLI 와 결합해서 개발과 프로덕션 Workflow 를 간단하게 해준다.
- Private 이미지를 사용할 수 있다.
- CloudTrail 을 사용해서 이미지 Pull 작업과 같은 모든 API History 를 볼 수 있다.

> AWS 가 Dockerfile 을 직접 빌드해주지는 않기 때문에 Dockerfile 은 직접 작성해야 한다.

## 보안적 측면

- IAM 을 사용해 자원(Resource)별로 권한 및 보안 정책을 관리할 수 있다.
- Container Image 를 Encryption 하여, S3 에 HTTPS 릍 통해 업로드한다.
- Cross-account image sharing 이 가능하다. [알아보기](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

## 가격

리포지터리에 저장된 데이터의 양과 인터넷 수신에 대한 비용을 지불한다. 정리하면, Data Transfer 와 Storage 에 대한 비용을 받는다.

_Asia Pacific Seoul 기준_

### Storage

1달에, 1GB 당 \$0.10

### Data Transfer

Data Inbound 에 대한 비용은 모두 무료다.

Data Outbound 에 대한 비용만 측정된다.

| Up to 1 GB / Month          | \$0.00 per GB  |
| --------------------------- | :------------: |
| Next 9.999 TB / Month       | \$0.126 per GB |
| Next 40 TB / Month          | \$0.122 per GB |
| Next 100 TB / Month         | \$0.117 per GB |
| Greater than 150 TB / Month | \$0.108 per GB |

### Free Tier

Storage: 1달에 500 MB 를 제공한다.

Data Transfer: 1년에 15 GB 를 제공한다.

## References

https://aws.amazon.com/ecr/

https://aws.amazon.com/ecr/faqs/#:~:text=Amazon%20ECR%20is%20integrated%20with,about%20scaling%20the%20underlying%20infrastructure.
