---
title: Web3 decode transaction input
date: 2021-10-28 15:00:00
update: 2021-10-28 15:00:00
tags:
 - blockchain
description:
---

# web3 decode transaction input

Smart Contract Call 에 의해서 트랜잭션이 발생하면 그때 input 으로 사용한 파라미터는 언제든지 다시 조회할 수 있습니다.

`web3.eth.getTransaction()` 함수를 통해서 트랜잭션 데이터를 들고오겠습니다.

```js
web3.eth.getTransaction(
  '0x33400107e60a8d816346ccd67b4bac878f4ddf6cb3030e51036368063e358151'
)
```

```json
{
  "blockHash": "0x7f3e6a5dbf6ba790fbff71923f88bea185336e1474c9e739857ecd171e579512",
  "blockNumber": 6284259,
  "from": "0x1a26AFe1E37B0fd5CA80bA8c691d6Be9E71f89A1",
  "gas": 30367,
  "gasPrice": "80000000000",
  "hash": "0x33400107e60a8d816346ccd67b4bac878f4ddf6cb3030e51036368063e358151",
  "input": "0xa6609154000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000000002434626332646162652d646566302d313165622d626138302d30323432616331333031323700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004038663535623538653465313638623433353064363534343231386536396434386663653861633832323464636464363436393635623563623233323535353932",
  "nonce": 276482,
  "to": "0x4B76b040F5c089565f9A73D25ccbAF0DF307C193",
  "transactionIndex": 0,
  "value": "0",
  "v": "0x3a",
  "r": "0xe2a2e2950e6e03702b9f17b1e352fdafe7678a9a08378622b24e30a49087bb6e",
  "s": "0x3d7ec39f94172fbb054324cd0fce00dad6600829a7e1f28cedc7c4621303728c"
}
```

위 데이터에서 input 이 우리가 원하는 값이고 이것을 디코딩해주어야 합니다.

디코딩하기 위해서는 컨트랙트의 ABI 파일이 필요합니다.

그리고 우리는 Consensys 에서 개발한 [abi-decoder](https://github.com/ConsenSys/abi-decoder)를 통해서 디코딩을 해보겠습니다.

## abi-decoder 설치

```bash
yarn add abi-decoder
```

## Decode input data

아래 코드를 통해서 최종적으로 input parameter 를 구할 수 있습니다.

```ts
import abiDecoder from 'abi-decoder'

function getContractFile(abiFilename: string): Record<string, any> {
  const abiPath = `${process.cwd()}/abi/${abiFilename}.json`
  return require(abiPath)
}

const contract = getContractFile('VCHistory')

abiDecoder.addABI(contract.abi)

const txInput =
  '0xa6609154000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000000002434626332646162652d646566302d313165622d626138302d30323432616331333031323700000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000004038663535623538653465313638623433353064363534343231386536396434386663653861633832323464636464363436393635623563623233323535353932'

const decodedData = abiDecoder.decodeMethod(txInput)
console.log(decodedData)
```

```json
{
  "name": "addVCHistory",
  "params": [
    {
      "name": "vcId",
      "value": "4bc2dabe-def0-11eb-ba80-0242ac130127",
      "type": "string"
    },
    {
      "name": "hash",
      "value": "8f55b58e4e168b4350d6544218e69d48fce8ac8224dcdd646965b5cb23255592",
      "type": "string"
    }
  ]
}
```

## 최종 코드

```js
import abiDecoder from 'abi-decoder'

function getContractFile(abiFilename: string): Record<string, any> {
  const abiPath = `${process.cwd()}/abi/${abiFilename}.json`
  return require(abiPath)
}

async function getDecodedInputParameter(txHash: string, abiFilename: string) {
  const contract = getContractFile(abiFilename)
  abiDecoder.addABI(contract.abi)

  const { input: txInput } = await web3.eth.getTransaction(txHash)

  const decodedData = abiDecoder.decodeMethod(txInput)
  console.log(decodedData)
}
```
