---
title: JWT 에 대하여
date: 2021-06-08 22:00:00
update: 2021-06-08 22:00:00
tags:
 - development
description:
---

## JWT 란

> JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object.

- JWT 는 JSON Web Token 의 약자로 전자 서명된 URL-safe 토큰이다. RFC 7519 의 표준이다.
- 자가수용적(self-contained)이다. 필요한 모든 정보를 가지고 있다는 뜻이다.
  - 토큰에 대한 기본 정보 (알고리즘), 전달할 정보 (Payload), Signature
- 다양한 프로그래밍 언어 지원
- 전달 용이성
  - Base64Url Encoding 되어서 URL 파라미터로도 전달이 가능.

## JWT 구조

`.` 을 구분자로 3가지 문자열로 구성된다. `header.payload.signature`

header 와 payload 는 JSON Object 로 되어있고 이 둘을 서명한 것이 signature 가 된다.

각각에 대하여 base64 url encoding 을하고 . 으로 구분하면 JWT 가 된다.

## JWT 구조 - Header

Header 는 두 가지 정보를 지닌다.

- typ: 토큰의 타입
- alg: 해싱 알고리즘.
  - HMAC SHA256 혹은 RSA 가 사용된다.
  - 이 알고리즘은 토큰을 검증하는 Signature 에서 사용된다.

아래 정보는 Header 의 예시이다.

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

Base64url 인코딩 예시 (Node.js)

```javascript
const header = {
  typ: 'JWT',
  alg: 'HS256',
}

// encode to base64
const encodedHeader = Buffer.from(JSON.stringify(header), 'binary')
  .toString('base64')
  .replace(/=/g, '')
  .replace(/\+/g, '-')
  .replace(/\//g, '_')
console.log(encodedHeader)
```

결과값: `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9`

> 참고: 위 과정에서 JSON 객체의 공백, 엔터 등이 사라져서 다음과 같은 문자열 String 을 인코딩하게 된다. `{"alg":"HS256","typ":"JWT"}`

위 코드는 JWS 라이브러리 코드를 까서 확인했고 실제 코드는 아래와 같이 되어있다. [출처](https://github.com/auth0/node-jws/blob/8857ee77623104e5cf9955932165ddf9cea1b72c/lib/sign-stream.js#L18)

```javascript
function base64url(string, encoding) {
  return Buffer.from(string, encoding)
    .toString('base64')
    .replace(/=/g, '')
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
}

function jwsSecuredInput(header, payload, encoding) {
  encoding = encoding || 'utf8'
  var encodedHeader = base64url(toString(header), 'binary')
  var encodedPayload = base64url(toString(payload), encoding)
  return util.format('%s.%s', encodedHeader, encodedPayload)
}
```

여담이지만 [링크](https://github.com/nodejs/node/issues/26512)에서 논의 중인 Github 이슈를 보면 아직 Buffer.toString() 에서 'base64' 만 지원하고 'base64url' 을 지원하지 않아서

JWS 라이브러리에선 직접 base64url function 을 만들어 사용하는 것을 볼 수 있다.

## JWT 구조 - Payload

`Payload` 에는 토큰에 담을 정보를 넣는다. 정보의 한 조각을 **클레임(Claim)**이라고 부르고, name : value 쌍으로 되어있다. 토큰에는 여러 개의 클레임을 넣을 수 있다.

클레임의 종류는 다음과 같이 크게 세분류로 나뉜다.

- 등록된(registerd) 클레임
- 공개(public) 클레임
- 비공개(private) 클레임

### 1. 등록된(registerd) 클레임

등록된 클레임은 서비스에 필요한 데이터가 아니라, 미리 이름을 약속해둔 클레임들이다. 이 클레임들은 모두 optional 이므로 구현하지 않아도 된다.

종류는 다음과 같다. [출처](https://velopert.com/2389)

- `iss`: 토큰 발급자 (issure)
- `sub`: 토큰 제목 (subject)
- `aud`: 토큰 대상자 (audience)
- `exp`: 토큰의 만료시간 (expiration), 시간은 NumericDate 형식. 예: 1480849147370
- `nbf`: Not Before 를 의미. 이 날짜가 지나기 전까지는 토큰이 처리되지 않는다. 시간은 NumericDate 형식.
- `iat`: 토큰이 발급된 시간 (issued at), 이 값을 사용하여 토큰의 `age` 를 판단할 수 있다.
- `jti`: JWT 의 고유 식별자로서, 주로 중복적인 처리를 방지하기 위해 사용한다. 일회용 토큰에 사용하면 유용하다.

### 2. 공개(public) 클레임

충돌이 방지된 (collision-resistant) 이름을 가지고 있어야 한다. 충돌을 방지하기 위해서 uri 형식으로 짓는다.

```json
{
  "https://jongho.dev/jwt_claims/is_admin": true
}
```

### 3. 비공개(private) 클레임

서비스에 필요한 정보를 담는 곳.

```json
{
  "username": "jongho",
  "name": "Jeon Jongho"
}
```

### 4. 예제 Payload

```json
{
  "iss": "jongho.dev",
  "exp": "1623130203000",
  "https://jongho.dev/jwt_claims/is_admin": true,
  "username": "velopert",
  "name": "Jeon Jongho"
}
```

위 데이터도 마찬가지로 base 64 url encoding 한다.

```javascript
const payload = {
  iss: 'jongho.dev',
  exp: '1623130203000',
  'https://jongho.dev/jwt_claims/is_admin': true,
  username: 'jongho',
  name: 'Jeon Jongho',
}

// encode to base64
const encodedPayload = Buffer.from(JSON.stringify(payload), 'binary')
  .toString('base64')
  .replace(/=/g, '')
  .replace(/\+/g, '-')
  .replace(/\//g, '_')
console.log(encodedPayload)
```

결과값: `eyJpc3MiOiJqb25naG8uZGV2IiwiZXhwIjoiMTYyMzEzMDIwMzAwMCIsImh0dHBzOi8vam9uZ2hvLmRldi9qd3RfY2xhaW1zL2lzX2FkbWluIjp0cnVlLCJ1c2VybmFtZSI6ImpvbmdobyIsIm5hbWUiOiJKZW9uIEpvbmdobyJ9`

## JWT 구조 - Signature

서명은 Header와 Payload 의 Encoding 값을 합친 후 주어진 비밀키로 해싱하여 생성한다.

서명을 만드는 수도코드는 다음과 같다.

```pseudocode
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload), your-256-bit-secret
)
```

위 결과값을 `base64 url encoding` 을 하면 된다.

서명 예제 (Node.js). 실제 [라이브러리 코드](https://github.com/auth0/node-jwa/blob/master/index.js#L128) 참고.

```javascript
const crypto = require('crypto')
const signature = crypto
  .createHmac('sha256', 'secret')
  .update(encodedHeader + '.' + encodedPayload)
  .digest('base64')
  .replace(/=/g, '')
  .replace(/\+/g, '-')
  .replace(/\//g, '_')

console.log(signature)
```

결과값: `iHoqk_ELnlikekEwU19pRNVRPfFY1ayT76EP17Q4L-w`

## JWT 구조 - 마무리

위에서 만든 Header, Payload, Signature Encoding 값을 `.` 으로 이어 붙여주면 다음 결과가 나오고 이것이 JWT 가 된다.

`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJqb25naG8uZGV2IiwiZXhwIjoiMTYyMzEzMDIwMzAwMCIsImh0dHBzOi8vam9uZ2hvLmRldi9qd3RfY2xhaW1zL2lzX2FkbWluIjp0cnVlLCJ1c2VybmFtZSI6ImpvbmdobyIsIm5hbWUiOiJKZW9uIEpvbmdobyJ9.iHoqk_ELnlikekEwU19pRNVRPfFY1ayT76EP17Q4L-w`

이것을 검증하기 위해선 secret key 로 위에서 만든 비밀키인 'secret' 을 전해주면 된다.

![](./images/jwt_sample.png)

## JWT, JWS, JWE

JWT 는 다음 토큰들의 일반적인 이름이다.

- JSON Web Signature (JWS): The payload is encoded and signed so the integrity of the claims can be verified.

- JSON Web Encryption (JWE): They payload is encrypted so the claims are hidden from other parties.

우리가 일반적으로 웹에서 사용하는 JWT 는 사실 JWS 인 것이고, 클레임이 포함된 Payload 를 암호화해서 아무나 읽게 하고 싶지 않다면 JWE 를 사용하면 된다.

## 정리

예전에 대충 이해하고 사용했었는데, 이번 기회에 제대로 정리해보면서 학습을 해보니 좀 더 기술에 대한 이해도가 생겼다.

내가 옛날엔 JWT 가 Secret Key 가 없어도 복호화가 가능하고, 비밀키로 검증만 된다는 사실을 그저 맹목적으로 외우고만 있었는데 지금 보니 당연한 것이다.

그저 Payload 를 base 64 encoding 한 것이니 당연히 읽을 수 있고 그것을 비밀키로 해싱을 했으므로 검증할 때는 똑같이 비밀키로 해싱해서 해싱한 값만 비교하면 검증이 되는 것이다.

## References

https://stackoverflow.com/questions/58341833/why-base64-is-used-in-jwts

https://jwt.io

https://velopert.com/2389

https://kaonsoft.tistory.com/6

```

```
