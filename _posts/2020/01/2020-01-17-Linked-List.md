---
title: Linked List / javascript 구현
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-01-17
categories: [Javascript, data structure ]
tags: [data structure, Linked List ]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## Linked List / 연결리스트
---

![linkedlist.gif](https://images.velog.io/post-images/760kry/f2080f70-38fc-11ea-8a14-7ddb1d5b4b35/linkedlist.gif)

여러 노드들이 위 그림과 같이 한 방향을 가리키는 연결 구조이다.
가장 처음 시작하는 노드를 `head`라고 하며, 가장 마지막 노드를 `tail`이라고 한다. 노드 들은 본인의 이전 노드와 다음 노드를 기억한다.

예를 들어, 어떠한 작업을 했을 때, 이전 단계로 작업을 되돌리기 위해 단축키 `컨트롤 z`로 이전 작업으로 이동할 수 있다. `컨트롤 z`로 계속 이전 작업 단계로 이동하다 보면 가장 초기 작업 단계를 `head`라고 표현할 수 있다. 또 단축키 `컨트롤 y`로 다시 작업 후의 단계로 이동할 수 있고, `컨트롤 y`로 계속 다음 작업 단계로 이동하다 보면 마지막에 작업했었던 상태로 돌아갈 수 있다. 마지막 작업 상태를 `tail`이라고 표현할 수 있다. 이렇게 서로 `이전` 과 `다음` 단계처럼 연결되어 있는 구조를 연결 리스트 linkde list라고 한다.

### big-O

- insertion: O(1)
- deletion: O(1)
- search: O(n)

#### insertion / O(1) 자료구조에 삽입

![linkedlist insert.gif](https://images.velog.io/post-images/760kry/5f387df0-390c-11ea-934e-6196557c6e38/linkedlist-insert.gif)

위 그림과 같이 삽입하고 싶은 연결 리스트의 위치만 안다면 `1`이 `3`을 바라보게 하고 `3`이 `2`를 바라보게 한다면 기존 javascript 배열처럼 자리를 찾기 위해 for loop 로 자리를 순위 하지 않아도 쉽게 자료 구주에 삽입할 수 있다. 한 번에 자기 자리를 찾아 들어갈 수 있기 때문에 big-O는 O(1)이다.

#### deletion / O(1) 삭제

![linkedlist.gif](https://images.velog.io/post-images/760kry/ce430c00-390d-11ea-991a-575cdc8f8f05/linkedlist.gif)

위 그림과 같이 어떤 노드를 삭제하고 싶다면 이전 노드가 삭제하고 싶은 노드의 다음 노드를 가리키도록 연결하면 된다. insert와 마찬가지로 기존 배열처럼 for loop로 삭제할 요소를 순회하지 않아도 한 번에 노드를 삭제할 수 있다. 때문에 big-O는 O(1)이다.

#### search / O(n)  찾기

![linkedlist serch.gif](https://images.velog.io/post-images/760kry/52592cd0-390f-11ea-a7b0-a72f7db8f52d/linkedlist-serch.gif)

위 그림과 같이 `3`이라는 노드를 찾기 위해서 처음 `head`부터 `3`이라는 노드가 나올 때까지 다음 노드를 가리키는 방향을 따라 찾아가야 한다. worst case로 `3`이라는 노드가 마지막에 위치하고 있을 수도 있기 때문에 big-O는 O(n)이다.


## javscript로 Linkde List 구현
---
아래는 Linked List를 구현한 자바스크립트 소스이다.
새로운 노드를 맨 마지막 tail로 넣어주고, 맨 앞의 head 노드를 삭제하고, list에 node가 있는지 확인해주는 기능까지 구현한 소스이다.
```javascript
var LinkedList = function () {
  var list = {};
  list.head = null;
  list.tail = null;

  list.addToTail = function (value) {
    var currNode;

    var node = new Node(value);
    if (!this.head) {
      this.head = node;
      this.tail = node;
    }
    else {
      currNode = this.head;
      while (currNode.next) {
        currNode = currNode.next;
      }
      currNode.next = node;
      this.tail = node;
    }
    return node;
  };

  list.removeHead = function () {
    var removeValue = this.head;
    this.head = this.head.next;
    return removeValue.value;
  };

  list.contains = function (target) {
    var accNode = this.head;
    while (accNode) {
      if (accNode.value === target) {
        return true;
      }
      else {
        accNode = accNode.next;
      }
    }
    return false;
  };

  return list;
};



var Node = function (value) {
  var node = {};

  node.value = value;
  node.next = null;

  return node;
};

```
피드백을 받은 후 다시 구현해보았다. addToTail 메소드를 작성할 때, 원래 작성했던 대로라면 O(1)이 아니라 O(n)이다. 원하는 위치로 가기 위해서 탐색해나가면서 원하는 위치에 추가하는 것이다. this.tail.next로 바로 추가하면 되는데 ...  addToTail 을 O(1)으로 다시 구현하고 head가 null일 경우를 추가해서 다시 구현해 보았다.

```javascript
var LinkedList = function () {
  var list = {};
  list.head = null;
  list.tail = null;

  list.addToTail = function (value) {
    let node = new Node(value);

    if (!this.head) {
      this.head = node;
      this.tail = node;
    } else {
      this.tail.next = node;
      this.tail = node;
    }
  };

  list.removeHead = function () {
    let removeNode = this.head;
    if (TimeRanges.head !== null) {
      this.head = removeNode.next;
      return removeNode.value;
    }
  };

  list.contains = function (target) {
    let accNode = this.head;
    while (accNode) {
      if (accNode.value === target) {
        return true;
      }
      accNode = accNode.next;
    }
    return false;
  };

  return list;
};



var Node = function (value) {
  var node = {};

  node.value = value;
  node.next = null;

  return node;
};

```
