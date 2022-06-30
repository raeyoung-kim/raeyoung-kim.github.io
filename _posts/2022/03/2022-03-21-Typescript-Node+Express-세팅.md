---
title: typescript + express.js + node.js 세팅
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-03-21
categories: [Backend]
tags: [typescript + express.js + node.js 세팅, typescript node express]
math: true
mermaid: true
---

## install
---
```bash
npm init
npm i express morgan express-async-errors typescipt ts-node
```
- 기본적으로 typescript, express.js, node.js를 사용하기 위해 필요한 라이브러리를 설치한다.

```bash
npm i nodemon concurrently @types/node @types/express @types/morgan --save-dev
```
- typescript를 사용하기 위해 `@types/node`,`@types/express`, `@types/morgan`을 추가로 설치한다.

- `nodemon`을 설치해 서버 코드를 변경할 때마다, 서버를 자동으로 재시작한다.

- `nodemon`과 타입스크립트에서 제공하는 `tsc -w`를 같이 사용하기 위해 `concurrently`를 설치한다.


## **tsconfig.json 설정**
---
```bash
tsc --init
```
- 위와 같이 명령어를 사용하면 기본 `tsconfig.json` 파일이 생성된다.

```jsx
{
  "compilerOptions": {
    "target": "es2016",                                  /
    "module": "commonjs", 
    "sourceMap": true,                               
    "outDir": "dist",                                   
    "esModuleInterop": true,                             
    "strict": true,                                      
    "skipLibCheck": true                                 
  }
}
```
- `"outDir": "dist"`로 설정하여 자바스크립트로 컴파일 하는 경로를 지정해준다. 이렇게하면 타입스크립트로 코드로 작성한 파일이 자바스크립트로 컴파일이되고 dist 폴더에 들어간다. dist 폴더안에 자바스크립트로 변환된 파일들이 노드에 반영이 되어 실행되게 된다.


## .gitignore
---
- `.gitignore` 파일 생성 후 위와 같이 작성해 필요하지 않는 파일 들을 git에서 제외한다.

```jsx
.DS_Store
/dist
/node_modules
```

## 자동으로 재실행 시키기

---

### package.json
- `start` 부분에 아래와 같이 작성하면 `concurrently`를 사용하여 `nodemon`과 `tsc -w`를 같이 사용하여 자동으로 타입스크립트 코드를 컴파일하고 노드에 반영이 되어 실행된다.

```jsx
{
  /* 생략 */
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "concurrently \"tsc -w\" \"nodemon dist/app\""
  },
  /* 생략 */
}
```

## ESLint & Prettier 적용
---
- 코드 품질 및 일관성을 위해 eslint 설정을 해주었다.

```bash
npm i eslint prettier eslint-config-prettier
npm i eslint-plugin-prettier --save-dev
```

### .eslint.js
```javascript
module.exports = {
  /* 생략 */
  plugins: ["@typescript-eslint", "prettier"],
  rules: {
    "prettier/prettier": "error",
  },
};
```
