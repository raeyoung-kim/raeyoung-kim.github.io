---
title: react-native의 간단한 기본 원리 및 개발 환경
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-09-13
categories: [React Native]
tags: [react-native]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---


- 리액트 네이티브는 페이스북에서 만든 오픈소스 모바일 응용 프로그램으로 네이티브 앱 개발을 위한 자바스크립트 프레임워크이다.
- 크로스 플랫폼으로 하나의 코드 개발로 ios와 android에서 동일하게 동작시킬 수 있다.
- 자바스크립트를 기본 언어로 쓰기 때문에 자바나 코틀린 언어에 의존하는 안드로이드 네이티브 앱 혹은 swift 언어에 의존하는 ios 네이티브 앱 개발 보다 진입장벽이 낮고 쉽다는 장점을 가지고 있다.

## react native 기본 원리
---

- ios 경우 object-c 혹은 swift 코드를 ios 플랫폼에 타켓팅해주는 컴파일러가 존재하고 android의 경우 자바나 코틀린을 android 플랫폼에 타켓팅해주는 컴파일러가 존재한다.

![](https://images.velog.io/images/760kry/post/5e17828e-edff-48a6-ab1c-627cdbcd8e2b/KakaoTalk_Photo_2020-09-13-17-57-25.jpeg)

- 자바스크립트로 짜여진 리액트 네이티브의 JS bundle 은 JS thread에 의해 실행한다.
- 각 플랫폼의 앱 실행은 native thread에 의해 실행되는데 JS thread와 Native thread와 직접적으로 커뮤니케이션을 할 수 없는데 중간에 react-native에서 제공되는 brideg에 의해 상호작용을 하게 된다.

## Expo CLI vs React Native CLI
---

### Expo CLI

- react-native로 앱을 제작할 때 자주 사용하는 네이티브 기능들을 패키지로 묶어서 제공한다.
그래서 개발 환경 구축과 실제 개발이 쉽고 편하다. 하지만 OS Layer와 직접 상호 작용이 불가능하다. (Java, Kotlin, obj-c, swift로 추가 코딩이 불가하다.) expo에서 제공해 주는 모듈만 가져다 사용할 수 있기 때문에 개발 관점에서의 자유도가 낮다.

### React Native CLI

- 초기 개발 환경 구축 및 실제 앱 개발 시 설정 시간이 다소 걸리지만 OS Layer와 직접 상호작용이 가능하다.  (Java, Kotlin, obj-c, swift로 추가 코딩이 가능하다.) Native 기능에 접근이 가능하기 때문에 원하는 언어로 추가 코딩이 가능하기 때문에 필요한 기능이 있는 경우 모듈을 직접 만들어서 사용할 수 있어 개발 관점에서의 자유도가 높다.
