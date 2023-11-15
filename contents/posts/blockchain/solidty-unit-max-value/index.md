---
title: Solidity uint 최대값
date: 2021-06-09 22:00:00
update: 2021-06-09 22:00:00
tags:
 - blockchain
description:
---

`uint` 는 `uint256` 의 줄임말이다. 따라서 `2^256 - 1` 이 최대값이다. 총 저장할 수 있는 값은 2^256 이지만 0 도 저장해야 하므로 최대 값에서 1을 빼준다.

`int` 의 최대값은 `2^256 / 2 - 1` 혹은 `2^255 - 1` 이다. 값의 범위는 `-2^255 ~ 2^255 - 1` 이다.

## References

https://ethereum.stackexchange.com/questions/58981/what-is-the-maximum-value-an-int-and-uint-can-store/58985
