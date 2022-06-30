---
title: CSS layout
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2019-11-19
categories: [Frontend]
tags: [css layout]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

# block vs  inline
## block element
* 화면 전체를 사용하는 태그

```html
<h1>Hello world</h1>
```

## inline element
* 화면의 일부를 차지하는 태그

```html
<span>반갑습니다.</span>
```
## block vs  inline

```html
<h1>Hello world</h1>
안녕하세요. <span>반갑습니다.</span>
```

![image.png](https://images.velog.io/post-images/760kry/a31251b0-0a11-11ea-bec3-cd3fb087bbe4/image.png)

## display
`display` 속성을 사용해서 `block element` 를 `inline element` 로 바꿀수 있고, `inline element`를 `block element` 로 바꿀수 있다.

### html

```html
<h1>Hello world</h1>
안녕하세요. <span>반갑습니다.</span>
```

### css

```css
h1,span {
   border:1px solid red;
}
h1 {
   display: inline;
}
span {
   display:block;
}
```


![image.png](https://images.velog.io/post-images/760kry/f0cc2060-0a12-11ea-bec3-cd3fb087bbe4/image.png)


# 박스 모델(box model)

사각형의 형태(box)로 그 태그의 부피감을 결정한다

## 박스모델 관련 속성들
* margin : 태그와 태그 사이의 여백.
* padding : 태그와 내부 컨텐츠 사이의 여백
* border: width style color; 순서로 값 지정

**`inline element`는 `width`, `height` 값이 무시 된다.**

# box-sizing
* 박스의 크기를 화면에 표시하는 방식을 변경하는 속성. 
* width와 height는 엘리먼트의 컨텐츠의 크기를 지정한다. 
* content를 기준으로 하기 때문에 테두리가 있는 경우에는 테두리의 두께로 인해서 원하는 크기를 찾기가 어렵다. 

**`box-sizing:border-box;` 로 지정하면  테두리를 포함한 크기를 지정할 수 있기 때문에 예측하기가 더 쉽다.**

## html

```html
<div id="small">Hello</div>
<div id="large">Hello</div>
```
## css

```css
div {
    margin:10px;
    width:150px;
}

#small {
    border:10px solid black;
}

#large{
    border:30px solid black;
}
```
![image.png](https://images.velog.io/post-images/760kry/48f40a60-0a17-11ea-bb33-93f44343d06a/image.png)

## css `* { box-sizing:border-box; }`

```css
* {
     box-sizing:border-box;
 } 
 
div {
    margin:10px;
    width:150px;
}

#small {
    border:10px solid black;
}

#large{
    border:30px solid black;
}

```

![image.png](https://images.velog.io/post-images/760kry/b752bd80-0a17-11ea-99d9-5d710cd0121f/image.png)

#  position
## 엘리먼트의 위치를 지정하는 4가지 방법

* static
* relative
* absolute
* fixed

## static VS relative
***
### `position: static`
기본값으로 `position` 값을 지정하지 않을경우 `static`이 기본값으로 들어간다.
### `position: relative`
`position` 속성의 값을 `relative`로 할 경우 부모 태그를 기준으로 'position' 을 잡는다. 
#### html
```html
    <div id='container'>
        <div id='parent'>
          부모
          <div id='me'>
            me
          </div>
        </div>
    </div>
```
#### css

```css
* {
    box-sizing: border-box;
}

#container {
    border: 2px solid black;
}

#parent {
    border: 2px solid black;
    width: 50%;
    height: 100%;
    position: relative;
}

#me {
    border: 2px solid black;
    position: relative;
    width: 50%;
    height: 100%;
}
```

![image.png](https://images.velog.io/post-images/760kry/749b67a0-0a29-11ea-8c17-6547a543d132/image.png)



## absolute
***
부모태크를 무시하고 body태그 기준으로 위치를 잡는다.
부모태그중 positon의 값이 static 아닌경우 부모태그를 기준으로 위치를 잡는다.

### html
```html
    <div id='container'>
        <div id='parent'>
          부모
          <div id='me'>
            me
          </div>
        </div>
    </div>
```

### css

```css
* {
    box-sizing: border-box;
}

#container {
    border: 2px solid black;
}

#parent {
    border: 2px solid black;
}

#me {
    background-color: black;
    color: white;
    border: 2px solid black;
    position: absolute;
    left:0;
    top:0;
}
```

### 부모태그에 `position` 값이 없을때

***
![image.png](https://images.velog.io/post-images/760kry/e9fecc50-0a2c-11ea-8c17-6547a543d132/image.png)

### 부모태그에 `position` 값이 있을때
***
![image.png](https://images.velog.io/post-images/760kry/452aa450-0a2d-11ea-9782-154a6ef44a29/image.png)


## fixed 
***

부모태그를 무시하고 스크롤을 내려도 지정된 위치에 있는다.

### html

```
    <div id='container'>
        <div id='parent'>
          부모
          <div id='me'>
            me
          </div>
        </div>
    </div>
```

### css

```css
* {
    box-sizing: border-box;
}

#container {
    border: 2px solid black;
}

#parent {
    border: 2px solid black;
    position: relative;
}

#me {
    background-color: black;
    color: white;
    border: 2px solid black;
    position: fixed;
    left:0;
    top:0;
}
```

![image.png](https://images.velog.io/post-images/760kry/ccc52dc0-0a2a-11ea-850a-d1a069599dc8/image.png)


# flex

flex를 사용하기 위해서는 컨테이너 태그에 `display:flex` 속성을 부여한다.
`flex-direction`을 이용해서 정렬방향을 바꿀 수 있다.
`flex-directiondms`  기본적으로 `row` 라는 기본값을 갖게 된다.


![image.png](https://images.velog.io/post-images/760kry/18961ef0-0a7e-11ea-bdab-4f97d25fff59/image.png)

## `display: flex` `flex-directiondms: row`
***
`flex-directiondms`  기본적으로 `row` 라는 기본값을 갖게 되고 수평방향으로 정렬된다.
#### html
```html
    <div class="container">
        <div class="item">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
        <div class="item">4</div>
        <div class="item">5</div>
    </div>
```
#### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
        }
```

![image.png](https://images.velog.io/post-images/760kry/97731480-0a7e-11ea-bdab-4f97d25fff59/image.png)

### `flex-direction:row-reverse;`
***
**`row` 반대로 정렬된다.**
#### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row-reverse;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
        }
```

![image.png](https://images.velog.io/post-images/760kry/4ad10c80-0a7f-11ea-a712-e96416aedd9b/image.png)

### `flex-direction:column;`
***
**수직방향으로 정렬된다.**
#### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:column;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
        }
```

![image.png](https://images.velog.io/post-images/760kry/00582610-0a80-11ea-b91b-4d9c8bbe92ae/image.png)
### `flex-direction:column-reverse`
***
**`column` 반대로 정렬된다.**
### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:column-reverse;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
        }
```


![image.png](https://images.velog.io/post-images/760kry/61635460-0a81-11ea-9c81-1fc53304b068/image.png)

## flex-basis
***
**`flex` 방향에 해당되는 엘리먼트의 크기를 지정한다.**
#### html
```html
    <div class="container">
        <div class="item first">1</div>
        <div class="item">2</div>
        <div class="item">3</div>
        <div class="item">4</div>
        <div class="item">5</div>
    </div>
```
#### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
        }
        .first {
            flex-basis: 200px;
        }
```

![image.png](https://images.velog.io/post-images/760kry/866888e0-0a84-11ea-a712-e96416aedd9b/image.png)

## flex-grow & shrink
***
### flex-grow
`flex-grow`의 기본값은 `0`이다.
`flex-grow`에 값을 주면 element 들이 여백 전체를 n/1로 나눠 갖는다.

### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
            flex-grow:1;
        }
```

![image.png](https://images.velog.io/post-images/760kry/5fd47a30-0a85-11ea-a712-e96416aedd9b/image.png)

**n/1에서 우선순위가 `.first`가 높아진다.**
### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
            flex-grow:1;
        }
        .first {
            flex-grow:2;
        }
```

![image.png](https://images.velog.io/post-images/760kry/329ac280-0a86-11ea-b91b-4d9c8bbe92ae/image.png)

**`.item`에 `flex-grow:0;`을 주게 되면 `.first`가 여백을 독식하게 된다. `.first`의 `flex-grow`값을 어떠한 것으로 바꿔도 여백을 독식한다.**
### css
```css
        .container {
            background-color: powderblue;
            height:200px;
            display:flex;
            flex-direction:row;
        }
        .item {
            background-color: tomato;
            color:white;
            border:1px solid white;
            flex-grow:0;
        }
        .first {
            flex-grow:2;
        }
```

![image.png](https://images.velog.io/post-images/760kry/678f03b0-0a87-11ea-998e-2d8ec5cd30f2/image.png)

## flex-shrink
***
**설정된 숫자값에 따라 flex-container 요소 내부에서 flex-item 요소의 크기가 축소된다.
`flex-shrink: 0;`일 때 는 element의 크기가 축소 되지 않는다.**

![image.png](https://images.velog.io/post-images/760kry/b9c2c1c0-0a88-11ea-998e-2d8ec5cd30f2/image.png)

***
* 참조 https://codepen.io/enxaneta/pen/adLPwv
