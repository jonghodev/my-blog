---
title: 폐쇄망에 도커 설치하고 컨테이너 띄우기 (Centos)
date: 2021-09-15 14:09:17
update: 2021-09-15 14:09:17
tags:
 - docker
description:
---

## 배경

### 폐쇄망이란?

일반적인 환경에서는 인터넷 엑세스가 허용되므로 (아웃바운드 허용) 도커를 설치하기 쉽습니다. 하지만 금융권이나 보안이 강한 곳은 폐쇄망이라고도 불리며, 인터넷 엑세스가 안 되므로 미리 설치 파일들을 준비해야 하는데요.

폐쇄망에서 서버를 설치할 때 필요한 관련 패키지들을 관리하는 것은 번거롭습니다. 자바 서버를 설치한다고 하면 자바 SDK 도 설치하고 Nginx 같은 웹서버를 설치해야 할 수도 있고 그밖에 패키지는 많습니다.

### 폐쇄망에서 도커를 사용하는 이유

도커를 사용하면 더 편리하게 작업할 수 있습니다. 우리가 해야할건 도커로 서버를 만들고, 그 도커 이미지와 도커 데몬만 페쇄망 컴퓨터에 설치하면 됩니다.

도커를 설치하는 방법과 도커 이미지를 만들어서 파일로 저장하고 서버로 전송하는 방법을 알아봅시다.

## 1. 도커 이미지 생성

### 1-1. Docker Image 파일로 생성

우선 도커 이미지를 생성합니다.

```bash
# 현재 경로에서 Dockerfile 을 참조하여 도커 이미지를 빌드합니다.
docker build . -t "이미지 이름":"태그"
# 위에서 만든 도커 이미지를 파일 경로에 파일로 내보냅니다.
docker save "이미지 이름":"태그" -o "파일 경로"
```

```bash
# SAMPLE
docker build . -t sample_image:dev
docker save sample_image:dev -o sample_image.tar
```

### 1-2 Docker Image 폐쇄망으로 전송

USB 를 활용할 수도 있지만 만약 자신의 피씨가 인바운드가 열려있다면 SCP 로 보내줍시다.

```bash
# SCP 커맨드를 사용하며 파일을 전송합니다.
scp -i "키 파일 경로" "전송할 파일 경로" "사용자"[@](mailto:centos@54.180.9.171)"주소":"전송할 경로"
```

```bash
# SAMPLE
scp -i ~/.ssh/914.pem ~/sample_image.tar centos@52.79.240.109:/home/centos/sample_image.tar
```

## 2. 도커 설치하기

### 2-1. 패키치 설치

자신의 피씨에 도커를 설치하기 위한 패키지들을 설치합니다. CentOS 에서 진행해주세요. 자신의 컴퓨터가 CentOs 가 아니라면 가상환경을 만들어서 설치하고 다시 자신의 컴퓨터로 옮기면 됩니다.

```bash
mkdir ~/docker_packages
cd ~/docker_packages
# 현재 디렉토리에 도커를 설치하기 위한 rpm 파일들을 모두 다운로드합니다.
sudo yumdownloader docker --resolve
```

### 2-2. 도커 관련 패키치 폐쇄망으로 전송

1-2 에서 했던 방법과 마찬가지로 폐쇄망으로 패키지들을 전송해줍니다. 단 폴더를 전송해주는거니 scp 커맨드에 -r 옵션을 넣어줍니다.

```bash
# SCP 커맨드를 사용하며 파일을 전송합니다.
scp -i "키 파일 경로" -r "전송할 파일 경로" "사용자"[@](mailto:centos@54.180.9.171)"주소":"전송할 경로"
```

```bash
# SAMPLE
scp -i ~/.ssh/914.pem -r ~/docker_packages centos@52.79.240.109:/home/centos/docker_packages
```

## 3. Docker Daemon 설치 및 도커 이미지 로딩

이제 필요한 작업들이 모두 끝났습니다. SSH 로 접속해서 도커 데몬을 설치하고 도커 이미지를 로딩해서 컨테이너를 띄우면 끝납니다.

### 3-1 SSH 접속

SSH 로 폐쇄망 서버에 접속합니다. 자기가 그 컴퓨터가 있는 곳에사 가서 직접한다면 이 단계는 건너뛰면 됩니다.

```bash
ssh -i "키 파일 경로" "사용자"@"주소"
```

```bash
# SAMPLE
ssh -i ~/.ssh/914.pem centos@15.164.250
```

### 3-2 Docker Daemon 설치

도커 데몬을 설치합니다.

```bash
cd ~/docker_packages
sudo rpm -ivh --replacefiles --replacepkgs *.rpm
```

### 3-3 Docker Deamon 실행

도커 데몬을 실행합니다.

```bash
sudo systemctl start docker
sudo chmod 666 /var/run/docker.sock

# 도커 데몬이 잘 떴는지 확인!
docker ps
```

### 3-4 Docker Image Load

아까 파일로 변환한 도커 이미지를 우리의 도커 이미지로 로딩해줍니다!

```bash
docker load -i "파일 경로"
# 잘 로딩되었는지 확인
docker images
```

```bash
# SAMPLE
docker load -i ~/docker_image.tar
```

### 3-5 도커 컨테이너 실행

위에서 로딩한 이미지로 컨테이너를 실행하면 끝입니다~!

```bash
docker run "이미지 이름":"태그"
```

```bash
# SAMPLE
docker run sample_image:dev
```

## 마무리

이렇게 폐쇄망에 도커를 설치하고 컨테이너까지 띄우는 방법을 알아봤습니다. 어려운 내용이 있다면 알려주세요.
