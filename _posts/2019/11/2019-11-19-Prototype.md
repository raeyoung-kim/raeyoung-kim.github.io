---
title: Prototype
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2019-11-19
categories: [Javascript]
tags: [Prototype]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

# 객체지향
## 생성자함수와 new 키워드
* 생성자(constructor)는 객체를 만드는 역할을 하는 함수(class)이다.
* 함수 앞에 new를 붙이면 리턴 값은 객체(instance)가 된다.
* 생성자 함수이름 첫글자를 대문자로 표기해 준다.

### ex)

```javascript
function Array () { // 생략 }    // 생성자 함수

new Array()   //   instance 객체가 반환된다.


function Object () { // 생략 }   // 생성자 함수

new Object()   //   instance 객체가 반환된다.


function String () { // 생략 }    // 생성자 함수

new String()   //   instance 객체가 반환된다.

```
### ex) 

```javascript
var arr = [];     ===      var arr = new Array();
```
**모든 자바스크립트 객체는 생성자 함수를 이용해서 만들어 지는것과 같다.**

# Prototype
### ex)
```javascript
function foo() {} // 생성자함수
foo.prototype // `prototype`이라는 속성을 가지고 있는 객체를 가지게 된다.
```
* **함수는 객체이기 때문에 key와 value를 가질수있다.**
* **모든 함수에는 'prototype' 이라는 속성을 가지고 있고 값으로 객체를 가지게 된다.**
* 생성자 함수는 첫 글자를 대문자로 표기해 준다.
### ex)
```javascript
function Foo() {}   // 생성자함수
Foo.prototype   // 모든 함수에는 'prototype' 이라는 속성을 가지고 있다.
Foo.prototype.constacrtor   // 생성자 함수를 가리킨다.
```
`foo.prototype` 객체는 `constacrtor` 속성을 가지고있고 `foo.prototype.constacrtor`는 생성자 함수를 가리키게 된다.

## instance

### ex)
```javascript
function Object () { // 생략 }   // 생성자 함수

new Object() //  instance 객체가 반환된다.
```
**`new Object()` 는 생성자 함수와 prototype 기능을 모두 빌려쓸 수 있는 객체가 반환된다.**


# EX5 or EX6
## EX5
```javascript
function Car(brand, name, color) {
  // 생략
}
Car.prototype.refuel = function() {
  // 생략
}
Car.prototype.drive = function() {
  // 생략
}
```
## EX6
```javascript
class Car {
   constructor(brand, name, color) {
      // 생략
   }
  refuel() {
     // 생략
  }
  drive() {
     // 생략
  }
}
```
