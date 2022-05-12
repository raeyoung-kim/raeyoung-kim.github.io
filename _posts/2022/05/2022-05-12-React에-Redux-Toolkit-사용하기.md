---
title: React에 Redux Toolkit 사용하기
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-05-12
categories: [React, Redux]
tags: [react @reduxjs/toolkit, redux toolkit, '@reduxjs/toolkit']
math: true
mermaid: true
---

## Redux Toolkit이란?
[Redux Toolkit](https://redux.js.org/introduction/why-rtk-is-redux-today)은 redux에서 지원하는 개발 도구이다.

- 스토어 설정, 리듀서, 데이터 가져오기 등 Redux 로직을 작성하기 위해 <U>권장되는 접근 방식</U>이다.
- Redux Toolkit의 configureStore는 설정을 단순화하고 일반적인 버그를 방지하는 데 도움이 되는 향상된 createStore 버전이다.

### 사용하는 이유
Redux Toolkit은 아래와 같은 Redux의 문제점을 보안해 준다.

- 복잡한 Redux 저장소 구성을 어느 정도 해결해 줄 수 있다.
- 리덕스를 사용하다 보면 <U>코드 양이 점점 많아</U>지게 되는데 `Redux Toolkit`을 사용하면 자체적으로 immer를 내부적으로 도입해서 사용하고 있어 코드의 양이 줄어든다.(boilerplate)
- Redux에서 주요 기능들을 사용하기 위해 많은 패키지를 추가해야 하는데 자주 사용하는 기능들을 모아두어(예: redux-thunk, redux devtool 등 디폴트로 제공) 패키지 의존성을 줄여준다. **(saga를 제외한 기능들은 거의 모두 제공된다.)**

## 사용 방법

### Install
Redux 툴킷에는 Redux 코어와 Redux 응용 프로그램(예: Redux Thunk 및 Reselect)을 구축하는 데 필수적이라고 생각하는 기타 주요 패키지가 포함되어 있다.

```bash
npm install react-redux @reduxjs/toolkit
```

> [기존 Redux 사용 방식](https://velog.io/@760kry/React-Redux)에서 아래와같이 변경한다.

### slice
- [Slice](https://redux-toolkit.js.org/api/createSlice)는 `CreateSlice`를 통해 Action과 Reducer를 한 번에 정의할 수 있다.

- 슬라이스를 식별하기 위한 `문자열 이름(name)`, `초기 상태 값(initialState)`, `상태 업데이트 방법(reducers)`을 정의한다.
  - name : 이름을 작성한다.
  - initialState : 초깃값 세팅한다.
  - reducers : 리듀서를 작성한다.(리듀서의 키값으로 액션 함수가 자동으로 생성된다.)

`src/services/store/slice/user.ts`
```ts
import { createSlice } from '@reduxjs/toolkit';

export const initialState = {
  name: null,
  isLogin: false,
};

export const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    setUserName: (state, action) => {
      state.name = action.payload;
    },
    setUserLogin: (state, action) => {
      state.isLogin = action.payload;
    },
  },
});

export const { setUser, setUserLoading } = userSlice.actions;

export default userSlice.reducer;
```


### configureStore
슬라이스에서 리듀서 함수를 가져와서 스토어에 추가한다.

- `combineReducers`를 사용하여 사용할 리듀서를 사용할 키값과 함께 정의한다.
- `configureStore`를 실행하여 [옵션](https://redux-toolkit.js.org/api/configureStore)과 함께 스토어를 생성한다.
   - `reducer`에 `combineReducers`으로 생성한 `rootReducer`를 넣어준다.
   - `middleware` 배열을 반환하는 옵션으로 미들웨어를 사용할 수 있다[getDefaultMiddleware](https://redux-toolkit.js.org/api/getDefaultMiddleware)와 함께 툴킷에서 제공하는 기본 미들웨어를 호출하고 다른 미들웨어를 배열에 넣어 같이 사용할 수 있다.

```ts
import { combineReducers, configureStore } from '@reduxjs/toolkit';
import logger from 'redux-logger';
import userReducer from './slice/user';


/*
  slice(Action과 Reducer를 한번에 정의한 것)
*/
const rootReducer = combineReducers({
  userReducer: userReducer,
});

const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>  getDefaultMiddleware().concat(logger),
  // preloadedState (서버 사이드 렌더링 전용)
});

export type RootState = ReturnType<typeof store.getState>;

export default store;
```



### React에 Redux 스토어 전달하기

저장소가 생성되면 애플리케이션(`<App>`) 주위에 React-Redux `<Provider>`를 감싸 생성된 저장소(store)를 전달한다.
  
`src/index.tsx`
```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

import { Provider } from 'react-redux';
import store from 'src/services/store';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
);

reportWebVitals();
```

### 상태 변경 및 확인
- react-redux에서 제공하는 useDispatch, useSelector로 상태 관리를 한다.
- 슬라이스에서 작성한 리듀서 액션을 사용해서 상태 값을 변경할 수 있다.

```tsx
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { RootState } from 'src/services/store';
import { setUserName } from 'src/services/store/slice/user';

const MainPage = () => {
  const dispatch = useDispatch();
  const user = useSelector((state: RootState) => state.userReducer);
  
  console.log(user);
  
  return (
    <div>
      <button
        onClick={()=> dispatch(setUserName('raeyoung'))}
      >
        test
      </button>
    </div>
  );
};

export default MainPage;
```
