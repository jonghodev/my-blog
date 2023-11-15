---
title: Git flow 정리
date: 2020-09-20 17:10:94
update: 2020-09-20 17:10:94
tags:
 - development
description:
---

![](./1.png)

브랜치를 production 에 가까운 순서대로 나열을 하면
feature, develop, release, hotfix, master 가 있다.

## 1. feature branch

특정 기능을 개발하는 브랜치다.
보통 feature/auth, feature/post, feature/pay 같은 이름으로 브랜치를 따 개발을 한다.

feature branch 란 주로 develop 에서 start 를 해 어떤 기능이 만들 때 사용한다. 그 어떤 기능이란 로그인, 결제, 배너, 각종 신규 기능들이 될 수 있고
여러 개발자들에 의해 여러 기능이 개발될 수 있으므로 서로에게 영향을 끼치지 않기 위해 각각의 기능 개발을 따로 하고 그것을 나중에 머지해서 다시 한 번 통합 테스트를 하는 것 같다.

## 2. develop branch

feature branch 와 마찬가지로 개발자들이 직접 작업하는 공간 중 하나다.
직접 작업한다는 것은, 그 반대의 의미로 살펴보면 release, master 와 같은 브랜치는 직접 작업을 하지 않고 merge 를 통해 소스코드를 내려보내는 형태라고 할 수 있고. 직접 코드를 작성해 푸쉬를 한다는 의미이다.

## 3. release branch

QA 같은 내부 테스트 팀이 테스트를 하는 브랜치다. production 으로 배포하기 전에 검수하는 최종 관문이라고 할 수 있다.
개발자는 오직 feature 혹은 develop branch 에서만 작업을 한다.그 작업한 것을 pull request 를 보내 코드리뷰 후 머지하는 방식으로 release, master 로 소스코드를 옮긴다.

## 4. master branch

production 으로 실질적으로 배포되는 브랜치이고 조심스럽게 다루어야 하는 곳이다.
보통 이곳에 푸쉬를 하면 webhook 을 통해 ci/cd 를 돌려 production 서버에 배포를 한다.
언제든 원하는 버전으로 돌아갈 수 있게 tag 를 달아 변경이 쉽게 만든다.

## 5. hotfix branch

긴급 상황이 발생했을 때 만드는 브랜치다.
개발팀에서 어떤 기능을 추가해 develop 에 merge 하고 release 에 merge 하고 테스트를 거쳤음에도 production 에서 에러, 이슈가 발생할 수 있다.
이 production 에러를 빠르게 수정하기 위해 feature 나 develop 에서 시작하는 것이 아닌, master 브랜치나 release 브랜치에서부터 시작을 해 에러가 난 부분만 수정을 해
바로 master 로 병합을 해 에러를 해결하는 방법이다.

나도 아직까진 gitflow 를 통해 hotfix 브랜치를 파 무언가를 해본적은 없지만, 그 상황을 상상했을 때
만약 production 에러가 발생했다는 것을 알았을 때, 그게 critical 한 것이면 바로 tag 를 이전버전으로 돌릴 것이고
잠깐 빠르게 수정해서 될 문제라면 빠르게 hotfix 브랜치를 release 에서 파서 개발 및 테스트를 하고 master 로 머지할 것이다.

## 6. 내 생각

나는 git flow 도 하나의 방법론 중 하나라고 생각하고, 무조건 이걸 따를 필요는 없고 조직에 맞게 변형시켜 사용하면 된다고 생각한다.
