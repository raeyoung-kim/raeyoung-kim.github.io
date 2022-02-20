---
title: design-patterns
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-01-20
categories: [Javascript]
tags: [design-patterns, 디자인 패턴 ]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## 디자인 패턴이란?
---
프로그램을 개발하는 과정에서 발생하는 다양한 문제점들을 해결하기 위해 많은 개발자들이 빈번하게 발생하는 문제들을 상황에 따라 적용해서 쓸 수 있는 패턴 형태로 만든 증명된 기술들이다. 어떠한 상황에서 정확한 해결책을 제시해 주는 것은 아니지만 일종의 방향성(솔루션)을 제시해준다.


### 1. Modules 모듈 패턴
---
모듈(module)은 일부의 독립된 코드로 분리해서 만들어 놓은 것을 말한다.
모듈은 프로젝트의 코드를 깨끗한 구조로 정돈하는 데 도움을 준다.
자바스크립트에서는 모듈을 구현하는 가장 쉬운 방법은 객체 리터럴을 사용하는 방법이다.
아래의 경우 가장 일반적인 모듈 형태이다.
`obj`변수에 중괄호와 함께 내부 변수 및 내부 함수(method)를 모두 갖고 있어야 한다.

```javascript
var obj = {
  key: 'value',
  method: function () {
    console.log('hello world');
  }
}
```
객체 리터럴은 사용하기 간편하고 유용하지만 위와 같이 작성했을 경우 보안에 취약하다.
자바스크립트는 일반적으로 브라우저에 사용자에게 직접 노출되기 때문에 위와 같이 **글로벌 공간에 변수를 선언하는 것은 되도록 피해야 한다.**

```javascript
const PlusMinus =(function () {
  let num = 0;
  
  return {
     plus: function () {
       num++;
     },
     minus: function () {
       num--;
     },
     getNum: function () {
       return num;
     }
  } 
})()
```
![image.png](https://images.velog.io/post-images/760kry/5618f830-3ab5-11ea-b1dc-934ebce8968b/image.png)

위와 같이 클로저나 익명 함수(즉시 실행 함수)를 사용하여 객체를 리턴하고 `num`변수를 함수 안에 선언하여 보호한다. 하지만 위 코드는 재사용이 번거롭다.

```javascript
(function () {
  function plusMinus () {
    let num = 0;
  
    return {
       plus: function () {
         num++;
       },
       minus: function () {
         num--;
       },
       getNum: function () {
         return num;
       }
    }
  }
  
  let num1 = plusMinus();
  let num2 = plusMinus();
  
})()
```
위 코드는 `plusMinus`라는 함수를 익명 함수(즉시 실행 함수)로 감싸주어 변수를 보호할 수 있고, 재사용성을 높일 수 있다.

- 장점: 코드를 정리할 때 사용한다.
- 단점: 코드의 양이 많아지고 파일 크기도 늘어난다.


### Singletons 싱글톤 패턴
---
싱글톤 패턴은 상황에 따라서 만드는 것을 제한을 두는 것으로 특정 클래스의 객체를 한 개만 유지하는 패턴이다.
자바스크립트에서는 이미 객체 리터럴을 이용한 객체 생성 방법이 싱글톤 패턴과 대표적인 예이다.

```javascript
var obj = {
  key: 'value',
  method: function () {
    console.log('hello world');
  }
}
```

위 코드는 단 하나밖에 없는 객체이다. 하지만 변수들이 모두 전역 변수에 노출되어 보호할 수 없다.

```javascript
var singleton = (function() {
  var a;
  var b = 'hello';
  function foo () {
    return {
      c: b,
      d: function() {
        alert(b);
      }
    };
  }
  return {
    e: function() {
      if (!a) {
        a = foo ();
      }
      return a;
    }
  }
})();
var first = singleton.e();
var second = singleton.e();
console.log(first === second); // true;
```
위와 같이 변수를 보호하고 객체를 하나만 유지하는 것을 싱글톤 패턴이라 한다.

### Mixin 패턴
---
어떠한 기능을 공유하여 사용하는 것을 말한다.
중복되는 코드가 있다면 중복되는 것을 함수로 만들어 주면서 코드의 재 사용을 높인다. mixin은 제약이 없고 유연하다.

재사용성을 높이는 방법에는 두가지가있다.

#### 1. 함수 만들기
```javascript
var person1 = {
   name: function () {},
   job: function () {},
   eat: function () {},
}
var person2 = {
   name: function () {},
   job: function () {},
   eat: function () {},
}
var person3 = {
   name: function () {},
   job: function () {},
   eat: function () {},
}
```

위와 같이 breath라는 속성이 중복된다면 어느 한쪽을 수정하려고 할 때 모두 수정해야 하기 때문에 번거롭다.
아래와 같이 공통되는 속성을 함수로 만들어 주면 재사용이 쉽고 제약이 없다.

```javascript
var person1 = {
   name: function () {},
   job: function () {},
}
var person2 = {
   name: function () {},
   job: function () {},
}
var person3 = {
   name: function () {},
   job: function () {},
}
eatMixin(person1);
eatMixin(person2);
eatMixin(percon3);
function eathMixin (person) {
   person.eat = function () {};
}
```

#### 2. prototype
```javascript
function Computer (specifications) {
   this.specifications = specifications;
}
function Oven (bake) {
  this.bake = bake;
}
mixin(coputer.prototype);
mixin(oven.prototype);
function mixin (target) {
   target.energy = function () {
      console.log('I need energy');
   }
}
```
위와같이 여러 생성자 함수에 프로퍼티와 메소드들을 복사 해서 재사용 할 수 있다.
단, prototype은 사용할 때 prototype chain 등 주의 깊게 생각해서 사용해야 한다.



### Pub-Sub 패턴
---
pub-sub 은 Publish/Subscribe를 줄여서 부르는 말이다. 발행/구독의 개념이다.
예를 들어, 유튜브 채널 구독신청을 하면 새로운 영상이 업데이트될 때마다, 구독자는 새로 게시된 영상의 알림을 받을 수 있다.
pub/sub 패턴은 발행자(게시자)와 구독자의 분리이다.
발행자와 구독자는 느슨하게 연결(Loose coupling) 되어 있으며 서로의 존재를 알 필요가 없이 비동기 메시지 방식의 도움을 받아 통신하며 각각 독립적으로 작동한다.
이러한 방식 때문에 발행자와 구독자의 결합도가 낮아 확장성이 좋다.
`예) click, mouseover, keypress 와 같은 브라우저 이벤트`

![pubsub.gif](https://images.velog.io/post-images/760kry/f6ee3570-4381-11ea-8039-f19deed0169c/pubsub.gif)
