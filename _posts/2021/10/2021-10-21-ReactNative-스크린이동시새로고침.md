---
title: stack or tab 스크린 이동 시 새로 고침
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2021-10-21
categories: [React Native]
tags: [React Native 스크린 이동 시 새로 고침]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## tab screen 이동할 때 새로 고침
---
- 옵션에`unmountOnBlur: true` 추가하면 해당 탭에 이동할 때 항상 새로 고침이 된다.

```javascript
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import React from 'react';
import * as screens from 'screens';


const Tab = createBottomTabNavigator();

export default function TabStack() {
  ...

  const options = {
    ...
    unmountOnBlur: true,
    ...
  }

  return (
    <Tab.Navigator>
        ...
      <Tab.Screen
        name="MyPage"
        component={screens.MyPage}
        options={options}
      />
    </Tab.Navigator>
  );
}

```

## tab or stack 스크린 이동할 때 lifecycle 실행하기
---
- 스크린을 이동 navigation.pop()(뒤로 가기)를 할 때 lifecycle 실행이 되지 않는데 lifecycle을 실행시키고 싶을 때 `useIsFocused`를 적용한다.

```javascript
import {useIsFocused, useNavigation} from '@react-navigation/native';

export default () => {
  const isFocused = useIsFocused();
  
  import React, {useEffect} from 'react';
  
  ...
  
  useEffect(() => {
    getData();
  }, [isFocused]);
  
  ...
}
```
