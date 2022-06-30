---
title: React 이미지 슬라이드(infinite loop) 구현
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-03-14
categories: [Frontend]
tags: [react 이미지 슬라이드 구현, Image Slider 구현]
math: true
mermaid: true
---

## To do

라이브러리 없이 아래 나열된 기능을 기준으로 이미지 슬라이드 구현을 해보았다.

- `<` (이전), `>`(다음) 버튼 클릭으로 슬라이드 이미지 전환
- 모바일에서 터치로 슬라이드 이미지 전환
- `무한 루프`로 마지막 이미지에서 다음 이미지로 터치 또는 클릭 시 자연스럽게 첫 번째 이미지로 이미지 전환, 첫 번째 이미지에서 이전 이미지로 터치 또는 클릭 시 마지막 이미지로 자연스럽게 이미지 전환

## 완성된 화면

![](https://images.velog.io/images/760kry/post/82e31df2-60c2-47ef-b888-495db3e16def/ezgif.com-gif-maker.gif)


## 버튼 클릭 시 이미지 슬라이드 전환 기능

> 아래와 같이 이미지 링크(string)를 배열로 받을 수 있는 컴포넌트로 구현하였다.

```tsx

import React, { FC } from 'react';
import { SwiperImage } from 'components';

const Sample: FC = () => {
  /* ... 생략 ...*/
  const data = ['이미지 url 링크 1', '이미지 url 링크 2'];

  return (
    <div>
      <SwiperImage data={data} />
    </div>
  );
};

export default Sample;
```

## 슬라이드 기능 

![](https://images.velog.io/images/760kry/post/103381d4-b0b9-4cf8-accc-261d85ec147e/Screen%20Shot%202022-03-13%20at%208.58.37%20PM.png)

- 위와 같이 보일 주황색 네모칸처럼 화면 크기를 정해둔 뒤 `overflow: hidden` 속성으로 주황색 네모칸을 벗어나는 이미지들은 숨김 처리한다.
- 이미지를 감싸고 있는 div에 `display: flex` 속성으로 가로로 이미지를 나열한다.
- 슬라이드 기능이 오른쪽으로 넘어가야 되기 때문에 x축이 마이너스 방향으로 이동되기 때문에 `transform: translateX(-${보일 이미지 인덱스}00%)`로 이동된다.
- `transition: all 0.4s ease-in-out` 속성과 주어 자연스러운 모션을 준다.


> 아래는 위의 설명을 구현한 소스이다.

```tsx
import classNames from 'classnames';
import React, { FC, useEffect, useRef, useState } from 'react';
import { IoIosArrowBack, IoIosArrowForward } from 'react-icons/io';

interface Props {
  data: string[];
}

const SwiperImage: FC<Props> = ({ data }) => {
  const ref = useRef<HTMLDivElement>(null);

  const [imageList, setImageList] = useState([
    data[data?.length - 1],
    ...data,
    data[0],
  ]);

  const [currentImgIndex, setCurrentImgIndex] = useState(1);

  const [style, setStyle] = useState({
    transform: `translateX(-${currentImgIndex}00%)`,
    transition: `all 0.4s ease-in-out`,
  });

  const nextSlide = () => {
    setCurrentImgIndex(currentImgIndex + 1);
    setStyle({
      transform: `translateX(-${currentImgIndex + 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  const prevSlide = () => {
    setCurrentImgIndex(currentImgIndex - 1);
    setStyle({
      transform: `translateX(-${currentImgIndex - 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  return (
    <div className="relative">
      <div
        className="overflow-hidden max-w-[480px] min-w-[280px] w-full bg-black"
      >
        <div ref={ref} style={style} className={`flex`}>
          {imageList?.map((el, i) => {
            return (
              <img
                key={i}
                src={el}
                className={'w-auto h-auto object-contain'}
              />
            );
          })}
        </div>
      </div>
      <div className="absolute w-full flex justify-between top-[50%]">
        <button className="text-white text-xl" onClick={prevSlide}>
          <IoIosArrowBack />
        </button>
        <button className="text-white text-xl" onClick={nextSlide}>
          <IoIosArrowForward />
        </button>
      </div>
      {/* ... 생략 ... */}
    </div>
  );
};

export default SwiperImage;

```

## 여기서 문제점!

여기서 첫 번째 이미지에서 사용자가 이전 버튼을 또 클릭하게 되면 마지막 이미지로 전환되지만 자연스럽지 못하게 휘리릭 마지막으로 넘어간다.


## 자연럽게 무한 루프 기능 추가하기

![](https://images.velog.io/images/760kry/post/e947b316-da92-4140-851a-f7b88c458cb1/Screen%20Shot%202022-03-13%20at%209.42.50%20PM.png)

- 위 화면과 같이 첫 번째 이미지(`이미지 1`) 왼쪽에 트릭을 주기 위한 가짜 마지막 이미지(`이미지 4`)를 두고 마지막 이미지(`이미지 4`) 오른쪽에도 트릭을 주기 위한 가짜 첫 번째 이미지(`이미지 1`)를 둔다.
- `이미지 4`에서 다음 버튼을 클릭할 경우 오른쪽에 있는 `trick 이미지 1`으로 넘어가는데, 여기서 `settimeout`으로 빠르게 `transition: 0ms`으로 효과 없이 사용자가 눈치채지 못하도록 진짜 `이미지 1`으로 이동한다.
- `이미지 1`에서 다음 버튼을 클릭할 경우 오른쪽에 있는 `trick 이미지 4`으로 넘어가는데, 여기서 `settimeout`으로 빠르게 `transition: 0ms`으로 효과 없이 사용자가 눈치채지 못하도록 진짜 `이미지 4`으로 이동한다.

![](https://images.velog.io/images/760kry/post/fbf29475-10f0-4500-bb62-6120bf9320b7/Screen%20Shot%202022-03-13%20at%209.56.31%20PM.png)

- 인덱스 `0`과 인덱스 `imageList[imageList.length - 1]`는 trick 이미지이기 때문에 가장 첫 번째 인덱스와 마지막 인덱스를 제외한 이미지들을 보여준다.

> 아래는 위 내용을 구현한 소스이다.

```tsx
import classNames from 'classnames';
import React, { FC, useEffect, useRef, useState } from 'react';
import { IoIosArrowBack, IoIosArrowForward } from 'react-icons/io';

interface Props {
  data: string[];
}

const SwiperImage: FC<Props> = ({ data }) => {
  const ref = useRef<HTMLDivElement>(null);

  const [imageList, setImageList] = useState([
    data[data?.length - 1],
    ...data,
    data[0],
  ]);

  const [currentImgIndex, setCurrentImgIndex] = useState(1);

  const [touch, setTouch] = useState({
    start: 0,
    end: 0,
  });

  const [style, setStyle] = useState({
    transform: `translateX(-${currentImgIndex}00%)`,
    transition: `all 0.4s ease-in-out`,
  });

  const nextSlide = () => {
    setCurrentImgIndex(currentImgIndex + 1);
    setStyle({
      transform: `translateX(-${currentImgIndex + 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  const prevSlide = () => {
    setCurrentImgIndex(currentImgIndex - 1);
    setStyle({
      transform: `translateX(-${currentImgIndex - 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  useEffect(() => {
    if (currentImgIndex === 0) {
      setCurrentImgIndex(imageList.length - 2);
      setTimeout(function () {
        setStyle({
          transform: `translateX(-${imageList.length - 2}00%)`,
          transition: '0ms',
        });
      }, 500);
    }

    if (currentImgIndex >= imageList?.length - 1) {
      setCurrentImgIndex(1);
      setTimeout(() => {
        setStyle({
          transform: `translateX(-${1}00%)`,
          transition: '0ms',
        });
      }, 500);
    }
  }, [currentImgIndex, imageList.length]);

  useEffect(() => {
    setStyle({
      transform: `translateX(-${1}00%)`,
      transition: '0ms',
    });
  }, [imageList]);

  return (
    <div className="relative">
      <div
        className="overflow-hidden max-w-[480px] min-w-[280px] w-full bg-black"
   
      >
        <div ref={ref} style={style} className={`flex`}>
          {imageList?.map((el, i) => {
            return (
              <img
                key={i}
                src={el}
                className={'w-auto h-auto object-contain'}
              />
            );
          })}
        </div>
      </div>
      <div className="absolute w-full flex justify-between top-[50%]">
        <button className="text-white text-xl" onClick={prevSlide}>
          <IoIosArrowBack />
        </button>
        <button className="text-white text-xl" onClick={nextSlide}>
          <IoIosArrowForward />
        </button>
      </div>
      {/* ... 생략 ... */}
    </div>
  );
};

export default SwiperImage;
```



## 모바일 터치 슬라이드 기능 구현

- `onTouchStart`
  - `e.touches[0].pageX` 처음 터치한 위치를 기억해 둔다.
- `onTouchMove`
  - 터치하면서 넘길 때 해당 위치까지 끌려오기 위해 `transition: 0ms`으로 이동할 위치로 이동한다

  ```javascript
    const current = ref.current.clientWidth * currentImgIndex;
    const result = -current + (e.targetTouches[0].pageX - touch.start);
    // 터치이동 시 이동되는 위치

    setStyle({
      transform: `translate3d(${result}px, 0px, 0px)`,
      transition: '0ms',
    });
  ```

- `onTouchEnd`
  - 처음 터치했을 때 위치보다 마지막 이동된 위치가 더 작을 경우 이전 슬라이드로, 반대인 경우 다음 슬라이드로 이동한다.

  ```javascript
    const end = e.changedTouches[0].pageX;
    if (touch.start > end) {
      nextSlide();
    } else {
      prevSlide();
    }
    setTouch({
      ...touch,
      end,
    });
  ```


### 완성된 구현 소스

```tsx
import classNames from 'classnames';
import React, { FC, useEffect, useRef, useState } from 'react';
import { IoIosArrowBack, IoIosArrowForward } from 'react-icons/io';

interface Props {
  data: string[];
}

const SwiperImage: FC<Props> = ({ data }) => {
  const ref = useRef<HTMLDivElement>(null);

  const [imageList] = useState([data[data?.length - 1], ...data, data[0]]);

  const [currentImgIndex, setCurrentImgIndex] = useState(1);

  const [touch, setTouch] = useState({
    start: 0,
    end: 0,
  });

  const [style, setStyle] = useState({
    transform: `translateX(-${currentImgIndex}00%)`,
    transition: `all 0.4s ease-in-out`,
  });

  const nextSlide = () => {
    setCurrentImgIndex(currentImgIndex + 1);
    setStyle({
      transform: `translateX(-${currentImgIndex + 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  const prevSlide = () => {
    setCurrentImgIndex(currentImgIndex - 1);
    setStyle({
      transform: `translateX(-${currentImgIndex - 1}00%)`,
      transition: `all 0.4s ease-in-out`,
    });
  };

  useEffect(() => {
    if (currentImgIndex === 0) {
      setCurrentImgIndex(imageList.length - 2);
      setTimeout(function () {
        setStyle({
          transform: `translateX(-${imageList.length - 2}00%)`,
          transition: '0ms',
        });
      }, 500);
    }

    if (currentImgIndex >= imageList?.length - 1) {
      setCurrentImgIndex(1);
      setTimeout(() => {
        setStyle({
          transform: `translateX(-${1}00%)`,
          transition: '0ms',
        });
      }, 500);
    }
  }, [currentImgIndex, imageList.length]);

  return (
    <div className="relative">
      <div
        className="overflow-hidden max-w-[480px] min-w-[280px] w-full bg-black"
        onTouchStart={(e) => {
          setTouch({
            ...touch,
            start: e.touches[0].pageX,
          });
        }}
        onTouchMove={(e) => {
          if (ref?.current) {
            const current = ref.current.clientWidth * currentImgIndex;
            const result = -current + (e.targetTouches[0].pageX - touch.start);
            setStyle({
              transform: `translate3d(${result}px, 0px, 0px)`,
              transition: '0ms',
            });
          }
        }}
        onTouchEnd={(e) => {
          const end = e.changedTouches[0].pageX;
          if (touch.start > end) {
            nextSlide();
          } else {
            prevSlide();
          }
          setTouch({
            ...touch,
            end,
          });
        }}
      >
        <div ref={ref} style={style} className={`flex`}>
          {imageList?.map((el, i) => {
            return (
              <img
                key={i}
                src={el}
                className={'w-auto h-auto object-contain'}
              />
            );
          })}
        </div>
      </div>
      <div className="absolute w-full flex justify-between top-[50%]">
        <button className="text-white text-xl" onClick={prevSlide}>
          <IoIosArrowBack />
        </button>
        <button className="text-white text-xl" onClick={nextSlide}>
          <IoIosArrowForward />
        </button>
      </div>
      <div className="text-gray-500 mt-4 text-center flex justify-center">
        {data.map((el, i) => {
          return (
            <div
              key={i}
              className={classNames(
                'bg-gray-200 h-[6px] w-[6px] mr-1 rounded',
                {
                  'bg-rose-200': i + 1 === currentImgIndex,
                }
              )}
            />
          );
        })}
      </div>
    </div>
  );
};

export default SwiperImage;
```
