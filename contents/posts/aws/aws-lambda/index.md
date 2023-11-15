---
title: AWS Lambda 정리
date: 2020-11-03 20:11:09
update: 2020-11-03 20:11:09
tags:
 - aws
description:
---

## 블로깅 작성 이유

내가 Rest API 서버를 Freetier 로 사용해보려고 분석했던 점을 정리한다.

## Lambda 란

AWS Lambda 란, 이벤트에 응답하여 코드를 실행하고 자동으로 서버가 구성되는 서버리스 서비스다.

[AWS 공식 문서](https://aws.amazon.com/lambda/)에서는 다음과 같이 말하고 있다.

> AWS Lambda lets you run code without **provisioning** or **managing servers**. You pay only for the compute time you consume.
>
> With Lambda, you can run code for virtually any type of application or backend service - all with **zero administration**.
>
> Just <u>upload your code</u> and Lambda takes care of everything required to run and scale your code with **high availability**.
>
> You can set up your code to automatically trigger from other AWS services or call it directly from any web or mobile app.

위 내용을 간략히 정리해보겠다.

**프로비저닝(Provisioning)** 없이, 서버를 자기가 사용한 만큼에 대한 비용을 지불할 수 있는 Managed 서비스이다.

> 프로비저닝이란 OS 를 서버에 설치하고 동작할 수 있게 준비해두는 것을 말한다.

**고가용성(High Availability)** 을 자동으로 갖고 있는 서비스이다.

> AWS Managed Service 는 기본적으로 모두 고가용성을 갖고 있다.

여러 언어를 실행시킬 수 있고. AWS Service 를 포함한 클라이언트에 의해 호출될 수 있다고 한다.

### 정리하면

AWS Lambda 의 장점은 코드를 Lambda 에 업로드하면 자동으로 서버가 구성된다는 것이다. 그리고 자동으로 서버가 Scaling 하니 관리 포인트가 적다.

과금 방식은 100ms 단위의 코드가 실행되는 시간 및 코드가 트리거 되는 횟수를 기준으로 한다. 코드가 실행되지 않을 때는 요금이 부가되지 않는다.

## 사용 가능한 백엔드 서비스 (Available Backend Server)

[AWS 공식 문서](https://aws.amazon.com/lambda/features/)에서는 다음과 같이 말하고 있다.

> With AWS Lambda, there are no new languages, tools, or frameworks to learn. You can use any third party library, even native ones. You can also package any code (frameworks, SDKs, libraries, and > > more) as a Lambda Layer and manage and share them easily across multiple functions. Lambda natively supports Java, Go, PowerShell, Node.js, C#, Python, and Ruby code, and provides a Runtime API ?> > which allows you to use any additional programming languages to author your functions.

정리하면, Lambda 에 소스코드만 올려두면 대부분의 Backend Runtime 에서 작동할 수 있다. 대표적으로 Node.js, Java, Python, GO, Ruby 와 같은 Major 백엔드 언어를 지원한다.

## 작동 방법 (How it work?)

AWS Lambda 에 코드를 업로드 하고, Trigger 를 걸어야 한다. Trigger 는 AWS Service 가 될 수도 있고, 웹, 앱 등 클라이언트에 제한이 없다.

> Use Case: Cloud Watch 를 연결해 Scheduling 시스템을 효율적인 비용으로 구성할 수 있다.

만약 REST API Server 로 이용하려 한다면 AWS API Gateway 를 Trigger 로 이용할 수 있다. API Gateway 또한 비용이 있으니 참고하자.

## 비용 (Price)

[AWS 공식 문서](https://aws.amazon.com/lambda/pricing/)에서는 다음과 같이 말하고 있다.

> With AWS Lambda, you pay only for what you use. You are charged based on the number of requests for your functions and the duration, the time it takes for your code to execute.

과금은 100ms 단위의 코드가 실행되는 시간 및 코드가 트리거 되는 횟수를 기준으로 한다. 코드가 실행되지 않을 때는 요금이 부가되지 않는다.

예를들어, 5백만 개의 Request 와 그 Request 동안 10만 초동안 CPU 가 작동을 했다면 그것에 대한 비용을 받는 다는 것이다. 또한 Memory 크기에 따라 비용을 추가로 받는다.

Cloud 란 것은 자기가 사용한 만큼에 대해서만 비용을 부과하는 서비스다. 예를들어 EC2와 같은 서비스는 초당 요금을 부과된다.

반면에, AWS Lambda 는 정말로 자신이 사용한 만큼만 비용에 대한 비용만 지불하는, 진정한 의미의 Cloud Service 라고도 볼 수 있다.

## Performance

Lambda 는 내부적으로 AWS EC2 위에서 작동한다. CPU 는 EC2 처럼 따로 조정할 수 없고, 램에 따라 늘어난다.

AWS 측에서는 구체적으로 명시하고 있지 않고, 램이 증가할 수록 더 큰 CPU Power 를 제공한다고 한다.

약 2배의 CPU 파워를 얻기 위해선 2배의 메모리를 증설하면 된다.

램은 64 MB 단위로 최소 128MB 에서 3,008MB 가 최대다. [출처](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)

즉, 램이 3기가가 최대이니 Hight Execute 하거나 Cpu Intensive 한 Task 를 수행하기에는 무리가 있고, 실제 유즈 케이스에 알맞지 않을 확률이 높다.

[출처](http://blog.naver.com/teamdable/220928186717) 의 글을 참고하면 램과 CPU 의 관계를 실험한 것이 있다.

Cold Start 를 위한 방안을 생각해야 한다. 이것에 관한 자세한 내용은 생략한다.

## 보안 이슈 (Security Issue)

AWS Lambda 는 다른 고객들과 공유되는 VPC 에 존재할 수 있다.

따라서 매우 보안적으로 민감한 시스템이라면, EC2 를 사용하거나 VPC 를 구성해야 하는데 [Lambda 를 VPC 내부에서 사용하면](https://www.rajeshbhojwani.co.in/2019/04/aws-lambda-best-practices.html) Cold Start, 동시 실행 제한과 같은 이슈가 발생할 수 있다.

## 유즈케이스 (Use cases)

AWS Cloud Service 는 매우 유용하다.

모든 기술이 마찬가지만, 여러가지 기업의 상황에 맞춰 이해타산을 잘 따지며 유즈케이스에 맞게 기술을 선택해야 한다.

AWS Lambda 는 서버리스 기반의 고가용성을 가진, 관리 포인트가 적은 서비스다.

내가 생각하는 유즈케이스는 자주 호출되는 서비스에 사용하기 보다는, 가끔 이용되는 서비스이거나, 스케쥴링을 통해 돌아가는 서비스이거나, S3 에 업로드 됬을 때 트리거를 걸리는 서비스가 적합해 보인다.

혹은 간단히 시장 반응성을 보기 위한 파일럿 프로젝트에도 알맞는 것 같다.

만약 기업에서 신규 인공지능 서비스나 챗봇 도입하려하는데 사용자의 수가 예측이 안 된다면 사용하기에 좋다.

왜냐면 사용한 만큼 요금을 부과받기 때문에 미리 많은 트래픽을 대비해 컴퓨팅 자원을 높게 설정해서 서버를 운용할 필요도 없고 요청이 적게 들어온다고 해도 서버 비용이 적게 들어가기 때문이다.

만약 EC2 를 사용한다면 OS 와 자바와 같은 서드파티 라이브러리, 톰캣과 같은 웹서버, Node.js 런타임 등 각종 많은 여러 작업이 필요해질 것이다.

그리고 EC2 의 Status 를 Check 를 하기 위해 Health Checking 을 하며 모니터링을 해야하고, 어느정도 고가용성을 유지하기 위해 로드밸런싱을 적용해야 할 수도 있다.

만약 EC2 에 고가용성이 없을 때, 트래픽이 증가하면 그것의 부하에 대응하기 매우 힘들지만, AWS Lambda 는 그러한 작업의 필요성을 없애준다.

나중에 트래픽이 증가하거나 다른 이유로 EC2 나 온프레미스로 옮겨야 하면, 그때 인프라 작업을 시작해도 늦지 않을 것이다.

## Free tier

1 million request 무료.

3.2 million seconds 작동 시간 무료 (한달 = 2.6 million seconds)

따라서 한달 내내 돌릴 수 있고, 백 만개의 요청은 무료다.

## References

https://lumigo.io/blog/aws-lambda-vs-ec2/#:~:text=And%2C%20if%20we%20look%20at,200%20ms%20with%201GB%20Memory
