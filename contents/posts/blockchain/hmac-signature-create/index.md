---
title: hmac signature create
date: 2021-10-28 09:11:87
update: 2021-10-28 09:11:87
tags:
 - blockchain
description:
---

hmac signature 를 생성하는 코드

```js
const { createHmac } = 'crypto'

const config = {
  secretKey: '4d5818810a83d801d9c3941cc4eb991a560ffb6f28f7a41c4d1253beaa74017d',
  hmacId: '108ef517-3082-4c3e-85c0-a451a35c9e7d',
}

function generateHmacHeaders(method, url, data) {
  const timestamp = getTimestamp().toString()

  const sig = hashMessage(
    config.secretKey,
    method,
    url,
    config.hmacId,
    timestamp,
    data
  )

  return {
    'content-type': 'application/json',
    'hmac-id': config.hmacId,
    timestamp,
    sig,
  }
}

function getTimestamp() {
  const now = Math.floor(Date.now() / 1000)
  return now
}

function hashMessage(secret, method, url, hmacId, timestamp, body) {
  const message = createMessage(
    method,
    url,
    hmacId,
    timestamp,
    JSON.stringify(body)
  )
  const hash = createHmac('sha256', secret)
    .update(message)
    .digest()
    .toString('hex')

  return hash
}

function createMessage(method, url, hmacId, timestamp, body = {}) {
  const hmacMessage = method
    .concat(url)
    .concat(hmacId)
    .concat(timestamp)
    .concat(body)

  return hmacMessage
}

const url = '/textApi'
const method = 'GET'
const headers = generateHmacHeaders(method, url)

console.log(headers)
```
