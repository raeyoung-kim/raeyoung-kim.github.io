---
title: let, var, const 차이
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2019-11-27
categories: [Javascript]
tags: ['let, var, const']
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## let 과 var 의 차이점
---
* **let**: **block scope** 안에서 유효한 스코프를 가지고 있으며, **지역변수**를 선언한다.
* **var**: **function scope** 안에서 유효한 스코프를 가지고 있으며, **전역변수**를 선언할 수 있다.

* `let`은 전역 객체를 통해 접근할 수 없지만 `var`는 접근이 가능하다.

```javascript
let a = 1;
var b = 2;

console.log(window.a); // undefined
console.log(window.b); // 2
```

*  `let`은 재선언 할 수 없지만, `var`는 재선언 할 수 있다.

* `const`는 `let`과 같은 유효범위를 가지고 선언 후, 값을 변경할 수 없다.

## let
---
**Block Scope란 `{}`중괄호를 이용하여 구분된 영역을 말한다.**
```javascript
{ 
   let a=1; 
}
console.log(a); // error
```
`Block Scope` 에서 유효한 스코프를 가지고있으며, **지역 변수**를 선언한다.

### 1. Block Scope

```javascript
let c = 1;

if (true) {
  let d = 2;
}

console.log(c + d); // ?
```
`Block Scope` 에서 유효한 스코프를 가지고 있기 때문에 `d`를 참조할 수 없다.

#### example
```javascript
for (let i = 0; i < 10; i++) {
  console.log(i);
}

console.log(i); //  error
```
#### example
```javascript
for (let i = 1; i < 11; i++) {
  setTimeout(function () {
    console.log(i);
  }, i * 1000);
}
```

### 2. let 은 재선언을 할 수 없지만 재할당은 가능하다.
#### 재선언 불가능
```javascript
let a = 1;

if (true) {
  let a = 2;
  console.log(a);
}

console.log(a); //  error

```

#### 재할당 가능
```javascript
let a = 1;

if (true) {
  a = 2;
  console.log(a);
}

console.log(a); //  2

```
### 3. let 은 hoisting은 되지만 error를 발생한다.
#### `let`은 초기 값을 할당하지 않으면 `undefined`가 초기 값으로 할당된다.
```javascript
let a;
console.log(a)  // undefined
```

#### hoisting은 되지만 error를 발생한다.
```javascript
function doSomething() {
  console.log(bar);
  console.log(foo);
  var bar = 1;
  let foo = 2;
}

doSomething();

```

### 4. let 을 이용하여 선언한 변수는 글로벌 객체에 입력되지 않는다.

```javascript
var x = 'global x';
let y = 'global y';

console.log(window.x);   //  'global x'
console.log(window.y);   //   undefined
```

## var
---
### 1. function scope
**function scope** 안에서 유효한 스코프를 가지고 있으며, **전역변수**를 선언할 수 있다.
### `function scope`
```javascript
function foo () {
   for (var i = 0; i < 10; i++) {
      console.log(i);
   }   
   console.log(i);    // 10
}

foo();
```

### 2. var 는 재선언이 가능하다.
```javascript
var a = 1;

if (true) {
  var a = 2;
  console.log(a);  
}

console.log(a); // 2
```

### 3. var 를 이용하여 선언한 변수는 글로벌 객체에 입력된다.

```javascript
var x = 'global x';
let y = 'global y';

console.log(window.x);   //  'global x'
console.log(window.y);   //   undefined

```

### 4. var 의 hoisting
#### `var`는 초기 값을 할당하지 않으면 `undefined`가 초기 값으로 할당된다.
```javascript
var a;
console.log(a);   // undefined
```
### hoisting
```javascript
function doSomething() {
  console.log(bar);  // undefined
  var bar = 1;
}

doSomething();  
```
hoisting이 되면서 콘솔에 `undefined`가 출력된다.

## const
---
`const`를 이용하여 선언한 변수는 `let`을 이용하여 선언한 변수와 거의 동일하다. 다만 **재할당을 할 수 없다.** 

### 1. 반드시 초기 값을 할당해야 한다.
#### ex 14)
```javascript
const a;  // error
```

### 2. 재선언, 재할당 모두 할 수 없다.
#### 재선언 할 수 없다.
```javascript
const a = 5;
const a = 2; // error
```

#### 선언 이후 다른 값을 할당할 수 없다.
```javascript
const foo = 2;
foo = 3; // error
```
### 3. 객체일 때 속성 값을 추가 할 수 있다.
```javascript
const obj = {};
obj.say = 'hello'; 
```

![image.png](https://images.velog.io/post-images/760kry/a97fa0a0-38f8-11ea-b7bc-e52c887b1706/image.png)
