---
title: AWS React 무중단 배포
date: 2021-03-26 08:03:54
update: 2021-03-26 08:03:54
tags:
 - aws
description:
---

## 1. Create S3 Bucket

빌드된 React 파일을 호스팅 할 S3 를 생성한다.

이때 Public acccess 를 허용하는 버킷을 생성해야한다.

S3 버킷을 생성한 후 Properties 탭에서 Static website hosting 옵션을 활성화 해야한다.

Index document 와 Error document 를 index.html 로 지정해준다.

Error document 도 index.html 로 지정해주는 이유는 개발자가 커스텀하게 에러 페이지를 정의하기 위해서다.

## 2. Create Codebuild

### 2-1. Source

Github 를 선택한다.

### 2-2. Environment

- Build 할 OS 를 선택한다.
- IAM Role 을 선택한다.

IAM Role 은 Codebuild 에 관한 Policy 뿐만 아니라 S3FullAcess 가 필요하다.

왜냐면 아래의 buildspec 파일에서 AWS CLI 를 이용해 S3 에 접근할 것이기 때문이다.

### 2-3 Buildspec

Buildspec file 의 이름을 지정한다.

보통 아래와 같이 설정한다.

- buildspec-dev.yml
- buildspec-stg.yml
- buildspec-prod.yml

파일에 작성할 코드는 다음과 같다.

```yml
version: 0.1
phases:
  pre_build:
    commands:
      - npm install
  build:
    commands:
      - npm run build
  post_build:
    commands:
      # copy the contents of /build to S3
      - aws s3 cp --recursive --acl public-read ./build s3://<Your Bucket Name>/

      # set the cache-control headers for index.html to prevent browser caching
      - >
        aws s3 cp --acl public-read
        --cache-control="max-age=0, no-cache, no-store, must-revalidate"
        ./build/index.html s3://<Your Bucket Name>/

      # invalidate the CloudFront cache for index.html
      # to force CloudFront to update its edge locations with the new versions
      - >
        aws cloudfront create-invalidation --distribution-id <Your Cloudfront ID>
        --paths /index.html
artifacts:
  files:
    - '**/*'
  base-directory: build
```
