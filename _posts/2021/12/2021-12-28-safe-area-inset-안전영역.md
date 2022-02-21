---
title: safe-area-inset 안전영역(Next.js)
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2021-12-28
categories: [CSS]
tags: [safe-area-inset]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

## Safe Area
---
아이폰 디스플레이에서 영역이 상/하단 라운드 영역을 포함하고 있는 경우 추가적으로 상단 영역은 카메라, 스피커 등의 센서가 있는 노치(notch) 영역 존재하므로 안정적으로 콘텐츠가 보일 수 있도록 Safe Area를 설정해 주어야 한다.


## 해결방법
---
- `_app.tsx` 파일에 뷰포트 메타태그에서 viewport-fit=cover를 추가한다.

```tsx
import Head from 'next/head';

function MyApp({ Component, pageProps }: AppProps) {
  return (
    <>
      <Head>
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover"
        />
      </Head>
    </>
  );
}
export default MyApp;
```

- `style.css` 해당 셀렉터에 아래와 같이 safe-area-inset를 적용한다. (constant 및 env 값을 둘 다 추가해야 IOS 구버전, 신버전 대응이 가능하다.)

```css
.safe-button {
  height: auto;
  padding: 20px 0px calc(constant(safe-area-inset-bottom) + 20px);
  padding: 20px 0px calc(env(safe-area-inset-bottom) + 20px);
}
```
