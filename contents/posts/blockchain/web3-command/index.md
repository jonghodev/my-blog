---
title: Web3 명령어 정리
date: 2021-10-27 18:00:00
update: 2021-10-27 18:00:00
tags:
 - blockchain
description:
---

# Web3 명령어 정리

## Open Metadium console

```bash
/opt/meta/bin/gmet.sh console
```

## Get Balance

```js
function getBalance(address) {
  const balance = await web3.eth.getBalance(address);
  console.log(web3.utils.fromWei(balance, "ether"));
}

console.log(getBalance("0xd2ff7e0827a7d0af18e4f159ea608e4631bbd41b"));
```

## Create Account

```js
console.log(web3.eth.accounts.create())
```

## Send ether

```js
personal.unlockAccount(web3.eth.accounts[0]) // demo;

toAddress = '0x1a26AFe1E37B0fd5CA80bA8c691d6Be9E71f89A1'
web3.eth.sendTransaction({
  value: web3.toWei(10000, 'ether'),
  from: web3.eth.accounts[0],
  to: toAddress,
})

balance = web3.eth.getBalance(toAddress)
web3.fromWei(balance, 'ether')
```

## Web3Py get Transcation count (Nonce)

```bash
Web3(HTTPProvider(URI('https://api.metadium.com/dev'), {'timeout': 20})).eth.getTransactionCount('0x89020D7bf3857E729f22f2446c1D737866b8a3a0')
```
