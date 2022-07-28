---
title: React에서 setInterval 사용하기
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-07-28
categories: [Frontend]
tags: [react setInterval]
math: true
mermaid: true
---

## 업데이트 함수 setState를 활용하여 구현

```jsx
import { useEffect, useState } from "react";

import "./App.css";

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount((prev) => prev + 1);
    }, 1000);

    return () => {
      clearInterval(timer);
    };
  }, []);

  return (
    <div className="App">
      <h1>{count}</h1>
    </div>
  );
}

export default App;
```

위 소스는 1초마다 카운트를 1 씩 올려주는 코드입니다.

-   useEffect 두 번째 인자에 빈 배열을 넣어 componenetDidMount와 같이 timer 함수를 한 번만 세팅합니다.
-   setInnerval로 timer를 세팅하고 컴포넌트가 사라질 때 clearInterval로 timer를 정리합니다.
-   useEffect 두 번째 인자에 빈 배열을 넣을 경우 렌더링 후 한 번만 실행되기 때문에  
    **setState의 업데이트 함수**(setCount(prev => prev + 1))를 활용하여 구현합니다. 
    -   업데이트 함수 setCount(prev => prev + 1) 사용한 경우 
    prev는 항상 최신 상태 값을 가져오는 것을 보장할 수 있습니다.
    -   setState(count + 1)을 사용할 경우 초기 count 값만을 가져오기 때문에 count가 1에서 멈춰 버립니다.

#### 문제점

업데이트 함수인 setState를 사용해서 상태 변경에서는 count 업데이트를 할 수 있지만 props가 변경된 경우에는  
타이머 함수가 실행되지 않습니다.  useEffect 두 번째 인자에 빈 배열을 넣어 timer함수를 한 번만 세팅했기 때문입니다. 

## useRef를 활용한 타이머 구현

```jsx
import { useEffect, useRef, useState } from "react";

import "./App.css";

function App() {
  const timerRef = useRef();

  const [count, setCount] = useState(0);

  const handleCount = () => {
    setCount(count + 1);
  };

  const handleRestart = () => {
    if (!timerRef.current) {
      timerRef.current = setInterval(handleCount, 1000);
    }
  };

  const handleStop = () => {
    if (timerRef.current) {
      clearInterval(timerRef.current);
      timerRef.current = null;
    }
  };

  useEffect(() => {
    timerRef.current = setInterval(handleCount, 1000);

    return () => {
      clearInterval(timerRef.current);
    };
  });

  return (
    <div className="App">
      <h1>{count}</h1>
      <button onClick={handleRestart}>restart</button>
      <button onClick={handleStop}>stop</button>
    </div>
  );
}

export default App;
```

timerRef.current에 timer를 저장하여 타이머를 정지하는 stop버튼과 
다시 시작하는 restart 버튼을 추가하여 구현하였습니다.

-   useRef는 리렌더링을 방지하고 useRef의 timerRef 객체는 컴포넌트의 전 생애주기 동안 유지되어 
useRef로 관리하는 값은 state 값이 변경되어도 ref의 값을 유지합니다.
-   count 상태 변경만의 리렌더링을 나타내고 restart 버튼 or stop 버튼을 클릭해도 리렌더링이 일어나지 않습니다.
