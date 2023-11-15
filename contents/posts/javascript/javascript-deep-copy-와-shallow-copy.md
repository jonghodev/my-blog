---
title: Javascript Shallow, Deep Copy 와 Merge
date: 2020-11-14 08:00:00
update: 2020-11-14 08:00:00
tags:
 - javascript
description:
---

## Shallow Copy, Merge

Javascript 에서 Shallow Copy, Merge 를 하기 위해선 다음 두 가지 방식이 있다.

- `Object.assgin()` 이용
- `Spread` 연산자 사용

```javascript
const obj1 = { a: 1 }
const obj2 = { a: 2 }

const shallowCopyResult = Object.assign(obj1, obj2)
```

```javascript
const obj1 = { a: 1 }
const obj2 = { a: 2 }

const shallowCopyResult = { ...obj1, ...obj2 }
```

`Object.assign()` 은 함수 인자로 넘어온 객체의 자신의 값을 변경시킨다.

그래서 사이드 이펙트를 생길 수 있어서 함수형 프로그래밍에서는 부합하지 않는 방식이다.

그렇다면 원본 객체를 수정하지 않는 Spread 연산자를 사용하면 된다.

## Deep Copy

- JSON parse, stringify 사용
- Lodash 의 deeep clone
- 직접 구현

### JSON parse, stringify 사용

```javascript
const deepcopy = obj => JSON.parse(JSON.stringify(obj))

const obj = {
  a: [
    {
      b: 2,
      c: 3,
    },
  ],
}
deepcopy(obj)
```

위 방법은 성능적으로 느리고 function 을 undefined 로 처리하기 때문에 가능한 지양하는 것이 좋다.

### Loadsh 의 deep clone

```javascript
const clonedeep = require('lodash.clonedeep')

const obj = {
  a: [
    {
      b: 2,
      c: 3,
    },
  ],
}
clonedeep(obj)
```

많은 사람들에 의해 검증된 lodash library 를 사용하는 방법이 있다.

### 직접구현

```javascript
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') {
    return obj
  }

  const result = Array.isArray(obj) ? [] : {}

  for (let key of Object.keys(obj)) {
    result[key] = deepClone(obj[key])
  }

  return result
}
```

### Deep Merge

```javascript
const deepmerge = require('deepmerge')
const _ = require('lodash')

const obj1 = {
  a: [
    {
      b: 2,
      c: 3,
    },
  ],
}

const obj2 = {
  a: [
    {
      b: 12,
      d: 14,
    },
  ],
}

// 1번째 방식
deepmerge(obj1, obj2) // { a: [ { b: 2, c: 3 }, { b: 12, d: 14 } ] }

// 2번째 방식
_.merge(obj1, obj2) // { a: [ { b: 12, c: 3, d: 14 } ] }
```

obj1 과 obj2 를 비교해보면 a 라는 key 안에 있는 배열에서 객체가 b 라는 key 겹치는 상황이다.

deepmerge 의 merge 결과는 배열에 push 가 되는 형태로 되었다.

loadash 의 merge 결과는 b 라는 key 가 겹치기 때문에 객체가 합쳐젔고

그리고 lodash 라이브러리는 `Object.assign()` 함수을 이용하기 때문에 원본을 변경시킨다.

어떤 방법을 선택을 해야할까?

## References

https://blog.ull.im/engineering/2019/04/01/javascript-object-deep-copy.html

https://junwoo45.github.io/2019-09-23-deep_clone/
