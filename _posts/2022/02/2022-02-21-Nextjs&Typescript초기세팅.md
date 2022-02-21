---
title: Next.js & Typescript 프로젝트 초기 기본 세팅
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-02-21
categories: [Next.js]
tags: [Next.js & Typescript 세팅,]
math: true
mermaid: true
---

## Next.js & Typescript install
---
```bash
npx create-next-app --ts <project-name>
```

```bash
touch tsconfig.json
```

```bash
npm run dev
```

**에러 나면 아래와 같이 설치 후 다시 실행**

```bash
npm install --save-dev @types/node
```

- package.json

```json
{
  /*...*/
  "scripts": {
    "dev": "next dev",
    "build": "next build && next export",
    "start": "next start",
    "lint": "next lint"
  },
  /*...*/
}
```

## ESLint 적용하기
---
```bash
npm i eslint

npx eslint --init

npm i prettier eslint-config-prettier

npm i -D eslint-plugin-prettier
```

- `.eslintrc.js`

```jsx
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended',
    'next/core-web-vitals',
    // "airbnb",

  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    sourceType: 'module',
  },
  plugins: ['react', '@typescript-eslint'],
  rules: {
    'prettier/prettier': ['error', { singleQuote: true, parser: 'flow' }],
    'react/jsx-filename-extension': [
      1,
      { extensions: ['.js', '.jsx', '.tsx', '.ts'] },
    ],
  },
};
```

## BaseUrl 설정
---
baseUrl 옵션을 사용하여 기본 경로를 지정해주면, 상대경로가 아닌 절대경로로 파일을 import 할 수 있다. base 가 되는 경로를 직접 설정함으로써, 패키지 depth 가 깊어질수록 길어지는 import 문을 짧게 나타낼 수 있다.

- `tsconfig.json`

```json
{
  "compilerOptions": {
    /*...*/
    "baseUrl": ".",
    /*...*/
  },
  /*...*/
}
```
