---
title: hash table / javascript 구현
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-01-19
categories: [Data Structure]
tags: [data structure, hash table ]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## hash table
---

어떠한 데이터를 받아 저장하려고 할 때, 저장할 데이터의 키를 받아 해시 코드로 변환해서 데이터 구조에 저장한다. 해시 코드를 만드는 것을 hashing이라고 하는데 hashing 이란 암호화하는 것이다. 해시 함수는 암호를 **일정한 길이**로 반환해준다. 대표적으로 ex) MD5, SHA .. 등 이 있다. 해시 함수는 연산을 여러 번 작용하더라도 **결과가 달라지지 않아야 한다.** 보통 **자바스크립트에서는 객체를 대체해서 사용**한다. 

주의할 점은, **`key` 값이 다르더라도** `hash function`에 의해 변환된 **`index`가 같은 충돌**이 발생할 수 있다. 이러한 경우를 **해시 충돌** 이라 한다.

![hash table.gif](https://images.velog.io/post-images/760kry/61d802d0-391e-11ea-a4f8-7fdde3503c02/hash-table.gif)

- 장점 : 빠르다. 탐색, 삽입, 삭제, 중복 데이터를 찾아내기 쉽다.
- 단점 : 저장할 공간은 많은데 들어올 메모리가 적을 때 효율적이지 않고, 저장할 때 순서가 없다. 또 O(1)의 일정 시간이 길면 모두 시간이 오래 걸린다.


### big - O

- insertion : O(1)
- deletion : O(1)
- search : O(1)

#### insertion & deletion  & search / O(1)
위의 그림과 같이 `key`값을 해시 함수를 돌려 일정한 길이로 암호화해준다. 해시 함수는 데이터 구조에 저장할 index 값을 돌려준다.
index 위치를 알고 바로 저장할 데이터를 `index` 위치에 바로 저장할 수 있기 때문에 O(1)이다. 
삭제와 검색 모두 마찬가지로 `index`로 직접적으로 삭제와 검색을 할 수 있기 때문에 O(1)이다.



## javascrpt로 Hash Table 구현
---
아래는 Hash Table을 구현한 자바스크립트 소스이다.
새로운 `key`와 `value`를 묶은 노드를 데이터 구조에 추가하고 `value`를 검색하고 삭제하는 기능까지 구현한 소스이다.
```javascript
var HashTable = function () {
  this._limit = 8;
  this._storage = LimitedArray(this._limit);
  this._arr = []
};

HashTable.prototype.insert = function (k, v) {
  var index = getIndexBelowMaxForKey(k, this._limit);
  var obj = {};
  var val = this._storage.get(index);

  if (!val) {
    obj[k] = v;
    this._storage.set(index, obj);
  } else {
    obj = val;
    obj[k] = v;
    this._storage.set(index, obj);
  }
};

HashTable.prototype.retrieve = function (k) {
  var index = getIndexBelowMaxForKey(k, this._limit);
  return this._storage.get(index)[k];
};

HashTable.prototype.remove = function (k) {
  var index = getIndexBelowMaxForKey(k, this._limit);

  this._storage.each(function (el, i) {
    if (index === i) {
      delete el[k]
    }
  });
};
```

사용하지 않는 변수를 지우고 참 -> 거짓 순으로 (예: `if (val) {} else {}`로 구현하는 게 더 읽기 좋다는 것과 동적으로 sotrage를 확장/축소하는 것도 구현해 보라는 피드백을 받았다. 지금 코드를 다시 보니 사용하지 않았던 변수를 지우지 않고 불필요했던 로직을 지우지 않았던 것이 보였다. 아래 소스는 피드백을 받은 것을 바탕으로 다시 코드를 구현한 소스이다. 아직 확장 / 축소 하는것은 구현 중이다.

```javascript
var HashTable = function () {
  this._limit = 8;
  this._storage = LimitedArray(this._limit);
};

HashTable.prototype.insert = function (k, v) {
  var index = getIndexBelowMaxForKey(k, this._limit);
  let value = this._storage.get(index);
  let obj = {};
  if (value) {
    obj = value;
    obj[k] = v;
    this._storage.set(index, obj);
  } else {
    obj[k] = v;
    this._storage.set(index, obj);
  }
};

HashTable.prototype.retrieve = function (k) {
  var index = getIndexBelowMaxForKey(k, this._limit);
  return this._storage.get(index)[k];
};

HashTable.prototype.remove = function (k) {
  var index = getIndexBelowMaxForKey(k, this._limit);
  this._storage.each(function (el, i) {
    if (index === i) {
      delete el[k];
    }
  });
};

```
