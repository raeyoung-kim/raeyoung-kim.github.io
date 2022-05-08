---
title: AWS CodePipeline을 이용한 배포
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-05-08
categories: [AWS]
tags: [codepipeline, codepipeline node ts]
math: true
mermaid: true
---


## AWS CodePipeline으로 배포하기

사이드 프로젝트를 **CodePipeline**을 이용해서 배포해 보았다.
그 과정을 아래와 같이 정리해 보았다.

CodePipeline으로 배포할 때 `Source -> Deploy`으로 바로 배포할 수 있지만 
프로젝트에 `Typescript`를 사용했기 때문에 `Source -> Build -> Deploy`로 `Build`부분을 추가했다.

![](https://velog.velcdn.com/images/760kry/post/f1535574-30fd-4c6e-bbba-8515258095f3/image.png)



## 배포 과정
### Elastic Beanstalk(EB)
**CodePipeline**은 배포 과정을 자동으로 진행시켜주는 것이고 결국은 EC2 인스턴스에 배포가 되는 것으로 먼저 **Elastic Beanstalk**을 이용해서 인스턴스를 생성한다.

- aws 콘솔 로그인 후 Elastic Beanstalk에서 `새 환경 생성`을 클릭한다.
![](https://velog.velcdn.com/images/760kry/post/8b24304d-77dc-477f-a879-f6c79adef321/image.png)

- `웹 서버 환경` 체크 후 선택 클릭
![](https://velog.velcdn.com/images/760kry/post/1f901bc9-a90c-48c6-94be-f4c1ca7720cf/image.png)

- 배포할 애플리케이션 이름을 적는다.
![](https://velog.velcdn.com/images/760kry/post/ce5f7658-96b4-40db-8fcd-577028f43a8d/image.png)

- 애플리케이션 이름을 적으면 환경 이름이 자동으로 만들어진다.(환경 이름은 나중에 변경할 수 있다)
![](https://velog.velcdn.com/images/760kry/post/73368255-3eed-4690-8176-fc048cc6f222/image.png)

- 플랫폼에서 `Node.js` 선택한다.
![](https://velog.velcdn.com/images/760kry/post/2794c8a3-1413-4a0c-8e79-54a55c36d091/image.png)

- `CodePipeline`과 연결시킬 거기 때문에 우선 `샘플 애플리케이션` 선택 후 `환경 생성`을 한다.
![](https://velog.velcdn.com/images/760kry/post/35b75b43-eb78-4a7d-8123-b7eaa29a58bd/image.png)

- 아래와 같이 환경이 생성된다
![](https://velog.velcdn.com/images/760kry/post/f348e7e9-ba5e-4d66-83e8-21c6943262cc/image.png)

- 위 밑줄 친 주소로 들어가면 `샘플 애플리케이션`으로 만들어진 아래와 같은 화면이 나온다.
![](https://velog.velcdn.com/images/760kry/post/7e756d8e-8a97-4b74-aadd-02d6a758c538/image.png)

- `Elastic Beanstalk > 구성 > 편집 > 소프트웨어 > 편집`을 클릭한다
![](https://velog.velcdn.com/images/760kry/post/aa47a587-75b6-4d12-b707-3e089ce31382/image.png)

- `.env`파일에서 관리하던 환경 변수를 입력한다.
![](https://velog.velcdn.com/images/760kry/post/c6aa4a82-a8a0-4541-9d46-9035aca11f1b/image.png)



### CodePipeline

- 파이프라인을 생성한다.
![](https://velog.velcdn.com/images/760kry/post/d635149b-e64b-445f-81b4-1f100bd003da/image.png)

- 파이프라인 이름을 작성하고 다음으로 넘어간다.
![](https://velog.velcdn.com/images/760kry/post/00ba8d7c-901b-41df-8d8a-e9c9ace7f1d8/image.png)

- `GitHub(버전 1)`을 선택 후 `GitHub에 연결`한다.
![](https://velog.velcdn.com/images/760kry/post/dca8498f-bf6a-4d82-98a9-9ae1d8f9c480/image.png)

- `GitHub에 연결`이 되면 연결된 계정의 repository를 확인할 수 있다. 해당 repository를 선택하고 배포 브랜치를 선택한다.
![](https://velog.velcdn.com/images/760kry/post/364d92db-4423-4765-892f-06da54da259d/image.png)

- 빌드 스테이지를 추가한다.
	- 빌드 공급자를 `AWS CodeBuild`를 선택한다. 
    - 가까운 리전을 선택한다
    - `.env`파일에서 관리하던 환경 변수를 입력한다.
![](https://velog.velcdn.com/images/760kry/post/5a97d1b8-7410-4d97-8b75-d01603851497/image.png)

- 배포 공급자에서 `AWS Elastic Beanstalk`을 선택하고 만들어 두었던 EB 애플리케이션을 선택하고 리전 및 환경 이름도 선택한다.
![](https://velog.velcdn.com/images/760kry/post/39c5a918-5a61-4fa5-8b7c-0b86088e0704/image.png)

- 마지막 검토에서 확인 후 `파이프라인 생성`을 클릭한다.

- `CodeBuild`에서 `Buildspec`을 수정한다. 아래 `편집 > Buildspac` or`빌드 세부 정보`에서 수정할 수 있다.
	- `편집 > Buildspac` 
![](https://velog.velcdn.com/images/760kry/post/eebadc08-fa69-4d73-a8e4-a0487b188ca5/image.png)
	- `빌드 세부 정보`
![](https://velog.velcdn.com/images/760kry/post/b9c38aeb-fe95-4cec-b78a-62fc8ad4d5a9/image.png)
	- 아래와 같이 수정한다.
![](https://velog.velcdn.com/images/760kry/post/dcca03cd-c40f-40fb-b66d-595c4f388aa0/image.png)
![](https://velog.velcdn.com/images/760kry/post/ee0f6268-ae82-44cb-a24f-97b6504fd2c3/image.png)

ex) 
```
version: 0.2

phases:
  build:
    commands:
       - npm install && npm run build

artifacts:
  files:
    - '**/*'
```

- 프로젝트 마스터 브랜치에 Push를 하면 자동으로 변경을 감지하고 아래와 같은 진행과정을 거쳐 연결된 EB에 배포된다.
<img width="500px" height="100px" src="https://velog.velcdn.com/images/760kry/post/97b2807c-7aef-44b6-906b-13f86b24c69d/image.png"/>

>아래와 같이 **`편집 > 스테이시 편집 > 표시된 수정 아이콘`을 클릭하면 환경 변수 및 여러 가지를 수정할 수 있다.**
![](https://velog.velcdn.com/images/760kry/post/69f009d9-1ee8-464c-95b3-db2fef93ac80/image.png)
![](https://velog.velcdn.com/images/760kry/post/17b85eaf-19c3-4d79-a6ad-24b88e8564ea/image.png)
![](https://velog.velcdn.com/images/760kry/post/3173951e-801d-4a67-ba39-8443f3c33b8a/image.png)
