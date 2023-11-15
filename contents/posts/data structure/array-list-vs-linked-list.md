---
title: Array List vs Linked List
date: 2021-04-06 17:04:14
update: 2021-04-06 17:04:14
tags:
 - data structure
description:
---

## 탐색 시간

Array List 는 Index 기반 자료 구조로, 탐색시 O(1) 의 시간 복잡도를 갖는다.

Linked List 는 모든 요소를 탐색해야할 수 있어서 O(N) 의 시간 복잡도를 갖는다.

## 삽입 & 삭제

Array List 는 일반적인 경우에는 O(1) 에 가능하지만, 최악의 경우 메모리 복사가 일어날 수 있어서 O(n) 의 시간 복잡도를 갖는다.

Lined List 는 tail 혹은 head field 가 가리키고 있는 노드의 주소 값만 바꾸어 주면 되는 구조라서, O(1) 의 시간 복잡도를 갖는다.
