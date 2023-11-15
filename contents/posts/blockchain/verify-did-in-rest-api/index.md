---
title: RestAPI DID 검증
date: 2021-05-08 18:00:00
update: 2021-05-08 18:00:00
tags:
 - blockchain
description:
---

## 1. 시간 검사

---

Timestamp 를 확인해 3분이 지났는지 검사한다.

```jsx
const { timestamp } = headers

// 밀리세크 단위이므로 1000을 나눈다.
const now = Date.now() / 1000
// 3분이 넘는지 확인
if (Math.abs(now - parseInt(timestamp, 10)) > 60 * 3) {
  throw new BusinessError(ErrorCode.AuthenticationTimeout)
}
```

## 2. Message 구성

---

메시지를 구성한다.

`METHOD ^ URI ^ DID ^ TIMESTAMP`

```jsx
const { did, timestamp } = headers
const { method, url, rawBody = '' } = req

const message = method
  .concat(url)
  .concat(did)
  .concat(timestamp)
  .concat(rawBody)
```

## 3. DID 검증

- Header 의 DID 를 Resolver 에 요청해서 DID Document 를 구한다.
- signature 로부터 address 를 추출해서 did document 의 address 와 비교함으로써 검증한다.

### 3-1. DID Document Library 를 이용해 검증

```jsx
const didDocument = await didResolver.getDocument(
  did,
  'https://resolver.metadium.com/1.0/',
  false
)

const isVerified = didDocument.hasRecoverAddressFromSignature(
  did,
  'MetaManagementKey',
  message,
  sig
)
```

앱에서는 위와 같이 라이브러리를 호출하면 간단하게 해결된다.

### 3-2. 서명값과 DID 를 이용해 검증

DID Document 라이브러리 코드를 까서 확인해보겠다.

```jsx
class DidDocument {
	...

	// 실질적으로 DID 와 서명을 검증하는 함수.
	hasRecoverAddressFromSignature(
	  did: string,
	  svc_id: string,
	  message: string,
	  signature: string,
	): boolean {
		// Signature 로부터 Address 를 얻는다.
	  const address = sigUtils.addressFromSignature(message, signature);
		// 위에서 구한 Address 와 DID Document 에 있는 Address 와 비교한다.
	  return this.hasAuthWithAddress(did, svc_id, address);
	}
}
```

### 3-3. Message 와 Signature 를 이용해 Address 를 구한다.

```jsx
// Signature 로부터 Address 를 구하는 함수
function addressFromSignature(message: string, signature: string): string {
  // 서명값으로부터 Public Key 를 구해서 Address 로 변환한다.
  return toAddress(publicKeyFromSignature(message, signature))
}

// Signature 로부터 Public Key 를 구하는 함수
function publicKeyFromSignature(message: string, signature: string): Buffer {
  const sign = signature.startsWith('0x') ? signature.substr(2) : signature
  const msg = Buffer.from(message)
  const megHash = ethJsUtil.keccak256(msg)

  const vrs = Buffer.from(sign, 'hex')
  if (vrs.length !== 65) throw new Error('signature must be 65 bytes')

  // Signature 는 V+R+S 의 조합이므로 Slice 해서 값을 구한다.
  const r = vrs.slice(0, 32)
  const s = vrs.slice(32, 64)
  const v = vrs[64]

  // 이더리움 라이브러리로 v, r, s 와 Message Hash 를 전달해서 Public Key 를 구한다.
  return ethJsUtil.ecrecover(megHash, v, r, s)
}

// Public Key 로부터 Address 를 구하는 함수
function toAddress(publicKey: Buffer): string {
  // Address 를 Buffer 로 받는다.
  const addrBuf = ethJsUtil.pubToAddress(publicKey)
  // 버퍼를 16진수로 변환하여 반환한다.
  return ethJsUtil.bufferToHex(addrBuf)
}

// Public Key 로부터 Address 를 구하는 함수
function pubToAddress(pubKey: Buffer): Buffer {
  assertIsBuffer(pubKey)
  assert(pubKey.length === 64)

  // Public Key 의 끝 20 자리가 Address 이다.
  // keccak 은 keccak-256 해쉬 함수다.
  return keccak(pubKey).slice(-20)
}
```

ecrecover 함수 [출처](https://github.com/ethereumjs/ethereumjs-util/blob/master/src/signature.ts#L69)

```jsx
function ecrecover(
  msgHash: Buffer,
  v: BNLike,
  r: Buffer,
  s: Buffer,
  chainId?: BNLike
): Buffer {
  const signature = Buffer.concat(
    [setLengthLeft(r, 32), setLengthLeft(s, 32)],
    64
  )
  const recovery = calculateSigRecovery(v, chainId)
  if (!isValidSigRecovery(recovery)) {
    throw new Error('Invalid signature v value')
  }
  const senderPubKey = ecdsaRecover(signature, recovery.toNumber(), msgHash)
  return Buffer.from(publicKeyConvert(senderPubKey, false).slice(1))
}
```

### 3-4. 위에서 구한 Address 와 DID Document 의 Address 와 비교한다.

```jsx
class DidDocument {
	...

	// Address 와 DID Document 의 Address 와 비교하는 함수
	hasAuthWithAddress(did: string, svc_id: string, address: string): boolean {
		/**
			authenticationVoList는 DID Document 의 authentication Field 에 해당하며
			아래와 같은 구조를 가진다.

			"authentication":[
         "did:meta:000000000000000000000000000000000000000000000000000000000000c141#MetaManagementKey#4fa7a0b19acd54b072961ed404ff6df0c6307706",
         "did:meta:000000000000000000000000000000000000000000000000000000000000c141#7e928682-4887-11ea-972f-0a0f3ad235f2#06ecdcfefd2c781a99975c75afff16fb1d6ef371",
	       ...
      ]
		*/
    const authenticationVoList = this.getAuthentication();
    const addr = address.startsWith('0x') ? address.substr(2) : address;

    return authenticationVoList.some((auth: string) => {
			// did_auth: did:meta:000000000000000000000000000000000000000000000000000000000000c141
			// svc_id_auth: MetaManagementKey
			// publicKeyHash (address): 4fa7a0b19acd54b072961ed404ff6df0c6307706
      const [did_auth, svc_id_auth, publicKeyHash] = auth.split('#');
      return (
        did_auth === did && svc_id_auth === svc_id && publicKeyHash === addr
      );
    });
  }
}
```

.concat(body)

````

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
````

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
