---
title: underscore
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2019-12-16
categories: [Javascript]
tags: [underscore]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## _.identity
***
```javascript
  _.identity = function(val) {
    return val;
  };
```
`_.identity`는 인자를 그대로 반환 한다.

## _.first
***
```javascript
  _.first = function(array, n) {
    return n === undefined ? array[0] : array.slice(0, n);
  };
```
`_.first`는 배열의 처음 n개의 element를 담은 배열을 반환한다. 만일 n이 undefined일 경우, 단순히 첫번째 element를 반환한다.

## _.last
***
```javascript
  _.last = function(array, n) {
    if(n === undefined){
      return array[array.length-1];
    }else if(array.length <= n){
      return array;
    }else{
      return array.slice(array.length-n, array.length);
    }
  };
```
`_.last`는 마지막 n개의 element를 담은 배열을 반환한다. 만일 n이 undefined일 경우, 단순히 마지막 element를 반환한다.

##  _.each
***
```javascript
  _.each = function(collection, iterator) {
    if(Array.isArray(collection)){
      for(let i=0; i < collection.length; i++){
        iterator(collection[i], i, collection)
      }
    }else if(typeof collection === 'object'){
      for(let key in collection){
        iterator(collection[key], key, collection)
      }
    }
  };
```
`_.each`는 각 요소에 iteratee를 적용한다.

##  _.indexOf
***
```javascript
  _.indexOf = function(array, target) {
  
    var result = -1;

    _.each(array, function(item, index) {
      if (item === target && result === -1) {
        result = index;
      }
    });

    return result;
  };
```
```javascript
    for(let i=0; i < array.length; i++){
      if(array[i] === target){
        return i;
      }
    }
    return -1;
```
`_.indexOf`는 target으로 전달되는 값이 배열에서 발견되면, 그 index를 반환하고, 만일 배열에서 발견할 수 없다면 -1을 반환한다.

## _.filter
***
```javascript
  _.filter = function(collection, test) {
    var arr = [];
    for(let i=0; i < collection.length; i++){
      if(test(collection[i])){
        arr.push(collection[i]);
      }
    }
    return arr;
  };
```
```javascript
  _.filter = function(collection, test) {
    var result = [];
    _.each(collection, function(el){
      if(test(el)){
        result.push(el);
      }
    })
    return result;
  };
```
`_.filter`는 테스트 함수를 통과하는 모든 element를 담은 배열을 반환한다.

## _.reject
***
```javascript
  _.reject = function(collection, test) {
    var arr = [];
    for(let i=0; i < collection.length; i++){
      if(!test(collection[i])){
        arr.push(collection[i]);
      }
    }
    return arr;
  };
```
```javascript
  _.reject = function(collection, test) {
    return _.filter(collection, function(el){
      return !test(el);
    });
  };
```
`_.reject`는 테스트 함수를 통과하지 않는 모든 element를 담은 배열을 반환한다.

##  _.uniq
***
```javascript
  _.uniq = function(array) {
    var result = [];
    for(let i=0; i < array.length; i++){
      if(_.indexOf(result, array[i]) === -1){
        result.push(array[i]);
      }
    }
    return result;
  };
```
```javascript
  _.uniq = function(array) {
    var arr = [];
    _.each(array, function(el){
      if(_.indexOf(arr, el) === -1){
        arr.push(el);
      }
    });
    return arr;
  };
```
```javascript
  _.uniq = function(array) {
    var obj = {};
    for(let i=0; i < array.length; i++){
      obj[array[i]] = array[i];
    }
    return Object.values(obj);
  };
```
`_.uniq`는 element가 중복되지 않는 새로운 배열을 만들어 반환한다.

## _.map 
***
```javascript
  _.map = function(collection, iterator) {
    var result = [];
    for(let i=0; i < collection.length; i++){
      result.push(iterator(collection[i]));
    }
    return result;
  };
```
```javascript
  _.map = function(collection, iterator) {
    var result = [];
    _.each(collection, function(el){
      result.push(iterator(el))
    });
    return result;
  };
```
`_.map`는 iterator를 각 element에 적용한 결과를 담은 새로운 배열을 반환한다.

## _.pluck
***
```javascript
  _.pluck = function(collection, key) {
    return _.map(collection, function(item) {
      return item[key];
    });
  };
```
```javascript
  _.pluck = function(collection, key) {
    var result = [];
    _.each(collection, function(el){
      result.push(el[key]);
    })
    return result;
  };
```
`_.pluck`은 collection의 key값이 동일한 값들만을 모아 반환한다.

## _.reduce
***
```javascript
  _.reduce = function(collection, iterator, accumulator) {
    var index = 0;

    if(accumulator === undefined){
      accumulator = collection[0];
      index = 1;
    }
    for(let i=index; i < collection.length; i++){
      accumulator = iterator(accumulator, collection[i])
    }
    return accumulator;
  };
```
```javascript
  _.reduce = function(collection, iterator, accumulator) {
    var slice = Array.prototype.slice;

    if(accumulator === undefined){
      accumulator = collection[0];
      collection = slice.call(collection,1);
    }
    _.each(collection, function(el){
      accumulator = iterator(accumulator, el);
    });
    return accumulator
  };
```
`_.reduce`는 입력받은 collection으로부터 iteratee의 내용이 무엇인지에 따라서 하나의 결과값을 반환한다.
accumulator는 초기값으로, 이 accumulator가 주어지느냐, 주어지지않느냐의 두가지 경우로 나뉜다.

## _.contains
***
```javascript
  _.contains = function(collection, target) {
    for(let key in collection){
      if(collection[key] === target){
        return true;
      }
    }
    return false;
```
```javascript
  _.contains = function(collection, target) {
    collection = Object.values(collection);
    return _.reduce(collection, function(wasFound, item) {
      if (wasFound) {
        return true;
      }
      return item === target;
    }, false);
  };
```
```javascript
  _.contains = function(collection, target) {
    return _.reduce(collection, function(a, b){
      if(b===target){
        b = true;
      }else{
        b = false;
      }
      return Boolean(a+b);
    },false);
  };
```
`_.contains`는 배열 또는 객체가 주어진 값을 포함하는지 체크한다. collection에 target이 존재한다면 true, 그렇지 않다면 false를 반환한다.
 

##  _.every
***
```javascript
  _.every = function(collection, iterator) {
    if(!iterator){
      iterator = _.identity;
    }
    for(let i=0; i < collection.length; i++){
      if(!iterator(collection[i])){
        return false;
      }
    }
    return true;
  };
```
```javascript
  _.every = function(collection, iterator) {
    var result = true
    if(!iterator){
      iterator = _.identity;
    }
    _.each(collection,function(el){
      if(!iterator(el)){
        result = false;
      }
    });
    return result;
  };
```
`_.every`는 모든 element가 iterator에 의해 truthy한지 체크한다. collection의 모든 element들이 iterator의 조건이 만족해야만 true를 반환한다. 단 하나의 element라도 조건에 만족하지 못한다면 false를 반환한다.
##  _.some
***
```javascript
  _.some = function(collection, iterator) {
    return !_.every(collection, function(el){
      if(typeof iterator !== 'function'){
        iterator = _.identity;
      }
      return !iterator(el);
    })
  };
```
`_.some`은 element가 하나라도 iterator에 의해 truthy한지 체크한다. collection의 element들 중 하나라도 iterator의 조건에 만족한다면 true를 반환한다.

## _.extend
***
```javascript
  _.extend = function(obj) {
    for(let i=0; i < arguments.length; i++){
      for(let key in arguments[i]){
        obj[key] = arguments[i][key];
      }
    }
    return obj;
  };
```
```javascript
  _.extend = function(obj) {
    _.each(arguments, function(el){
      for(let key in el){
        obj[key] = el[key];
      }
    });
    return obj;
  };
```
`_.extend` 주어진 객체를 전달된 인자의 모든 속성으로 확장한다.

## _.defaults
***
```javascript
  _.defaults = function(obj) {
    for(let i=0; i<arguments.length; i++){
      for(let key in arguments[i]){
        if(!obj.hasOwnProperty(key)){
          obj[key] = arguments[i][key];
        }
      }
    }
    return obj;
  };
```
```javascript
  _.defaults = function(obj) {
    _.each(arguments, function(el){
      for(let key in el){
        if(!obj.hasOwnProperty(key)){
          obj[key] = el[key];
        }
      }
    });
    return obj;
  };
```
`_.defaults`는 주어진 객체에 전달된 인자의 속성이 없다면 전달된 인자의 속성으로 확장한다.

## _.once
***
```javascript
_.once = function(func) {
    var alreadyCalled = false;
    var result;
    
    return function() {
      if (!alreadyCalled) {
    
        result = func.apply(this, arguments);
        alreadyCalled = true;
      }
      
      return result;
    };
  };
```
`_.once`는 최대 한번만 호출할 수 있는 함수를 반환한다. 이후의 호출은 이전에 한번 리턴된 값만을 반환해야한다.

## _.delay
***
```javascript
  _.delay = function(func, wait, ...args) {
    setTimeout(function(){
      func(...args);
    },wait);
  };
```
`_.delay`는 주어진 시간 동안 함수를 지연한 다음 제공된 argument로 함수를 호출한다.

## _.flatten
***
```javascript
  _.flatten = function(nestedArray, result) {
    var arr = [];
  
    for(let i=0; i< nestedArray.length; i++){
      if(!Array.isArray(nestedArray[i])){
        arr.push(nestedArray[i]);
      }else{
        arr = arr.concat(_.flatten(nestedArray[i]));
      }
    }
    return arr;
  };
```
```javascript
  _.flatten = function(nestedArray, result) {
    var result = [];
    _.each(nestedArray, function(el){
      if(!Array.isArray(el)){
        result.push(el);
      }else{
        result = result.concat(_.flatten(el));
      }
    })
    return result;
  };
```
`_.flatten`는 다차원 배열을 가져와서, 1차원 배열로 변환한다. 새로운 배열에는 다차원 배열의 모든 요소가 포함되어야 한다.

## _.shuffle
***
```javascript
  _.shuffle = function(array) { 
    var arr = array.slice();
    return arr.sort(function(){
      return Math.random()-Math.random();
    });
  };
```
`_.shuffle`는 배열 내용의 순서를 랜덤하게 변경한다.

## _.sortBy
***
```javascript
  _.sortBy = function(collection, iterator) {
    if(typeof iterator === 'string'){
      return collection.sort(function(a, b){
        return a[iterator]- b[iterator];
      });
    }else{
      return collection.sort(function(a, b){
        return iterator(a)-iterator(b);
      })
    }
  };
```
```javascript
  _.sortBy = function(collection, iterator) {
    var sort = Array.prototype.sort;

    if(typeof iterator === 'string'){
      return sort.call(collection, function(a, b){
        return a.length - b.length;
      })
    }else{
      return sort.call(collection,function(a, b){
        return iterator(a)-iterator(b);
      })
    }
  };
```
`_.sortBy`는 Collection 값들을 원하는 기준으로 정렬해준다.


## _.throttle
***
```javascript
  _.throttle = function(func, wait) {
    var result;
    return function(){
      if(!result){
        result = setTimeout(function(){
          func.apply(this, arguments);
          result=null;
        },wait);
      }
    }
  };
```
`_.throttle`는 마지막 함수가 호출된 후 일정 시간이 지나기 전에 다시 호출되지 않도록 하는 것

## Debounce
***
```javascript
function debounce(fn, wait) {
    var timer;
    return function() {
        clearTimeout(timer);
        timer = setTimeout(function(){
            fn.apply(this, arguments);
        }, wait);
    }
}
```
`Debounce`는 연이어 호출되는 함수들 중 마지막 함수(또는 제일 처음)만 호출하도록 하는 것.

##  _.invoke
***
```javascript
  _.invoke = function(collection, functionOrKey, args) {
    var arr = [];
    if(typeof functionOrKey === 'string'){
      for(let i=0; i<collection.length; i++){
        arr.push(String.prototype[functionOrKey].apply(collection[i],args));
      }
    }else{
      for(let i=0; i<collection.length; i++){
        arr.push(functionOrKey.apply(collection[i], args));
      }
    }
    return arr
  };
```
```javascript
  _.invoke = function(collection, functionOrKey, args) {
    var result = [];
    if(typeof functionOrKey === 'string'){
      _.each(collection, function(el){
        result.push(String.prototype[functionOrKey].apply(el, args))
      });
    }else{
      _.each(collection,function(el){
        result.push(functionOrKey.apply(el, args));
      });
    }
    return result;
  };
```
`_.invoke`는 배열에 `functionOrKey`을 적용시킨 뒤, 해당 배열을 리턴시킵니다.

##  _.zip
***
```javascript
  _.zip = function() {
    var arr = [];
    for(let i=0; i < arguments.length; i++){
      arr.push(arguments[i].length);
    }
    var maxlength = Math.max.apply(null, arr);
    
    var result = [];
    for(let i=0; i < maxlength; i++){
      let newArr = [];
      for(let j=0; j < arguments.length; j++){
        if(arguments[j][i] === undefined){
          newArr.push(undefined);
        }else{
          newArr.push(arguments[j][i])
        }
      }
      result.push(newArr);
    }
    return result;
  };
```
`_.zip`은 배열의 각 인덱스를 요소들을 새로운 배열에 병합시켜 반환한다. 

예)
```
  _.zip(['a','b','c','d'], [1,2,3]) returns [['a',1], ['b',2], ['c',3], ['d',undefined]]
  ```
## _.intersection
***
```javascript
  _.intersection = function() {
    var arr = [];
    for(let i=0; i < arguments[0].length; i++){
      for(let j=1; j < arguments.length; j++){
        if(arguments[0][i]===arguments[j][i]){
          arr.push(arguments[0][i]);
        }
      }
    }
    return arr;
  };
```

```javascript
  _.intersection = function() {
    var arr = [];
    for(let i=0; i<arguments[0].length; i++){
      for(let j=1; j<arguments.length; j++){
        if(_.contains(arguments[j], arguments[0][i])){
          arr.push(arguments[0][i]);
        }
      }
    }
    return arr;
  };
```
`_.intersection`는 배열들의 교집합을 찾아 반환한다.

## _.difference
***
```javascript
  _.difference = function(array) {
    for(let i=0; i<arguments[0].length; i++){
      for(let j=1; j < arguments.length; j++){
        if(_.contains(arguments[j], arguments[0][i])){
          arguments[0][i]=false;
        }
      }
    }
    return _.filter(arguments[0],function(el){
      return el !== false;
    });
  };
```
`_.difference`배열들의 차집합을 찾아 반환한다.

## _.memoize
***
```javascript
  _.memoize = function(func) {
    var results = {};
    return function() {
      var args = Array.prototype.slice.call(arguments);
      var key = JSON.stringify(args);

      if (!(key in results)) {
        results[key] = func.apply(this, args);
      }

    return results[key];
    };
  };
```
