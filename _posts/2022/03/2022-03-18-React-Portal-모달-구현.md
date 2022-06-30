---
title: React Portal을 이용해 모달 구현
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-03-18
categories: [Frontend]
tags: [react portal 모달]
math: true
mermaid: true
---

## Portal 이란? 
--- 
Portal 이란 부모 컴포넌트를 벗어나 DOM 계층 구조 바깥에 있는 DOM 노드로 자식을 렌더링 하는 방법이다.

DOM은 트리구조로 부모와 자식 관계를 가지면서 렌더링 되는데 엘리먼트가 위치한 부모와 자식 관계의 구조 따라 z-index가 먹히지 않는 경우가 있다.

아래와 같이 `1-modal`이 fixed에 z-index를 높였을 때 `2-parent` 엘리먼트가 z-index 우선순위가 높게 되어 렌더링 된다.

```html
<div id='1-parent'>
  <div id='1-modal'></div>
</div>
<div id='2-parent'>
  <div id='2-child'></div>
</div>
```
이렇게 트리 구조 사이에서 컴포넌트를 구현하다 보면 스타일 속성에 제약을 받는 경우가 있는데 Portal을 이용해 계층 구조 바깥 독립적인 위치에서 렌더링 하면 편리하다.

## Portal 모달 구현하기
---
### install
```bash
npm i react-dom

npm i npm i --save-dev @types/react-dom
```

### `/components/Portal/index.tsx`
- children으로 넣어주는 Portal을 만들어 준다.

```jsx
import { useEffect, useMemo } from 'react';
import type { FC, AriaAttributes } from 'react';
import { createPortal } from 'react-dom';

export interface Props extends AriaAttributes {
  style?: Partial<CSSStyleDeclaration>;
}

const Portal: FC<Props> = ({ children, style }) => {
  const element = useMemo(() => document.createElement('div'), []);

  useEffect(() => {
    if (style) {
      for (const property in style) {
        const value = style[property];
        if (value) element.style[property] = value;
      }
      if (style.position === 'absolute')
        document.body.style.position = 'relative';
    }

    document.body.appendChild(element);
    return () => {
      document.body.removeChild(element);
      if (document.body.style.position) document.body.removeAttribute('style');
    };
  }, [element, style]);

  return createPortal(children, element);
};

export default Portal;
```

### `/components/ImageSliderModal/index.tsx`
- Portal에 children을 넣어 모달을 구현한다.

```jsx
import { SwiperImage, Portal } from 'components';
import React, { FC } from 'react';

interface Props {
  data: string[];
  selectedImgIndex: number;
  onClose: () => void;
}

const ImageSliderModal: FC<Props> = ({ data, onClose, selectedImgIndex }) => {
  return (
    <Portal>
      <div className="fixed inset-0 bg-black flex justify-center items-center">
        <div className="w-full h-full flex items-center justify-center flex-col">
          <button className="text-white mb-5 p-2" onClick={onClose}>
            닫기
          </button>
          <SwiperImage data={data} initialImgIndex={selectedImgIndex + 1} />
        </div>
      </div>
    </Portal>
  );
};

export default ImageSliderModal;
```
