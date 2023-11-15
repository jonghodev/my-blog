---
title: Javascript Event Loop
date: 2021-08-04 09:08:84
update: 2021-08-04 09:08:84
tags:
 - javascript
description:
---

```javascript
const crypto = require('crypto')
process.env.UV_THREADPOOL_SIZE = '5'

const start = Date.now()
crypto.pbkdf2('a', 'b', 100000, 512, 'sha512', () => {
  console.log('1:', Date.now() - start)
})

crypto.pbkdf2('a', 'b', 100000, 512, 'sha512', () => {
  console.log('2:', Date.now() - start)
})

crypto.pbkdf2('a', 'b', 100000, 512, 'sha512', () => {
  console.log('3:', Date.now() - start)
})

crypto.pbkdf2('a', 'b', 100000, 512, 'sha512', () => {
  console.log('4:', Date.now() - start)
})

crypto.pbkdf2('a', 'b', 100000, 512, 'sha512', () => {
  console.log('5:', Date.now() - start)
})
```

## Ref

https://medium.com/@rpf5573/nodejs-nodejs%EB%8A%94-single-thread%EA%B0%80-%EC%95%84%EB%8B%88%EB%8B%A4-f02b0278c390
