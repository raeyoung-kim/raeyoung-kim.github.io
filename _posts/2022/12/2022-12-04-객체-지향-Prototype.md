---
title: 객체 지향 Prototype
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-12-04
categories: [Javascript]
tags: [javascript prototype, 자바스크립트 프로토타입, 프로토타입]
math: true
mermaid: true
---

## 객체지향 프로그래밍 OOP(Object Oriented Programming) 이란?

-   서로 관련 있는 데이터와 함수를 객체(하나의 역할을 수행하는 메소드와 변수의 묶음)로 정의해서 서로 상호작용할 수 있도록 프로그래밍해나가는 것을 의미합니다.
-   각 객체는 메시지를 받을 수 있고, 데이터를 처리할 수도 있으며, 또 다른 객체에게 메시지를 전달할 수도 있습니다.
-   자바스크립트는 **prototype**을 기반으로 **객체지향** 프로그래밍을 해나갈 수 있습니다.
-   객체지향을 사용하면 중복되는 관련 객체를 재사용할 수 있어 중복을 어느 정도 줄일 수 있고, 관련 있는 객체들의 역할 분담을 좀 더 확실하게 할 수 있어 가독성이 높아지고 생산성과 유지보수 및 확장성이 높아집니다.

## 생성자 함수와 인스턴스 생성

-   자바스크립트는 new 연산자를 통해 인스턴스를 생성하면 생성자 함수라고 합니다.  
    생성자 함수는 클래스이자 생성자의 역할을 합니다.
-   생성자 함수와 porototype 기능을 모두 빌려 쓸 수 있는 객체입니다.
-   일반 함수와 생성자 함수를 구분해서 작성하기 위해 생성자 함수는 첫 글자를 대문자로 표기해줍니다.
-   this를 사용합니다.

```js
function Person(name, age) {  // 생성자 함수
  this.name = name;
  this.age = age;
}

const jennie = new Person('jennie', 20); // 인스턴스
const lisa = new Person('lisa', 20);  // 인스턴스
```

## Prototype & `[[Prototype]]`

-   일반 함수와 생성자 함수와 상관없이 모든 함수(**모든 [함수는 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function)**)에는 **prototype**, **constructor**이라는 속성을 가지고 있습니다.(**javascript에서 Object.prototype은 최상위 객체입니다.**)
-   `생성자 함수`가 있을 때 new 연산자로 `인스턴스를 생성`하면 그 인스턴스에는 constuctor의 porototype의 프로퍼티 내용이 `[[Prototype]]`라고 하는 프로퍼티로 참조를 전달하게 됩니다.
-   `constructor.prototype`과 instance`[[Prototype]]`은 **같은 객체를 바라봅니다.**
-   `[[Prototype]]`은 접근이 가능한 것이 아니라 정보를 보여주기만 합니다.

아래 화면과 같이 생성자 함수 `Array`를 개발자 도구에서 출력하면 `porototype`을 확인할 수 있습니다.

![](https://velog.velcdn.com/images/760kry/post/3cd44c26-f947-4914-aaff-849ca33c4b39/image.png)


이 `porototype`은 인스턴스 배열 `arr의` `[[Prototype]]`과 연결됩니다.

![](https://velog.velcdn.com/images/760kry/post/99cb5cbf-c81c-4d74-93d6-b3b1412baae9/image.png)


-   `Array.prototype.constructor` 와 `arr.constructor` 는 **같은 배열의 생성자 함수를 가리킵니다.**
-   생성자 함수의 **prototype 프로퍼티**에 있는 것을 `[[Prototype]]`을 통해 메소드를 사용할 수 있습니다.

> 인스턴스가 생성장 함수의 prototype에 접근하려면?  
> [`Object.getPrototypeOf(instance)`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)

### 메소드 상속 

아래 소스와 같이 프로토타입으로 메소드를 만들면 인스턴스들을 계속 만들어도 한번 만들어 놓은 프로토타입 메소드를 참조하면서 메모리 사용 효율을 끌어올릴 수 있는 장점이 있습니다.

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.setAge = function () {
  this.age += 1;
};

Person.prototype.getAge = function () {
  return this.age;
};

const jennie = new Person("jennie", 20);
const lisa = new Person("lisa", 20);

jennie.setAge(); // 21
lisa.setAge(); // 21
```

### Prototype Chaning

-   **javascript에서 Object.prototype은 최상위 객체입니다.**
-   **배열이든 함수이든 Object.prototype과 프로토타입 체인으로 연결되어있습니다.**
-   Array도 Object의 상속을 받은 객체입니다. 때문에 Array도 Object의 prototype을 자신의 것처럼 사용할 수 있습니다.

![](https://velog.velcdn.com/images/760kry/post/7ab4cfba-4dc4-486b-973e-573b97510162/image.png)


예를 들어,  `[1, 2, 3]. method()` 어떠한 메소드를 호출한다고 했을 때 먼저 인스턴스 자기 자신에서 먼저 찾고, 없다면 프로토타입을 체이닝을 타고 올라가 `Array.prototype`에서 메소드를 찾습니다. 해당 메소드가 있다면 메소드를 호출하고 끝내지만 만약 없다면, 한 단계 더 타고 올라가 `Object.prototype`에서 메소드를 찾고 해당 메소드가 있다면 메소드를 호출하고 없다면 Object는 최상위 객체이기 때문에 더 이상 올라갈 수 있는 체인이 없어 에러를 던지게 됩니다. 이렇게 타고 타고 올라가는 것을 프로토타입 체이닝이라고 합니다.
