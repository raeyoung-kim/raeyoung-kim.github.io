---
title: Dockerfile 최적화 Multi-Stage Build(멀티스테이지 빌드)
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2023-07-17
categories: [Docker Study]
tags: [docker multi stage, docker 멀티스테이지 빌드, dockerfile 최적화]
math: true
mermaid: true
---

## Multi-Stage Build(멀티 스테이지 빌드)란?

-   **단일 도커 파일**에서 **여러 단계의 빌드를 수행하는 방법**입니다.

### 사용하는 이유

-   빌드 도구와 런타임 환경을 분리하고 실행에 필요한 **최소한의 구성만 포함하여 이미지 크기를 최소화**할 수 있습니다.
-   빌드 도구와 관련된 정보들을 외부에 노출시키지 않아 **보안을 강화**할 수 있습니다.
-   중복된 작업을 피하고 이전 단계의 **캐시를 활용하기 때문에 빌드 속도가 향상**됩니다.

## Dockerfile Example

아래는 **Create React App**을 기반으로 React 애플리케이션을 빌드하고  
**Nginx**를 사용하여 정적 파일을 서빙하는 **Dockerfile을 작성한 예시**입니다.

```Dockerfile
# node:18-alpine 베이스 이미지를 사용하여 builder 스테이지를 시작합니다. 
# alpine 이미지는 경량화된 이미지로 크기가 작아 네트워크를 절약할 수 있습니다.
FROM node:18-alpine AS builder

# 작업 디렉토리를 /app으로 설정합니다.
WORKDIR /app

# 패키지 매니저의 lock 파일과 package.json을 복사하여 종속성을 설치하기 전에 캐시를 활용할 수 있도록 합니다. 
# 캐시된 종속성은 변경이 없을 경우 재설치되지 않고 이전에 캐시된 결과물을 사용하여 이미지 크기를 줄입니다.
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./

# 해당하는 lock 파일이 있는지 확인하고, 해당 lock 파일에 맞는 도구를 사용합니다.
# 종속성을 설치하고 빌드를 진행하는 단계에서 yarn, npm, pnpm 등 빌드 도구를 선택적으로 사용합니다. 이미 설치된 종속성과 캐시를 활용하여 이전에 수행한 빌드 작업을 스킵하고, 변경된 부분만 다시 빌드 하여 이미지의 크기를 최소화합니다.
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi

#소스 코드를 복사하여 이미지에 포함시킵니다.(dockerignore 목록 제외)
COPY . /app

RUN npm run build

# 새로운 빌드 단계를 시작합니다. 이 단계에서는 Nginx를 사용하여 애플리케이션을 배포합니다.
FROM nginx

# 이전 builder 스테이지에서 생성된 빌드 결과물을 Nginx 컨테이너의 /usr/share/nginx/html 디렉토리로 복사합니다. 
# 이를 통해 Nginx는 정적 파일을 제공할 수 있게 됩니다.
COPY --from=builder /app/build /usr/share/nginx/html

# 호스트와 연결할 포트 번호인 3000번을 노출합니다.
EXPOSE 3000

# 컨테이너가 시작되었을 때 Nginx를 실행하는 명령어를 정의합니다. Nginx는 데몬 모드로 실행됩니다.
CMD ["nginx", "-g", "daemon off;"]
```

아래는 **Next** 프로젝트를 **멀티스테이지 빌드를 활용**하여 **Dockerfile을 작성한 예시**입니다. [참고](https://github.com/vercel/next.js/blob/canary/examples/with-docker-multi-env/docker/production/Dockerfile)

```Dockerfile 
# alpine 경량화된 이미지를 베이스 이미지로 base 스테이지를 시작합니다.
FROM node:18-alpine AS base

# 이전 단계인 base 단계를 기반으로 새로운 단계 deps를 정의합니다.
FROM base AS deps

# glibc 및 friends 대신 musl libc를 사용하므로 특정 소프트웨어는 
libc 요구 사항의 깊이에 따라 문제가 발생할 수 있기 때문에 해당 패키지를 추가합니다.(업데이트 되었는지 생략되어도 문제가 없었음)
RUN apk add --no-cache libc6-compat

# 작업 디렉토리를 /app으로 설정합니다.
WORKDIR /app

# 패키지 매니저의 lock 파일과 package.json을 복사하여 종속성을 설치하기 전에 캐시를 활용할 수 있도록 합니다. 
# 캐시된 종속성은 변경이 없을 경우 재설치되지 않고 이전에 캐시된 결과물을 사용하여 이미지 크기를 줄입니다.
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./

# yarn.lock 파일이 존재하는 경우 yarn --frozen-lockfile을 실행하여 종속성을 설치합니다.
# package-lock.json 파일이 존재하는 경우 npm ci를 실행하여 종속성을 설치합니다.
# pnpm-lock.yaml 파일이 존재하는 경우 yarn global add pnpm 명령어를 사용하여 pnpm을 전역으로 설치한 후 pnpm i를 실행하여 종속성을 설치합니다.
# 어떤 락 파일도 존재하지 않는 경우 "Lockfile not found." 메시지를 출력하고 프로세스를 종료합니다.
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i; \
  else echo "Lockfile not found." && exit 1; \
  fi


# 이전 단계인 base 단계를 기반으로 builder라는 새로운 단계를 정의합니다.
FROM base AS builder

# 작업 디렉토리를 /app으로 설정합니다.
WORKDIR /app

# 이전 단계인 deps 단계에서 생성된 node_modules 디렉토리를 현재 작업 디렉토리의 ./node_modules로 복사합니다.
COPY --from=deps /app/node_modules ./node_modules

# 현재 디렉토리의 모든 파일과 폴더를 작업 디렉토리로 복사합니다.
COPY . .

# yarn build 명령어를 실행하여 Next.js 애플리케이션을 빌드 합니다.
RUN yarn build

# 이전 단계인 base 단계를 기반으로 새로운 단계 runner를 정의합니다.
FROM base AS runner

# 작업 디렉토리를 /app으로 설정합니다.
WORKDIR /app

# 환경 변수 NODE_ENV를 production으로 설정합니다.
ENV NODE_ENV=production

# 시스템 권한 설정 및 중복되지 않는 uid 설정하여 유저 및 그룹 추가합니다.

# addgroup 명령어를 사용하여 nodejs 그룹을 추가합니다.
RUN addgroup --system --gid 1001 nodejs

# adduser 명령어를 사용하여 nextjs 사용자를 추가합니다.
RUN adduser --system --uid 1001 nextjs

# 이전 단계인 builder 단계에서 생성된 public 디렉토리를 현재 작업 디렉토리의 ./public로 복사합니다.
COPY --from=builder /app/public ./public

# 이전 단계인 builder 단계에서 생성된 .next/standalone 디렉토리를 현재 작업 디렉토리로 복사합니다.
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./

# 이전 단계인 builder 단계에서 생성된 .next/static 디렉토리를 ./.next/static로 복사합니다.
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# USER 명령어를 사용하여 컨테이너에서 실행할 사용자를 nextjs로 설정합니다.
# 유저 미설정 시 Root 권한으로 설정되어 보안에 이슈가 발생할 수 있기 때문에 미리 정의한 nextjs 유저를 등록합니다.
USER nextjs

# 외부에서 접근할 포트 설정합니다.
EXPOSE 3000

# 도커 이미지 빌드 후 실행시 접근가능한 환경변수
# server.js 에서 아래 코드가 설정되어있기 때문에 설정해준다.
# const port = process.env.PORT || 3000;
ENV PORT 3000

# standalone 옵션을 통해서 생성된 server.js를 실행시킵니다.
CMD ["node", "server.js"]
```

-   멀티스테이지 빌드를 사용하여 이전 단계에서 생성된 이미지와 캐시를 활용할 수 있습니다.  
    변경된 부분만 다시 빌드하고, 이전 단계에서 생성된 이미지와 **캐시를 재사용함으로써 중복 작업을 최소화하고 빌드 속도를 향상**합니다.
-   빌드 단계에서 생성된 결과물 중 런타임 환경에서 필요한 **최소한의 구성만을 선택하여 최종 이미지로 생성**합니다. 이를 통해 **이미지의 크기를 줄여 이미지 다운로드 및 배포 시간을 단축**합니다.
