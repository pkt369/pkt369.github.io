---
title: "인증과 인가"
excerpt: "인증과 인가"

categories:
  - CS
tags:
  - [OAuth2, 인증, 인가]

permalink: /CS/authentication-authorization-diff/

toc: true
toc_sticky: true

date: 2023-05-11
last_modified_at: 2023-05-11
---

## 인증
인증(Authentication)은 사용자의 신원을 검증하는 프로세스를 말합니다. <br>
<br>
예를 들어 서버에 아이디와 비밀번호를 넘기는 로그인 행위가 인증에 해당됩니다. <br>
<br>
<br>

## 인가
인가(Authorization)은 사용자에게 권한을 부여하는 프로세스를 말합니다. <br>
<br>
예를 들어 OAuth2 에서 Auth Server 가 리소스에 접근할 수 있는 Access Token 을 주는 것이 인가에 해당됩니다. <br>
<br>
<br>

## 인증과 인가 비교
인증과 인가는 서로 다른 단계입니다. 인증 후에 인가가 이루어집니다. <br>
예를 들어 어떤 사용자는 파일을 읽는 권한만 있을 수 있고, 어떤 사용자는 파일을 쓰고 읽는 권한이 있을 수 있습니다.<br>
이렇게 인가는 인증이 된 사용자에게 역할에 따라 권한을 달리 줄 수 있는데 이러한 과정을 인가라고 합니다. <br>
<br>
<br>

## Reference
[https://www.okta.com/kr/identity-101/authentication-vs-authorization/](https://www.okta.com/kr/identity-101/authentication-vs-authorization/) <br>
[https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization](https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)





