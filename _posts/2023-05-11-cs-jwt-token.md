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

## Jwt Token 이란
Jwt Token 은 Json Web Token 의 줄임말로, 인터넷 표준 인증 방식 중 하나입니다. <br>
Jwt Token 은 3가지로 이루어져 있으며, 각각 base64Encoding 을 통해 인코딩됩니다. <br>
![jwt-token](/assets/images/posts_img/cs-jwt-token/jwt-token-structure.png) <br>
참조)[https://jwt.io/](https://jwt.io/) <br>
<br>

#### Header
Header는 Token 의 타입과 시그니처 생성할 때 사용한 알고리즘이 있습니다.<br>
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

## Jwt Token 동작 방식
사용자 [인증 또는 인가](https://pkt369.github.io/CS/authentication-authorization-diff)가 되면 Jwt Token 을 넘겨주고 사용자가 요청을 할 때 토큰을 다시 넘겨주면 서버측에서 올바른 토큰인지 확인합니다. <br>
<br>

### 서버가 올바른 토큰인지 확인하는 방법
1. 받은 토큰을 먼저 .으로 나눈 뒤 base64decoding 을 진행합니다.
2. 시그니처 부분을 개인키(비밀키)로 복호화합니다.
3. Jwt Token 의 header 와 복호화한 헤더가 같은지, Jwt Token 의 payload 와 복호화환 payload 가 같은지 확인해서 같다면 올바른 사용자로 확인합니다.

<br>
<br>

----

## Jwt 사용하는 이유
1. 무상태(stateless): 세션은 사용자의 값을 세션 스토리지에 저장해서 사용하는 반면에 토큰은 payload 에 데이터가 있어 따로 저장하지 않습니다.<br>
<br>
2. 보안: 개인키로 암호화환 시그니처가 있기 때문에 토큰을 변경한다 하더라도 올바른 토큰인지 확인이 가능합니다.<br>
<br>
3. 확장성: 세션의 경우 사용자의 인증 정보를 서버의 메모리에 저장하는 반면 토큰은 사용자측에 저장하므로 서버를 늘릴 경우 기존 서버와 상관없어 확장에 좋습니다.<br>
<br>
<br>
제가 생각하는 가장 큰 이유는 MSA 환경에서 이점이 있기 때문입니다.<br>
<br>
마이크로서비스 아키텍처(MSA)에서 서비스 간 인증은 중요한 부분인데 세션을 이용하게 되면 서버마다 저장하고 서버마다 저장을 해야합니다. <br>
이렇게 되면 복잡해질뿐만 아니라 한 서버에서 세션 데이터를 변경했을 때 다른 서비스에서 세션 정보가 달라 문제가 생길 수 있기 때문 입니다. <br>
<br>
<br>
만약 Jwt Token 을 이용하게 되면 세션에 데이터를 저장하지 않기때문에 위와 같은 문제들을 해결할 수 있습니다. <br>
<br> 




<br>
<br>

----

## Reference
[https://www.okta.com/kr/identity-101/authentication-vs-authorization/](https://www.okta.com/kr/identity-101/authentication-vs-authorization/) <br>
[https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization](https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)





