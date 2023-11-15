---
title: Web3 get private key from miner account
date: 2021-10-28 15:00:00
update: 2021-10-28 15:00:00
tags:
 - blockchain
description:
---

# Web3 Miner 어카운트로부터 Private key 구하는 법

Metadium 에서 Miner account 어카운트의 Private Key 를 구하는 방법을 알아보겠습니다.

우선 블록체인 노드가 설치된 서버로 접속을 합니다. 그 후 `/opt/meta/keystore` 로 들어가서 원하는 account 를 골라서 그 계정의 정보를 복사합니다.

```bash
cd /opt/meta/keystore
cat account-1 # 결과값 복사
```

아래 함수를 통해서 Private Key 를 구할 수 있습니다.

data 파라미터에 위에서 복사한 정보를 넣어주고 accountPassword 파라미터에는 그 노드의 비밀번호를 넣어주면 됩니다.

```js
function getPrivateKey(data, accountPassword) {
  const res = web3.eth.accounts.decrypt(data, accountPassword)
  return res
}

const data = {
  address: '0xff34c241872d66bb0d000753370df45e4a323535',
  crypto: {
    cipher: 'aes-128-ctr',
    ciphertext:
      '6e17d40c1f780a98a69b9d5e94683edeef5101d228fe33a6c0558492bf7751d8',
    cipherparams: { iv: 'ef23e5b2e75c82e5ce293dfb1803520e' },
    kdf: 'scrypt',
    kdfparams: {
      dklen: 32,
      n: 262144,
      p: 1,
      r: 8,
      salt: 'ac7b9615b8242e5487689dae03563c5004d738c3abdc4f1a576ff8b5e8f8a000',
    },
    mac: 'eb6c493291ec865771ff7a1dd61d955ec752e3c7bc617206d7543f9705b76b32',
  },
  id: 'a0672e34-179f-4478-a8b3-87fb0482632a',
  version: 3,
}
const accountPassword = 'demo'

console.log(getPrivateKey(data, accountPassword))
```

위 함수를 돌리면 결과 값으로 아래와 같이 나오게 됩니다.

```js
{
  address: '0xff34c241872D66bb0D000753370DF45E4a323536',
  privateKey: '0x14c3de89d2f41022a9840344c9fe4d9292ea717b965e1f6f7f7c07b8192f15d2',
  signTransaction: [Function: signTransaction],
  sign: [Function: sign],
  encrypt: [Function: encrypt]
}
```

전체 소스 코드를 확인하려면 아래 깃허브 링크를 참고하면 됩니다.

https://github.com/jonghodev/get-private-key-from-mining-node
