---
title: Promise / async & await 활용
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-01-27
categories: [Javascript]
tags: [Promise, async await]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

배열은 순차적으로 비동기 처리를 하려고 할 때, promise 사용법을 블로깅 하려고 한다.
반복문이 있을 경우 promise를 사용하기 어려웠다.
예를 들어, 

```javascript
function test () {
  return new Promise(function (resolve, reject) {
    for(let i=0; i < 5; i++) {
      setTimeout(function(){
        console.log(i);
        resolve();
      }, i*1000);
    }
  })
}

test().then(function(){
  console.log('Done')
});

```
i를 순차적으로 콘솔에 찍은후 then을 실행하여 ''Done''를 찍으려고 작성했던 코드이다.
하지만 아래와 같이 실행된다.


![image.png](https://images.velog.io/post-images/760kry/2cbe5450-40e9-11ea-bccd-0d7094132f4e/image.png)


위와 같이 실행되는 이유는 처음 i를 찍은 후 resolve()를 호출했기 때문에 then()을 실행하고 콜 스택에 쌓여있던 함수들이 순차적으로 실행된 것이다. `resolve()`는 한번 호출되면 바로 `then()` 을 실행한다.


**반복문을 순차적으로 실행 후 then을 호출하려면 어떻게 해야 할까?**


**아래는 재귀를 사용해서 구현한 소스이다.**
```javascript
const array = [1, 2, 3, 4, 5];

let index = 0;

test();

function test () {
  const item = array[index]
  
  if(array.length === index){
    return console.log('done');
  }else{
    test2(item).then(test);
    index++; 
  }
}

function test2(item){
  return new Promise(function (resolve, reject) {
    setTimeout(function(){
      console.log(item);
      resolve();
    },1000)
  });
}
```

![image.png](https://images.velog.io/post-images/760kry/08b17000-418a-11ea-aeb6-27f76e9275a0/image.png)

**async & await 을 활용해 보았다.**
```javascript
function delay(item) {
  return new Promise(function(resolve, reject){
    setTimeout(function(){
      console.log(item);
      resolve();
    },1000)
  })
}

async function test(array) {
  for(let i=0; i< array.length; i++){
    await delay(array[i]);
  }
  console.log('Done');
}
test([1,2,3,4,5]);

```
- 함수 앞에 async를 붙여 작성하게 되면 비동기 함수가 된다. 
- async 함수는 promise를 반환한다. 
- await은 async 함수 안에서만 사용이 가능하다. 
- await 함수 실행이 모두 완료돼야만 다음 로직을 실행한다.

![image.png](https://images.velog.io/post-images/760kry/b0e57700-40e8-11ea-a096-0ba98c7e76aa/image.png)
