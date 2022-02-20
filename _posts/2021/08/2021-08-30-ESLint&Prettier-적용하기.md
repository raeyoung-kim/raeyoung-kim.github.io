---
title: ESLint & Prettier 적용하기
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2021-08-30
categories: [Webpack]
tags: [ESLint & Prettier, ESLint & Prettier 적용하기]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

---
ESLint는 JavaScript, JSX의 정적 분석 도구로 코드를 분석해 문법적인 오류를 찾아주고 일관된 코드 스타일로 작성하도록 도와준다. ESLint는 스타일 가이드를 좀 더 편리하게 적용할 수 있다.(예: Airbnb Style Guide, Google Style Guide)

- 포맷팅 : 일관된 코드 스타일을 유지할 수 있다. (들여 쓰기, 코드 라인 너비 규칙 등등)
- 코드 품질: 잠재적 오류나 버그 예방(사용하지 않는 변수 등)

### install

```bash
npm i eslint

// 코드 검증 및 수정
npx eslint [파일]
npx eslint [파일] --fix
```

### 명령어로 ESlint 세팅

```bash
npx eslint --init
```

위와 같이 커맨드 창의 입력하면 대화 형식으로 설정 파일을 만들 수 있다.
해당 설정에 맞게 세팅하고 필요한 라이브러리들을 추가 설치하면

아래와 같이 루트 디텍토리에 `.eslintrc.js` 파일이 만들어진다.


```javascript
  module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'plugin:react/recommended', // [https://eslint.org/docs/rules/](https://eslint.org/docs/rules/) 에 체크되어있는 것이 전부 세팅되어있다.
    'airbnb',
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    sourceType: 'module',
  },
  plugins: [
    'react',
  ],
  rules: {
  },
};
```

> - `"extends": "eslint:recommended"` 는 [https://eslint.org/docs/rules/](https://eslint.org/docs/rules/) 에 체크되어 있는 것이 전부 세팅되어 있다.
- 링크를 확인해보면 자동으로 수정할 수 있는 것은 렌치 표시가 되어있다.


## Prettier
---
- 프리티어는 es-lint에서 고쳐주지 않는 것까지 고쳐준다.

```bash
npm i prettier
npm i eslint-config-prettier

// 프리티어에 있는 규칙을 eslint에 넣기
npm i -D eslint-plugin-prettier

// 코드 수정
npx prettier [파일] --wirte
```

## ESLint & Prettier
---
```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    "eslint:recommended", // 미리 설정된 규칙 세트를 사용한다.
    "plugin:react/recommended",
    "airbnb",
    "plugin:prettier/recommended",
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    sourceType: "module",
  },
  plugins: ["react", "prettier"],
  rules: {
    "prettier/prettier": "error",
    ...
  },
};

```


## vs-code
---
### install

![](https://images.velog.io/images/760kry/post/ded18940-4369-422f-a86b-2a39bb9d4a33/Screen%20Shot%202021-08-30%20at%202.22.49%20AM.png)

![](https://images.velog.io/images/760kry/post/7168e996-a6ac-452e-bdb0-a049318d8014/Screen%20Shot%202021-08-30%20at%202.23.03%20AM.png)



### setting

```json
{
    "workbench.colorTheme": "Default Dark+",
    "terminal.integrated.defaultProfile.osx": "zsh",
    "editor.tabSize": 2,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    },
    "js/ts.implicitProjectConfig.strictNullChecks": true,
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
}
```
