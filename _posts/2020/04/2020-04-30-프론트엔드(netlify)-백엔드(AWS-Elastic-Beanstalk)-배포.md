---
title: 프론트엔드(netlify) 백엔드(AWS Elastic Beanstalk) 배포
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-04-30
categories: [CS]
tags: [netlify, AWS Elastic Beanstalk]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

처음으로 풀스택으로 프로젝트를 시작했다. 
혼자 해결해 나가는 부분에서 삽질을 많이 했다. 아직도 미흡한 점이 많지만 일단 배포를 먼저 해보기로 결정했다. 고치고 싶은 부분이라면 끝도 없이 많기에 .. 
프론트엔드는 react와 redux를 사용해서 작업했고 netlify 배포 서비스를 사용했다.
백엔드는 node.js express 와 mongoDB를 사용해서 작업했고 AWS Elastic Beanstalk 를 사용해서 배포하였다.
배포하는 과정에서 주의할 점을 정리해 보았다.

## 프론트엔드 배포(netlify)
---
netlify는 프론트엔드 스택으로만 구성된 정적 애플리케이션을 배포하는 용도로 최적화된 서비스다.
배포하는 방법은 정말 간단하다. 프로젝트를 github 계정에 연결하고 레파지토리를 선택한 후 배포할 브랜치를 선택해 주면 된다.
또 프로젝트를 수정하고 커밋 한 다음 다시 푸시 하면 자동으로 build 상태가 되고
`trigger deploy` 버튼만 눌러주면 된다.

**여기서 주의할 점은 `.env` 파일에 작성했던 환경 변수를 모두 넣어 주어야 한다! 또 백엔드를 배포했다면 개발과정에서 사용했던 ~~(예:localhost:5000)~~ 이 부분도 모두 백엔드 server url로 바꿔준다.**



## 백엔드 배포(AWS Elastic Beanstalk)
---
EC2를 조금 더 쉽게 배포할 수 있다는 Elastic Beanstalk을 사용해서 배포했다. 기본적인 설정이 되어있기 때문에 조금 더 간편하다고 한다.

프론트엔드를 netlify로 배포하면 https로 기본 배포 되고 EB에서는 http를 기본으로 구성하기 때문에 `Mixed Content 에러`가 발생한다. http -> https 로 요청은 가능해도 https -> http 요청은 안된다. 여기서 https를 사용하지 않는 사이트로 요청을 보낸다는 것은 비 암호화된 구간으로 데이터를 전송시켜서 정보 유출의 우려가 있기 때문이다. 프론트에서 정보를 수신하기 위해서는 EB로 배포된 서버를 http에서 https로 바꿔야 하는데 그 과정에서 굉장히 애를 먹었다.

### 기본 설치 과정
---
- 콘솔에 로그인한 뒤 지역을 설정한다 (예: 서울)
- 새 애플리케이션 생성을 한다.
- 기본 구성에 플랫폼을 node.js로 설정한다.
#### brew를 이용해서 aws cli 설치 (mac os)
```bash
brew install awscli
aws --version (aws cli 설치 확인)
```
#### brew를 이용해서 EB cli 설치 (mac os)
```bash
 brew install awsebcli
 eb --version (eb cli 설치 확인)
```
- `내 보안 자격 증명` 에 들어간다.
- `액세스 키(액세스 키 ID 및 비밀 액세스 키)`를 생성하고 파일을 다운로드한다. **(이 정보는 절대 github이나 클라우드 등 어디에도 올라가서는 안된다)**
- 최상위 루트에서 `.aws 폴더`를 만들고 `credentials 파일` 안에 `액세스 키(액세스 키 ID 및 비밀 액세스 키)`를 아래와 같이 작성한 뒤 저장한다.
**`~/.aws/credentials` **
```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```
- eb init 으로 초기화를 하고 지역을 선택 하면 만들어놓은 애플리케이션 중 하나를 선택한다. 다음으로 `do you wish to continue with CodeCommit? (y/n)` 가 뜨는데 이부분은 잘 모르기 때문에 `n`을 하고 넘어가고 `eb deploy` 명령어로 배포를 한다.
- 프로젝트 폴더를 확인해보면 .elasticbeenstalk 이라는 폴더가 생겨있다.
```
eb init
(지역선택: 서울) 10
(애플리케이션 선택: ktx-gogo) 1
do you wish to continue with CodeCommit? (y/n) n
eb deploy
```


### 생성한 뒤 상태가 초록색으로 떠야 하는데 빨간색으로 뜬다면?? (degraed) 
---
- 구성 -> 소프트웨어를 편집한다.
- node 명령을 작성한다.(예: npm start (개발과정에서 nodemon으로 되어있는 경우 에러가 발생할 수 있으니 node로 명령어가 되어있는지 확인한다.))
- 환경 속성에 환경 변수를 작성한다.(.env 파일 안의 환경 변수들을 작성한다.)


### EB https
---
클라이언트 https로 요청이 들어왔을 때 https로 요청을 받아 http로 통신한 데이터를 https로 응답해 주는 방법이다.
- 기본적으로 로드 밸런스 편집 기능이 없는 상태로 구성되기 때문에 로드 밸런스를 수정하기 위해 용량을 편집한다.
   - 환경 유형을 로드밸런스로 선택한 뒤 적용한다.
   - 인스턴스를 최대 2개 이상으로 설정한다.
 - 로드밸런스를 편집한다.
   - 리스너를 추가한다.
   - 프로토콜 https 포트는 443 인스턴트 포트는 80으로 설정한다.
   - ssl 인증서를 선택한 뒤 적용한다. ([ssl인증서 발급](https://jojoldu.tistory.com/434))
   - DNS 관리에 CNAME ssl 인증서에서 받은 키와 값을 넣어준다.
