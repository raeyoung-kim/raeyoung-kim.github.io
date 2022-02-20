---
title: react-native 안드로이드 이미지가 깨져 보일 때(png)
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-11-18
categories: [React Native]
tags: [react-native 안드로이드 이미지 깨져 보일 때]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

아이콘 이미지를 넣을 때 png 파일 사용 중 ios에서는 깨짐 현상 없이 정상적으로 렌더 되었지만 안드로이드에서는 흐릿하게 이미지가 깨져 보였다.
구글링 결과 React Native에서 사용하고 있는 fresco에서 android 상 해상도 보다 큰 이미지를 자동으로 줄이는 기능이 있어 이미지가 깨져 보인다고 한다.

## 해결방법
---

1. jpg로 사용한다.

2. Fresco를 커스텀 한 버전을 사용한다.



- [react-native-fresco]([https://github.com/clytras/react-native-fresco#readme](https://github.com/clytras/react-native-fresco#readme))

3. react-native-fast-image 이미지를 사용한다.

- [FastImage](https://github.com/DylanVann/react-native-fast-image)

세 가지 방법 중 이미 프로젝트에 FastImage(react-native-fast-image)를 사용하고 있어 세 번 째 방법으로 문제를 해결했다.

 

```jsx
import FastImage from 'react-native-fast-image';
import * as style from './style'; // 스타일 컴포넌트 사용

const CategoryBar = () => {

  ...
  
  return (
    ...
    <style.Item>
      <FastImage
        /* ... */
        source={images.fLogoWhite}
        /* ... */
      />
    </style.Item>
  )

};

export default CategoryBar;

```
