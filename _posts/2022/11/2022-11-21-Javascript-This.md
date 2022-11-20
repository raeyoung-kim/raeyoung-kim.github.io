---
title: Javascript This
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-11-21
categories: [Javascript]
tags: [javascript this]
math: true
mermaid: true
---

자바스크립트에서 this는 **실행 컨텍스트가 생성될 때 바인딩**됩니다.  
즉, **함수가 호출될 때 this가 결정된다는 것**입니다.

아래 소스를 보면 어떻게 호출했느냐에 따라 this가 달라집니다.

```js
var student = {
  name: "jennie",
  myName: function () {
    console.log(this.name);
  },
};

var myName = student.myName;
myName(); // undefined
student.myName(); // jennie
```

결과가 다른 이유는 `myName()`은 **함수로 호출**을 했고 `student.myName()`는 **메소드로 호출**했기 때문입니다.

## 함수 호출 시

### 모든 객체는 전역 객체의 프로퍼티입니다

-   전역 컨텍스트를 실행하는 주체는 전역 객체입니다.
-   모든 전역변수와 함수는 window 객체의 프로퍼티입니다.
-   a와 `window.a`는 같고, foo();와 `window.foo();`와 같습니다.
-   객체를 명시하지 않으면 암시적으로 window의 프로퍼티로 간주됩니다.

```js
var a = "Hello?";

function foo() {
  console.log("world");
}

console.log(a); // Hello?
console.log(window.a); // Hello?

foo(); // world
window.foo(); // world
```

### 일반 함수로 호출 시 this는 전역 객체를 가리킵니다

아래 소스와 같이 **일반 함수로 호출 시 this는 전역 객체를 가리킵니다.**(브라우저에서는 window, 노드에서는 global)

```js
function foo() {
  function a() {
    console.log(this); // window
  }
  a(); // 일반 함수 호출
}

foo();
```

```js
var obj = {
  foo: function () {
    function f() {
      console.log(this); //window
    }
    f();  // 일반 함수 호출
  },
};

obj.foo();
```

### use strict 모드일 때

> **오류를 보안해 주기 위해 엄격한 코드 실행을 해주는 환경**

```js
"use strict";

var a = "hello";

function foo() {
  console.log(this.a); // this === undefined
}

foo();
```

-   위 소스와 같이 `use strict` 모드가 적용되면 `this === undefined`로 설정됩니다.
-   만약 window를 가리키고 싶다면 아래와 같이 window를 명시합니다.

```js
"use strict";

var a = "hello";

function foo() {
  console.log(window.a); // hello
}

foo();
```

## Method 호출 시

-   메소드는 객체(인스턴스)와 관련된 동작을 의미합니다.
-   객체의 소속인 메소드의 this는 그 객체를 가리킵니다.

```js
var a = "hello";

var obj = {
  a: "world",
  foo: function foo() {
    console.log(this.a); // world
  },
};

obj.foo(); // this는 obj를 가리킵니다.
```

```js
var a = {
  name: "a",
  b: {
    name: "b",
    foo: function () {
      console.log(this.name); // b
    },
  },
};

a.b.foo(); // this는 a.b를 가리킵니다.
```

### 메소드 내부 함수에서의 this 우회법

```js
var name = "lisa";

var obj = {
  name: "jennie",
  getName: function () {
    console.log(this.name); // jennie (this === obj)

    function getThisName() {
      console.log(this.name); // lisa (this === window)
    }
    getThisName();
  },
};

obj.getName();
```

위 소스에서 `getThisName`의 this를 obj를 가리키도록(`this === obj`) 하려면 어떻게 해야 할까?

#### 변수에 this를 담는 방법

-   변수에 `this`를 담아 obj를 가리킬 수 있습니다.

```js
var name = "lisa";

var obj = {
  name: "jennie",
  getName: function () {
    var newThis = this;

    function getThisName() {
      console.log(newThis.name); // jennie
    }
    getThisName();
  },
};

obj.getName();
```

#### 화살표 함수 사용하기

-   **화살표 함수에는 this가 없습니다.**
-   스코프 체이닝 상의 this에 접근하여 `obj`를 가리킵니다.

```js
var name = "lisa";

var obj = {
  name: "jennie",
  getName: function () {
    console.log(this.name); // jennie

    var getThisName = () => {
      console.log(this.name); // jennie
    };
    getThisName();
  },
};

obj.getName();
```

## 콜백 호출 시 - 명시적 this 바인딩

### call

[`func.call(thisArg[, arg1[, arg2[, ...]]])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

```js
var callback = function () {
  console.log(this); // window
};

var obj = {
  foo: function (cb) {
    cb();
  },
};

obj.foo(callback);
```

`this`를 `obj`를 가리키려고 한다면 아래와 같이 `call`을 사용하여 this를 명시적으로 지정할 수 있습니다.

```js
var callback = function () {
  console.log(this); // obj
};

var obj = {
  foo: function (cb) {
    cb.call(this);
  },
};

obj.foo(callback);
```

call의 경우 arg 인자의 수가 정해져 있지 않습니다.

```js
var string = "hello";

function foo(a, b, c, d, e) {
  console.log(this.str);
  console.log(arguments);
}

var obj = {
  str: "world",
};

foo.call(obj, 1, 2, 3, 4, 5);
```

### apply

[`func.apply(thisArg, [argsArray])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

```js
var string = "hello";

function foo(a, b, c, d, e) {
  console.log(this.str);
  console.log(arguments);
}

var obj = {
  str: "world",
};

foo.apply(obj, [1, 2, 3, 4, 5]);
```

-   인자를 2개만 받습니다.
-   첫 번째는 this값을 넣어주고, 두 번째는 인자는 배열로 들어갑니다.

### bind

this값을 설정해 놓은 **함수를 실행하지 않고 함수 자체를 반환**해줍니다.

[`func.bind(thisArg[, arg1[, arg2[, ...]]])`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

```js
var callback = function () {
  console.log(this); // obj
};

var obj = {
  foo: function (cb) {
    cb.call(this);
  },
};

setTimeout(callback, 1000);
```

위와 같이 `setTimeout`에 콜백을 담아 넘기면 this는 전역 객체를 가리킵니다.  
`this`를 `obj`를 가리키려고 한다면 아래와 같이 `bind`를 사용하여 this를 명시적으로 지정할 수 있습니다.

```js
var callback = function () {
  console.log(this); // obj
};

var obj = {
  foo: function (cb) {
    cb.call(this);
  },
};

setTimeout(callback.bind(obj), 1000);
```

## 생성자 함수 호출 시

new 연산자를 썼을 때 생성자 함수의 내용을 바탕으로 인스턴트 객체를 만드는 명령으로 **새로 만들 인스턴스 객체 자체가 this**가 됩니다.

-   생성자(varructor)는 객체를 만드는 역할을 하는 함수(class)입니다..
-   생성자 함수는 첫 글자를 대문자로 표기합니다.
-   함수 앞에 new를 붙이면 리턴 값은 객체(instance)가 된다.

```js
function Foo() {
  console.log(this); // {}
}

new Foo(); // 객체 instance
```

`new` 키워드를 사용하여 함수를 호출하면 함수 안의 this의 값은 `빈 객체`가 할당되어서 함수가 실행됩니다.

```js
function Foo() {
  this.name = "sara";
}

var a = new Foo();
```

위 소스는 아래와 같이 this를 return 해주는 것과 같습니다.

```js
function Foo() {
  this.name = "sara";
  /*
    {
      name: 'sara';
    }
    return this;
  */
}

var a = new Foo();
```

```js
let name = "sara";
let age = 30;

function Person(name, age) {
  this.name = name;
  this.age = age;
}

var lisa = Person("lisa", 20);

console.log(window.name, window.age); // lisa, 20
```

위 소스처럼 new 연산자를 사용하지 않고 호출하게 되면 this는 전역 객체를 가리키게 되고 전역 객체의 name, age의 값은 재할당 하게 됩니다.

```js
let name = "sara";
let age = 30;

function Person(name, age) {
  this.name = name;
  this.age = age;
}

var lisa = new Person("lisa", 20);

console.log(window.name, window.age); // sara, 30
console.log(lisa); 
```

new 연산자를 사용할 경우 객체가 새로 만들어지면서 그 객체 안에 name, age 프로퍼티가 생성되면서 lisa라는 변수에 담기게 됩니다.
