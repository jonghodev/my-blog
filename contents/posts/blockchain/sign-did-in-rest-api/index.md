---
title: RestAPI DID 서명
date: 2021-05-08 18:00:00
update: 2021-05-08 18:00:00
tags:
 - blockchain
description:
---

## 1. Message 구성

---

메시지를 구성한다.

`METHOD | URL | DID | timestamp | BODY`

```jsx
const message = method
  .concat(url)
  .concat(did)
  .concat(timestampString)
  .concat(body)
```

## 2. 서명 메시지를 생성

---

- Message 를 Sha3 Hash 한다.
- 해싱된 메시지를 개인키로 ECDSA 서명을 한다.
- v, r, s 를 구해서 서명 메시지를 반환한다.

### Java version

[web3j 라이브러리 코드](https://github.com/web3j/web3j/blob/master/crypto/src/main/java/org/web3j/crypto/Sign.java#L74) 간략화

```java
public static SignatureData signMessage(byte[] message, ECKeyPair keyPair) {
		BigInteger publicKey = keyPair.getPublicKey();
		byte[] messageHash = Hash.sha3(message);

		ECDSASignature sig = keyPair.sign(messageHash);
		// Now we have to work backwards to figure out the recId needed to recover the signature.
		int recId = -1;
		for (int i = 0; i < 4; i++) {
		    BigInteger k = recoverFromSignature(i, sig, messageHash);
		    if (k != null && k.equals(publicKey)) {
		        recId = i;
		        break;
		    }
		}

		int headerByte = recId + 27;

		// 1 header + 32 bytes for R + 32 bytes for S
		byte[] v = new byte[] {(byte) headerByte};
		byte[] r = Numeric.toBytesPadded(sig.r, 32);
		byte[] s = Numeric.toBytesPadded(sig.s, 32);

		return new SignatureData(v, r, s);
}
```

이더리움에서 v 는 복구키로 서명으로부터 Public Key 를 구하기 위한 값으로 약속된 값이다.

### Javascript Version

```jsx
/**
 * Signature를 생성하는 함수
 * @param {String} privateKey Sign 할 때 사용하고자 하는 private key
 * @param {Object} payload Sign 하고자 하는 데이터
 * @return {Object} { code, signature }
 */
async function generateSignature(privateKey, payload) {
  const { serviceId, state, type, did, dataHash } = payload
  // Auth 서버에서 code와 nonce 를 받아옴
  const { code, nonce } = await requestAuth(
    serviceId,
    state,
    type,
    did,
    dataHash
  )
  // Private key를 이용해서 nonce를 서명함
  const signature = signMessage(nonce, privateKey)
  // Code와 서명 반환
  return { code, signature }
}

/**
 * Auth 서버에 요청하여 nonce와 code를 받아옴
 * @param {UUID} serviceId SP의 service id
 * @param {UUID} state 사이트 간 요청 위조 공격 방지를 위해 SP에서 인증 코드 생성 전에 생성한 임의 값
 * @param {Number} type 서비스 등록시 생성한 타입의 코드값 (optional)
 * @param {String} did 특정 did에 대한 인증이 필요할 때 사용할 did (optional)
 * @param {String} dataHash Keepin 에서 signature 생성 시 nonce 값에 포함될 데이터 값 (optional)
 * @return {Object} { code, nonce }
 */
async function requestAuth(serviceId, state, type, did, dataHash) {
  // URI 생성
  let uri = 'https://testauth.metadium.com/didauth/v1/authorize/code'
  uri = uri.concat(`?service_id=${serviceId}`)
  uri = uri.concat(`&state=${state}`)
  if (type !== undefined) uri = uri.concat(`&type=${type}`)
  if (did !== undefined) uri = uri.concat(`&did=${did}`)
  if (dataHash !== undefined) uri = uri.concat(`&data=${dataHash}`)
  try {
    // Auth 서버 요청
    debug(`Request auth to ${uri}`)
    const res = await axios.get(uri)
    // Auth 서버 응답 결과
    debug(`Auth request response: ${JSON.stringify(res.data, null, 4)}`)
    // code, nonce 추출 및 반환
    const { code, nonce } = res.data.data
    return { code, nonce }
  } catch (error) {
    throw new Error(
      `Auth server error: ${JSON.stringify(error.response.data, null, 4)}`
    )
  }
}

/**
 * Private key를 이용해 message를 sign 하는 함수
 * @param {String} message Sign 하고자 하는 message
 * @param {String} privateKey Sign 할 때 사용하는 private key
 */
function signMessage(message, privateKey) {
  debug(`Message to sign: ${message}`)
  const msgHash = ethereumjsabi
    .soliditySHA3(['string'], [message])
    .toString('hex')
  const signature = signMessageHash(msgHash, privateKey)
  return signature
}

/**
 * Private key를 이용해서 message hash를 sign하는 함수
 * @param {String} msgHash Sign하고자 하는 message hash
 * @param {String} privateKey Sign할 때 사용하는 private key
 * @return {String} signature
 */
function signMessageHash(msgHash, privateKey) {
  debug(`Message hash to sign: ${msgHash}`)
  // 0x prefix 가 없으면 privateKey에 추가
  const prvKey = !privateKey.startsWith('0x')
    ? privateKey.concat('0x')
    : privateKey
  // Private key를 이용해 msgHash를 sign
  const { v, r, s } = ethJsUtil.ecsign(
    Buffer.from(msgHash, 'hex'),
    Buffer.from(prvKey.substr(2), 'hex')
  )
  // v, r, s로부터 signature 생성
  const signature = ethJsUtil.toRpcSig(v, r, s)
  debug(`Signing finished: ${signature}`)
  return signature
}
```

## 3. 인증정보 전달

마지막으로 위에서 구한 서명 메시지를 포함해서 Header 로 전달하면 된다.

Header 에 포함할 데이터

```
DID: 서명하는 DID
TIMESTAMP: Uinx Timestamp
SIG: METHOD ^ URI ^ DID ^ TIMESTAMP 를 did private key 로 서명한 값
```
