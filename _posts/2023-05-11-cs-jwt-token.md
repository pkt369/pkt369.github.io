---
title: "jwt token"
excerpt: "jwt token 개념과 어디서 왜 사용되어야 하는지에 대한 글입니다."

categories:
  - CS
tags:
  - [jwt token, 인증, 인가]

permalink: /CS/jwt-token/

toc: true
toc_sticky: true

date: 2023-05-11
last_modified_at: 2023-05-11
---

## jwt token 이란
jwt token 은 Json Web Token 의 줄임말로, 인터넷 표준 인증 방식 중 하나입니다. <br>
jwt token 은 3가지로 이루어져 있으며, 각각 base64Encoding 을 통해 인코딩됩니다. <br>
<br>

#### Header
Header는 token 의 타입과 시그니처 생성할 때 사용한 알고리즘이 있습니다.<br>
<br>

#### Payload
Payload 는 서버측에서 사용하고 싶은 데이터를 키값 형태로 넣는 곳입니다.<br>
예를 들어 발행자, 만료 시간, 권한 범위 등을 입력할 수 있습니다. <br>
<br>

#### Signature
Signature 는 토큰의 무결성을 보장하기 위해 사용합니다.<br>
디코딩된 Header 와 Payload 를 합쳐서 Header 에 있는 알고리즘과 개인키(비밀키)로 암호화합니다.<br>
<br>

이 세부분을 . 으로 연결되어 하나의 문자열로 넘겨주게 됩니다.
<br>
<br>

----

## Jwt token 동작 방식
사용자 [인증 또는 인가](https://pkt369.github.io/CS/authentication-authorization-diff)가 되면 Jwt token 을 넘겨주고 사용자가 요청을 할 때 토큰을 다시 넘겨주면 서버측에서 올바른 토큰인지 확인합니다. <br>
<br>

### 올바른 토큰인지 확인하는 방법
1. 받은 토큰을 먼저 .으로 나눈 뒤 base64decoding 을 진행합니다.
2. 시그니처 부분을 개인키(비밀키)로 복호화합니다.
3. 


## 작성중

<br>
<br>

## Reference
[https://www.okta.com/kr/identity-101/authentication-vs-authorization/](https://www.okta.com/kr/identity-101/authentication-vs-authorization/) <br>
[https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization](https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)





