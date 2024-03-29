---
title: HTTP와 HTTPS
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2022-08-01
categories: [CS]
tags: [http, https]
math: true
mermaid: true
---

## HTTP 란?

-   http는 Hyper Text Transfer Protocol의 약자로 웹에서 **요청과 응답의 형태로 정보를 주고받을 수 있는 프로토콜**입니다.
-   TCP 기반으로 작동하며 **비연결성, 비상태성 특징**을 가집니다.
    -   이로 인해 많은 실제 동시 접속을 최소화하여 더 많은 유저의 요청 처리를 할 수 있습니다. 
    -   비연결성, 비상태성 특징 때문에 사용자의 이전 상태(로그인 유무 등)를 알 수 없어 이를 해결하기 위해  
        쿠키, 세션, JWT 등이 도입되었습니다.
-   클라이언트에서 서버로 request message 보내고 서버에게 response message를 받는 형태입니다.
    -   request message: startline(method, path, http version), header, body로 구성
    -   response message: statusline(status code, status message, http version), header, body로 구성

### HTTP status code

- HTTP status code는 클라이언트가 보낸 HTTP의 서버의 응답 코드로, 상태 코드를 통해 요청의 성광과 실패를 판단할 수 있습니다. 

- 100부터 ~ 500번대 까지 총 5가지의 클래스로 구분되어 있으며 요청에 대한 상태를 알려줍니다.

- 웹 개발 시 상황에 맞는 응답 코드를 response에 담아서 클라이언트에 넘겨주며 이를 토대로 클라이언트는 알맞은 대응을 할 수 있습니다.

#### status code

-   **1xx**: 요청을 완료했고 작업을 계속합니다.
-   **2xx**: 요청을 성공 적으로 처리하였습니다.
    -   **200**: 요청을 성공 (ex: 조회 성공)
    -   **201**: 데이터 생성을 성공적으로 처리 (ex: 회원가입 성공, 게시물 작성 성공)
-   **3xx**: 요청에 추가적인 작업이 필요합니다.
-   **4xx**: 클라이언트 요청에 문제가 있습니다
    -   **400**: 데이터 형식이 올바르지 않을 때
    -   **401**: 인증이 되지 않은 상태에서 인증이 필요한 리소스에 접근했을 때
    -   **403**: 인증은 되었지만 권한이 없는 리소스에 접근했을 때
    -   **404**: 존재하지 않는 route에 요청했을 때
-   **5xx**: 서버에 문제가 있습니다.

## HTTPS 란?

**보안 소켓 계층 SSL/TSL을 사용한 HTTP**를 HTTPS라고 합니다.

HTTP가 정보를 주고받을 때 텍스트 형태 그대로 주고받기 때문에 민감한 정보를 담고 있을 경우 이 정보를 탈취한다면 민감한 정보를 원본 그대로 볼 수 있는 단점이 있습니다.
하지만 보안 소켓 계층 SSL/TLS을 사용한 HTTPS를 사용할 경우 주고받는 http message의 body 정보들이 암호화되어 중간에 누군가 탈취한다 하더라도 데이터를 쉽게 알아볼 수 없습니다. 
또 인증기관(CA)으로부터 검증된 사이트만 https를 사용할 수 있으므로 안전한 사이트를 이용할 수 있기 때문에 **HTTPS를 사용하는 하여 보안성을 확보해야 합니다.**

### SSL/TLS

-   TLS는 SSL의 업그레이드 버전으로 일반적으로 두 단어를 동일한 의미로 사용합니다.
-   SSL은 웹 서버와 웹 브라우저간의 보안을 위해 만든 프로토콜입니다.
-   대칭키 방식과 공개키(비대칭키) 방식을 혼합해서 사용합니다.  
    -   대칭키: 양쪽에서 암호화를 같은 키로 암호화와 복호화를 하는 방법입니다.
        -   대칭키: 데이터 암호화, 복호화 시 사용합니다. 
        -   대칭키 하나로 암호화와 복호화를 할 수 있기 때문에 대칭키를 탈취당할 경우 문제가 됩니다.
    -   공개키(비대칭키): 양쪽에서 서로 다른 키로 암호화와 복호화를 하는 방법입니다.
        -   공개키: 데이터 암호화 시 사용합니다.
        -   개인키: 데이터 복호화 시 사용합니다.
        -   공개키로 암호화한 데이터는 개인키로만 복호화가 가능하기 때문에 공개키를 가져가도 안전합니다.
    -   공개키 방식이 좋은데도 혼합해서 사용하는 이유는 무엇일까?
        -   공개키 방식만이 대칭키 방식보다 암호화 연산이 많이 소비되어 비용이 많이 듭니다. 때문에  공개키를 활용한 대칭키로 클라이언트와 서버는 통신할 수 있도록 혼합하여 사용합니다.

### SSL 인증서 / CA

#### SSL 인증서

-   클라이언트가 접속한 서버가 신뢰할 수 있는 서버임을 보장합니다.
-   SSL 통신에 사용할 공개키를 클라이언트에게 제공합니다.

#### CA(Certificate Authority) 

-   인증서 역할은 클라이언트가 접속한 서버가 클라이언트가 의도한 서버가 맞는지를 보장하는 역할을 합니다.
-   신뢰성이 엄격하게 공인된 기업들만 참여할 수 있습니다.

![](https://velog.velcdn.com/images/760kry/post/e6c457f6-836a-4ce6-8b4c-d3c66c5025a7/image.png)


아래의 설명을 위 그림으로 나타낸 것입니다.

1.  서버는 사이트 정보와 자신의 공개키를 인증기관에 전달합니다.
2.  인증기관에서는 전달받은 데이터를 검증 후 자신의 개인키로 서명한 사이트 인증서를 서버에 전달 합합니다.
3.  인증기관은 클라이언트에 인정기관 공개키를 전달합니다. 이 공개키는 브라우저에 자동으로 내장됩니다.
4.  클라이언트가 서버에 접속 요청을 시도하면 서버는 사이트 인증서를 전송합니다.
5.  클라이언트는 브라우저에 내장되어있는 인증기관 공개키로 사이트 인증서를 복호화하여 검증하고 서버 공개키를 얻습니다.
6.  클라이언트는 자신의 대칭키를  서버 공개키로 암호화하여 암호화한 대칭키를 서버에 전송합니다.
7.  서버는 전달받은 대칭키를 자신의 개인키로 복호화하여 전달받은 대칭키를 알아냅니다.

이렇게 공개키를 활용한 대칭키로 클라이언트와 서버는 통신하게 됩니다.
