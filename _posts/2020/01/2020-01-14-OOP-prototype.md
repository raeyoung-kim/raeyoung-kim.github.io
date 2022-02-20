---
title: OOP / 객체 지향 프로그래밍 / prototype 
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-01-14
categories: [Javascript]
tags: [OOP, 객체 지향 프로그래밍]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## Object Oriented Programming / 객체 지향 프로그래밍 ?
***

객체지향은 동작하는 부분을 캡슐화해서 이해할 수 있게 하고, 함수형 프로그래밍은 동작하는 부분을 최소화해서 코드 이해를 돕는다.   -마이클 페더스‘레거시 코드 활용 전략' 저자

객체지향의 경우 객체 안에 상태를 저장한다.
각 객체는 메시지를 받을 수도 있고, 데이터를 처리할 수도 있으며, 또 다른 객체에게 메시지를 전달할 수도 있다.
객체지향 프로그래밍은 보다 유연하고 유지보수하기 쉬우며 확장성 측면에서서도 유리한 프로그래밍을 하도록 의도되었다.

## 생성자 함수와 인스턴스의 생성
***

### 생성자 함수란? 
자바스크립트는 생성자 함수와 new 연산자를 통해 인스턴스를 생성할하면 생성자 함수라고 한다. 생성자 함수는 클래스이자 생성자의 역할을 한다. 
```javascript
function foo () {} // 생성자함수
new foo // 인스턴스
```
```javascript
var obj = {}; // 생성자함수는 Object
var obj = new Object(); // 생성자함수는 Object
```
```javascript 
var arr = [];
var arr = new Array(); // 생성자함수는 Array
```
#### 일반함수와 생성자 함수를 구분해서 작성한다.
```javascript
function Person (name) {
    this.name = name;
}
new Person;
```
- 첫글자를 대문자로 표기해준다.
- this를 사용한다.

### 인스턴스의 생성
생성자 함수와 prototype 기능을 모두 빌려쓸 수 있는 객체다.
```javascript
function Person (name) {
   this.name = name
}
var person1 = new Person(taeback); // 인스턴스
var person2 = new Person(taesoon); // 인스턴스
```
person1과 person2는 아래와 같은 객체로  반환된다.
```javascript
var person1 = {
  name: taeback;
}
var person2 = {
  name: taesoon;
}
```


## 프로토타입 체인
*** 
모든 함수는 프로토타입이라는 다른 객체를 가리키는 내부 링크를 가지고 있다. 즉, 프로토타입을 통해 직접 객체를 연결할 수 있는데 이를 프로토타입 체인이라 한다.

### prototype
함수는 객체이기 때문에 key와 value를 가질수있다.
 모든 함수에는 'prototype' 이라는 속성을 가지고 있고 값으로 객체를 가지게 된다.
```javascript
function foo () {};
foo.prototype; // -> {constructor: foo}
foo.prototype.constructor === foo // 생성자함수를 가리킨다.
```
**생성자 함수와 상관없이 모든 함수에는 'prototype', 'constructor'이라는 속성을 가지고 있다.**

### prototype chain
```javascript
function Person (name) {
   this.name = name
}

Person.prototype.power = 100;
Person.prototype.fun = 200;
Person.prototype.love = function (a) {
  console.log(a + 20);
}

var person1 = new Person('taeback'); // 인스턴스
var person2 = new Person('taesoon'); // 인스턴스
```
person1과 person2는 아래와 같이 반환된다.
```javascript
var person1 = {
  name: 'taeback';
}
var person2 = {
  name: 'taesoon';
}

person1.power;  // 100
person1.fun;  // 200
person1.love(10);  // 30

person2.power;  // 100
person2.fun;  // 200
person2.love(20);  // 40
```
위 코드와 같이 person1과 person2에는 power, fun, love라는 속성이 없다. 하지만 instace 객체는 생성자 함수의 prototype 속성을 사용할 수 있다. prototype을 사용하면 생성자 함수 속성에 따로 정의하지 않아도 되기 때문에 중복을 줄일 수 있다.

### 상속
```javascript
function Grandmother (name) {
  name: name;
}

Grandmother.prototype.love = 100;

var mother = new Grandmother('mom');

var child = Object.create(mother); // mother의 prototype을 갖는 빈객체가 생성된다.

child.love;  // 100;
```
위와 같은 코드가 있을 경우에 child는 mother의 prototype을 갖는 빈객체이다. 
1. child에는 love라는 속성이 없기 때문에 상위 객체인 prototype chain을 타고 올라가 mother에서 love라는 속성을 찾는다.
2. mother에도 love라는 속성이 없다면 mother의 상위 객체인 grandmother에 가서 love라는 속성을 찾게 된다.
3. 속성을 찾게 되면 child는 love라는 속성을 사용할 수 있다.

```javascript
var arr = []; // 생성자함수 Array
Object.prototype.name = 'object';
arr.name; // 'object'
```
1. 'arr'는 'name'이라는 속성이 없다.
2. arr의 생성자 함수 'Array.prototype'에 가서 'name'이라는 속성을 찾는다
3. 'Array.prototype'에 'name'이라는 속성이 없기 때문에 상위 객체 'Object.prototype'에 'name'이라는 속성을 찾는다.
4. 'Object.prototype'에 'name'의 값을 찾아 'arr'이가 사용할 수 있게 된다.

**javascript에서 Object.prototype은 최상위 객체이다.**

## 캡슐화 추상화 팩토리
***
### 캡슐화
객체의 key의 값이 함수일 때 메소드이다.
캡슐화는 관련있는 멤버 변수와 메소드를 클래스와 같은 하나의 틀 안에 담고 외부에 공개될 필요가 없는 정보는 숨기는 것을 말한다.
변수를 스코프 안쪽에 가두어 함수 밖으로 노출시키지 않는 방법이다.
이를 통해서 객체의 내부에서만 사용해야 하는 값이 노출됨으로서 생길 수 있는 오류를 줄일 수 있다.

```javascript
var Person = (function () {
  
  var power = 0;

  return {
    powerUp: function () {
      power++;
    },
    powerDown: function () {
      power--;
    }
    getPower: function () {
      return power;
    }
  };
})();

console.log(power); // power 변수에 접근할 수 없다.
```
즉시 실행되는 익명함수로 만들어주면 scope 때문에 power 변수에 접근할 수 없다.
**외부에서 허용되지 않은 자들이 함부로 접근할 수 없도록 내부 정보를 보호하는 것을 캡슐화라고 한다.** 

### 추상화
객체의 프로퍼티와 메소드를 정의 하고 복잡한 원리나 구동 방식을 사용자로부터 추상화시켜주는 작업이다. 추상화란 객체들의 공통적인 프로퍼티와 메소드를 뽑아내는 작업을 의미한다.

예를 들어, 동물이라는 함수가 있다면 여러 메소드들이 존재하겠지만 음식을 섭취하고 배설하는 메소드는 공통적인 메소드이다.

```javascript
var Person = (function () {
  // Now encapsulated
  var power = 0;

  return {
    powerUp: function () {
      power = 100;
    },
    powerDown: function () {
      power = 0;
    },
    upDown: function () {
      power = 100;
      setTimeout(function () {
        power = 0;
      }, 5000);
    }
  };
})();
```


### Factory
생성자 함수가 아니면서 객체를 만들어서 돌려주는 함수를 말한다.

```javascript
var Person = {
  powerUp: function () {
    this.power = 100;
  },
  powerDown: function () {
    this.power = 0;
  },
};

function createPerson () { // <- Factory Function
  return Object.create(Person);
}

var person1 = createPerson();
var person2 = createPerson();
var person3 = createPerson();
```
**`return Object.create(Person);`는 값을 갖는 `Person`을 prototype으로 갖는 객체를 만들어 주는 것이다.**
