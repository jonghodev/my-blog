---
title: Install nodejs in offline
date: 2021-10-28 17:00:00
update: 2021-10-28 17:00:00
tags:
 - nodejs
description:
---

Offline 환경에 Nodejs 애플리케이션을 배포할 때는 여러가지 방법이 있다. 대표적으로는 아래와 같은 방법이 있다.

- Docker
- node_modules 통채로 복사
- yarn offline mirror

Docker 를 활용하는 방법은 "폐쇄망에 도커 설치하고 컨테이너 띄우기 (Centos)" 라는 글에 올려두었고

node_modules 를 통채로 복사하는 방법은 OS 가 다르거나 Nodejs 버전에 따라서 오작동할 확률이 높고, node_modules 용량과 그 안에 존재하는 하위 파일들이 너무나 많기 때문에 추천하지 않는 방법이다.

그래서 이번에는 yarn offline mirror 를 사용하는 방법을 알아볼 것이다.

## Yarn offline mirror

### .yarnrc 파일 생성

```bash
cd <project-dir>
cat > .yarnrc
yarn-offline-mirror "./npm_packages"
^z
```

### 기존 파일 및 캐시 삭제

```bash
rm -rf node_modules
rm yarn.lock
yarn cache clean
```

### npm_packages 설치

다음 커맨드를 입력하면 npm_packages 폴더에 \*.tgz 파일이 많이 생기게 된다.

```bash
yarn install
```

### Offline 환경에서

다음 커맨드를 입력하면 npm_packages 하위 파일을 이용해 node_modules 를 만들게 된다.

```bash
yarn install --offline
```

## References

https://musma.github.io/2019/08/23/nodejs-offline-deployment.html
