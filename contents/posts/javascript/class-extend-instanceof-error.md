---
title: class extend instanceof error
date: 2021-06-18 22:00:00
update: 2021-06-18 22:00:00
tags:
 - javascript
description:
---

## 에러

에러는 다음과 같다.

```javascript
class ErrorHandler extends Error {
  constructor() {
    super()
  }
}

const errorHandler = new ErrorHandler()
console.log(errorHandler instanceof ErrorHandler)
```

결과값이 당연히 true 가 나와야 할 것 같지만 `false` 가 나오는 것이다.

위 코드를 크롬 개발자 도구에서 실행하면 `true` 로 정상적으로 나오지만 nodejs 환경에서 실행하면 에러가 날 때가 있다.

```javascript
class ErrorHandler {
  constructor() {}
}

const errorHandler = new ErrorHandler()
console.log(errorHandler instanceof ErrorHandler)
```

그리고 위 코드는 또 `true` 가 나오며 정상적으로 실행된다.

## 원인

원인은 tsconfig.json 에 있다.

```json
{
  "compilerOptions": {
    "target": "es5",
    ...
  }
}
```

위와 같이 설정이 되어있으면 에러가 나고 아래와 같이 수정해주면 에러가 안 난다.

```json
{
  "compilerOptions": {
    "target": "es2016",
    ...
  }
}
```

이렇게 삽질을 하면서 해결하긴 했지만 왜 이게 해결이 되는지는 잘 모르겠다.
