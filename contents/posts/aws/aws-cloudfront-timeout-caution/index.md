---
title: AWS CloudFront Timeout 관련 주의사항
date: 2020-11-08 10:11:57
update: 2020-11-08 10:11:57
tags:
 - aws
description:
---

AWS 에서 CloudFront 와 ELB 를 사용할 때 주의점을 알아보겠습니다. 회사에서 일어난 Trouble Shooting 을 기반합니다.

저희가 개발 중인 서비스는 특성상 하나의 요청 시간이 긴 경우가 많아, 30초를 넘는 경우도 있었습니다.

저희는 CloudFront 를 활용해 백엔드 서비스를 ELB 에 연결해서 (Behavior option) Backend Service 를 Serving 하고 있었습니다. 즉 요청 구조가 CloudFront -> ELB -> Backend API 였던 것이죠!

Default CloudFront 의 Timeout 은 30초, ELB 의 Timeout 은 60초입니다. 따라서 저는 이 문제를 해결하기 위해서 CloudFront 와 ELB 의 Timeout 을 늘렸습니다.

다만, CloudFront 는 AWS 에 고객 문의를 통해서 Timeout 을 늘릴 수 있습니다.

## 관련 글

https://aws.amazon.com/ko/premiumsupport/knowledge-center/resolve-http-504-errors-cloudfront/

https://aws.amazon.com/blogs/aws/elb-idle-timeout-control/#:~:text=When%20your%20web%20browser%20or,is%20set%20to%2060%20seconds
