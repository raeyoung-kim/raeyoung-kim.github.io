---
title: shallow copy vs deep copy
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-02-29
categories: [Javascript]
tags: [Javascript]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

String, Boolean, Number 같은 원시형 값을 복사했을 경우 순수한 값이 복사되고 독립적인 값이 저장된다. 하지만 Reference형 객체 복사에는 크게 얕은 복제(shallow copy)와 깊은 복제(deep copy)로 분류된다.

### Reference복사

아래와 같이 새로운 변수`obj2`에 `obj`를 할당했다. 그리고 `obj`에 `obj.c = 3` 값을 추가하고 `obj2`를 확인해보면 `obj`와 같은 값을 가지고 있다.

```javascript
const obj = {
  a:1,
  b:2
}

const obj2 = obj

console.log(obj === obj2) // true

obj.c = 3

console.log(obj2) // {a: 1, b: 2, c: 3}
```
![](https://images.velog.io/images/760kry/post/71f3deeb-e183-4cf4-b5c1-d63bc012fdce/image.png)

이처럼 주소를 참조하고 있다는 것은 서로 다른 변수이지만 같은 객체를 바라보고 있다.

## shallow copy?
---

얕은 복사란 새로운 객체에 원본 객체의 프로퍼티 값을 복사하지만 프로퍼티의 값이 객체 형태라면 프로퍼티 객체의 주소를 복사한다.
### Object.assign ()

```javascript
  const obj = {
    a:'hello',
    b: {
      c:'world'
    }
  }
  const objCopy = Object.assign({}, obj);
  console.log(obj === objCopy) // false

  obj.b.c = 'shallow copy'

  console.log(obj);
  console.log(objCopy);

```
![](https://images.velog.io/images/760kry/post/19a16866-2b34-4746-a6e2-9cc04ee219ff/image.png)

위와 같이 `obj.b.c`의 값에 할당했지만 `objCopy.b.c`의 값도 변한 것을 확인할 수 있다.
## deep copy
---
깊은 복사는 원본 객체를 완전히 복사한다.
주소를 복사하지 않고 완전히 다른 것을 의미한다.

### JSON
```javascript
  const obj = {
    a:'hello',
    b: {
      c:'world'
    }
  }
  const copyObj = JSON.parse(JSON.stringify(obj))
  
  obj.b.c = 'deep copy';
  
  console.log(obj); // {a: "hello", b: {c: "deep copy"}}
  console.log(copyObj); // {a: "hello", b: {c: "world"}}
```

**stringify 메소드는 `function`일 경우 아래와 같이 `undefined`로 처리되므로 주의해서 사용해야한다.**

```javascript
  const obj = {
    a:'hello',
    b: {
      c:function(){}
    }
  }
  const copyObj = JSON.parse(JSON.stringify(obj))
  
  console.log(obj) // {a: "hello", b: {c: f()}}
  console.log(copyObj) // {a: "hello", b: {}}
```
![](https://images.velog.io/images/760kry/post/1ee352b6-fcb1-4871-aadc-b7234caf166e/image.png)
