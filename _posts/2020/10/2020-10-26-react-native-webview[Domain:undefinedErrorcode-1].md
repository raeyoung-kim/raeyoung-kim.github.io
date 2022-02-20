---
title: react-native-webview(Domain undefined Error code -1)
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-10-26
categories: [React Native, react-native-webview]
tags: [react native domain error]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

Android 버전이 tagetSdkVersion 28 이상일 경우 부터는 WebView에 일반적인 텍스트로 `http://` URL 접근이 막혔다. 

## 해결방법
---

AndroidManifest.xml 에서 application 의 usesClearTextTraffic 속성을 true로 바꿔주어 텍스트 URL 을 무조건 허용하게했다.

```xml
<manifest ...>
    ...
    <application
        ...
        android:usesCleartextTraffic="true">
```
