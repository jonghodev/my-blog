---
title: AWS 무중단 배포
date: 2020-12-13 11:00:00
update: 2020-12-13 11:00:00
tags:
 - aws
description:
---

본 포스팅 아키텍처는 다음 내용을 포함한다.

- 도커 컨테이너
- 오토 스케일 인/아웃
- 무중단 배포

## 1. Autoscaling 구성

### EC2 IAM 생성

EC2 가 사용할 IAM 을 만든다. 다음 Policies 들을 포함하자.

- AmazonEC2RoleforAWSCodeDeploy
- AmazonS3FullAccess
- AmazonSSMFullAccess
- AWSCodeDeployFullAccess

### AMI 생성

EC2 컨테이너가 포함해야 할 환경들을 설치하고 AMI 로 만든다.

Codedeploy Agent 는 꼭 설치를 해야한다.

ex) Swap Memory 설정, Java, Tomcat, Docker 설치 같은 것들이 있을 수 있다.

### Launch Template 작성

EC2/Launch Template 을 작성한다.

위에서 만든 IAM 과 AMI 를 포함하자.

### Autoscaling Group

EC2/Autoscaling Group 을 작성한다.

서비스 규모와 트래픽에 따라서 Policy 를 설정해주자.

## 2. ELB (Elastic Load Balancer) 구성

### Target Group 생성

위에서 만든 Autoscaling Group 을 Target 으로 설정한다.

### ALB

ELB Application Load Balancer 를 만든다.

필자는 다음과 같이 구성한다.

- 다중 AZ 구성
- redirect HTTP to HTTPS

## 3. Docker file 작성

### Dockerfile

Dockerfile 은 각각 Dockerfile.dev, Dockerfile.stg, Dockerfile.prod 를 만든다.

다음은 nest.js 예제다.

```dockerfile
# the first image use node image as the builder because it has git program
FROM node:14.15 as builder

WORKDIR /app

COPY ./package.json ./
COPY ./yarn.lock ./
COPY ./tsconfig.json ./

## install dependencies
RUN yarn

COPY . .

## compile typescript
RUN yarn build

# ===================================================
# the second image use node:slim image as the runtime
FROM node:slim as runtime

WORKDIR /app

ENV NODE_ENV="development"

## Copy the necessary files form builder
COPY --from=builder "/app/dist/" "/app/dist/"
COPY --from=builder "/app/template/" "/app/template/"
COPY --from=builder "/app/.env.development" "/app/.env.development"
COPY --from=builder "/app/node_modules/" "/app/node_modules/"
COPY --from=builder "/app/package.json" "/app/package.json"

EXPOSE 8080

CMD ["npm", "run", "start:prod"]

```

### Docker Compose

docker-compose 는 각각 docker-compose.dev, docker-compose.stg, docker-compose.prod 를 만든다.

아래 내용은 예시다.

```yml
version: '3.4'
x-app: &default-app
  image: jonghodev/test-api:latest
  restart: always
  build:
    dockerfile: Dockerfile.dev
    context: .
  container_name: 'app'
  ports:
    - '8080:8080'

services:
  app: *default-app
  app_git_hash:
    <<: *default-app
    image: jonghodev/test-api:${COMMIT_HASH}
```

## 3. Create S3

Public access 필요 없는 S3 버킷을 만든다.

## 4. Codebuild

### IAM

Codebuild 가 접근할 수 있는 Resource 의 Level 을 정할 수 있다.

IAM Role 은 자동생성하고, 만든 후 S3FullAccess 와 CloudFrontFullAccess 를 포함하자.

### Privielged

도커를 사용하므로 Privielged 옵션을 선택한다.

### Buildspec

어떻게 빌드가 일어날지를 설정한다.

다음 파일을 작성하자.

- buildspec.dev.yml
- buildspec.stage.yml
- buildspec.prod.yml

환경에 맞는 buildspec 파일 이름을 입력한다.

buildspec file template 은 다음과 같이 작성하자.

```yml
version: 0.2
phases:
  pre_build:
    commands:
      - 'echo Logging in to Docker Hub...'
      - 'docker login --username="username" --password="password"'
  build:
    commands:
      - 'echo Build started on `date`'
      - 'echo Building the Docker image...'
      - 'echo $CODEBUILD_RESOLVED_SOURCE_VERSION'
      - 'export COMMIT_HASH=${CODEBUILD_RESOLVED_SOURCE_VERSION:0:7}'
      - 'echo $COMMIT_HASH'
      - 'docker-compose -f docker-compose.dev.yml build'
  post_build:
    commands:
      - 'echo Build completed on `date`'
      - 'echo Pushing the Docker image...'
      - 'docker-compose -f docker-compose.dev.yml push'

artifacts:
  files:
    - 'appspec.yml'
    - 'scripts/*'
    - 'docker-compose.dev.yml'

cache:
  paths:
    - '/root/.npm/**/*'
```

pre_build hook 에서 dockerhub 에 로그인을 한다. (보안적으로 좋은 방법은 아니다.)

build hook 에서 docker-compose build 를 한다.

post_build hook 에서 빌드한 docker image 를 dockerhub 에 push 한다.

s3 에 저장할 artifact 들은 appspec.yml 파일과 scripts/\* 파일들이다.

### Artifact

빌드 과정 중에 생긴 파일들을 S3 에 저장한다.

버전관리를 위해 S3 에 저장하자. 아까 생성한 S3 Bucket 에 build 한 파일을 /build 경로에 두고 Build ID Namespace 옵션을 선택하자.

또한 빌드 캐싱을 위해 캐시 옵션을 선택하고 /cache 경로에 저장하자.

### Test

Build 를 수행하고 Docker hub 에 이미지가 잘 업로드 되었는지 확인한다.

## 5. Codedeploy

Codedeploy application 을 만든다.

Codedeploy Deployment Group 을 dev, stg, prod 라는 이름을 주어서 세 개를 만든다.

### IAM

다음 내용을 포함하는 IAM 을 준다.

- AmazonS3FullAccess
- AWSCodeDeployRole

### Deployment Type

In-place 혹은 Blue/green 배포 방식을 선택할 수 있다. 서비스 요구사항에 맞추어서 선태갛ㄴ다.

### Deployment Group

Deployment Group 은 위에서 만든 Autoscaling Group 을 선택한다.

위와 같이 할 시 Autoscaling Group 에 Codedeploy hook 이 걸러셔 자동으로 Instance 가 생성될 때도 Codedeploy 로직이 돈다.

### Appspec.yml

어떻게 배포가 일어날지를 설정한다.

다음과 같이 작성하자.

```yml
#/appspec.yml
version: 0.0
os: linux
files:
  - source: /
    destination: /deploy
hooks:
  AfterInstall:
    - location: scripts/pullDocker.sh
      timeout: 120
      runas: root
  ApplicationStart:
    - location: scripts/runDocker.sh
      timeout: 60
      runas: root
  ApplicationStop:
    - location: scripts/stopDocker.sh
      timeout: 60
      runas: root
```

그리고 다음 Script 들을 작성한다.

scripts/pullDocker.sh

```bash
# docker login
docker login -u jonghodev password # 수정할 것

# pull docker image
if [ "$DEPLOYMENT_GROUP_NAME" == "dev" ]
then
  docker-compose -f /deploy/docker-compose.dev.yml pull
elif [ "$DEPLOYMENT_GROUP_NAME" == "stg" ]
then
  docker-compose -f /deploy/docker-compose.stg.yml pull
elif [ "$DEPLOYMENT_GROUP_NAME" == "prod" ]
then
  docker-compose -f /deploy/docker-compose.yml pull
fi
```

scripts/runDocker.sh

```bash
if [ "$DEPLOYMENT_GROUP_NAME" == "dev" ]
then
  # Remove any anonymous volumes attached to containers
  docker-compose -f /deploy/docker-compose.dev.yml rm -v
  # build images and run containers
  docker-compose -f /deploy/docker-compose.dev.yml up --detach --renew-anon-volumes app
elif [ "$DEPLOYMENT_GROUP_NAME" == "stg" ]
then
  # Remove any anonymous volumes attached to containers
  docker-compose -f /deploy/docker-compose.stg.yml rm -v
  # build images and run containers
  docker-compose -f /deploy/docker-compose.stg.yml up --detach --renew-anon-volumes app
elif [ "$DEPLOYMENT_GROUP_NAME" == "prod" ]
then
  # Remove any anonymous volumes attached to containers
  docker-compose -f /deploy/docker-compose.yml rm -v
  # build images and run containers
  docker-compose -f /deploy/docker-compose.yml up --detach --renew-anon-volumes app
fi
```

scripts/stopDocker.sh

```bash
rm -rf /deploy || true

if docker ps | awk -v app="app" 'NR > 1 && $NF == app{ret=1; exit} END{exit !ret}'; then
  docker rm -f $(docker ps -a -q)
  docker rmi -f $(docker images -a -q)
fi
```

## 6. Codepipeline

위에서 만든 Codebuild, Codedeploy 를 결합한다.

소스코드는 Github Webhook 을 사용한다.
