---
title: "HTTP 버전 1.0 1.1 2.0의 차이"
excerpt: "http version 특징"

categories:
  - network
tags:
  - [network, http, https]

permalink: /network/http-version/

toc: true
toc_sticky: true

date: 2023-05-17
last_modified_at: 2023-05-17
---

## HTTP 공통
HTTP TCP/IP 는 3 way Handshake 을 통해 연결을 먼저 합니다. <br>
3 way Handshake 은 클라이언트: SYN -> 서버: SYN + ACK -> 클라이언트: ACK 로 서로 연결시키는 과정입니다. <br>
3 way handshake 를 거치게 되면 서로 데이터를 전송할 준비가 되었다는 의미이고, 신뢰성 있는 연결을 통해 데이터의 신뢰를 보장해줍니다. <br>
<br>
<br>

----

## HTTP 1.0
HTTP 1.0 의 특징은 3 way handshake 를 하고 나서 한번의 요청과 응답이 왔다갔다 하고나서 연결을 끊는 것이 특징입니다.<br>
(이미지)<br>
<br>
한번마다 연결을 하게되면 연결을 하는데 많은 비용을 사용하게 됩니다.<br>
그래서 나온 것이 keep-alive 옵션입니다. <br>
<br>
<br>

----

## HTTP 1.1
HTTP 1.1 은 1.0을 보완한 프로토콜입니다. 위에서 언급한 keep-alive 는 한번의 연결로 많은 요청과 응답을 주고 받을 수 있게 되었습니다.<br>
(이미지)<br>
<br>
HTTP 1.1 의 다른 특징은 한번의 요청 후 다음 요청을 하기까지 


참고) [HTTP](https://pkt369.github.io/network/http-protocol/), [HTTP, HTTPS 차이](https://pkt369.github.io/network/http-https/) <br>
<br>
tcp/ip 는
암호화가 되어 있지 않아 데이터를 보낼때 중간에서 가로채면 가로챈 데이터를 그대로 읽을 수 있습니다. <br>
<br>
참고) [HTTP](https://pkt369.github.io/network/http-protocol/)를 참고하시면 됩니다.
<br>
<br>
<br>

----

## HTTPS
https 는 443포트를 사용하고 있고, http 에서 데이터 암호화가 된 프로토콜입니다. <br>
데이터가 탈취당한다 하더라도 암호화가 되어 있기때문에 데이터를 읽을 수 없는 것이 특징입니다. <br>

### 암호화
https 는 대칭키와 비대칭키를 사용하고 있습니다. <br>
대칭키는 클라이언트와 서버가 <span style="color:red">같은 키</span>를 가지고 암호화 및 복호화를 하는 방식입니다. <br>
<br>===
비대칭키는 <span style="color:red">공개키와 개인키</span>를 이용해 암호화 및 복호화를 하는 방식입니다. <br>
<br>
공개 키: 모두에게 공개할 수 있는 키, 공개키 암호화 -> 개인키 복호화 방식 <br>
개인 키: 서버만 알고 있어야 하는 키, 개인키 암호화 -> 공개키 복호화 방식 <br>
=> 공개키를 안다고해도 공개키로 보낸 데이터를 탈취해도 데이터를 얻을 수 없습니다.<br>
<br>

![https-process](/assets/images/posts_img/network-http-https/https-process.png) <br>
<br>

### 동작 과정
1. 클라이언트에서 서버로 요청을 보내면 서버는 인증서 (공개키)를 클라이언트로 보냅니다. <br>
<br>
2. 브라우저는 받은 인증서가 유효한지 확인하고 세션키를 발급해서 받은 공개키로 암호화를 합니다.<br>
<br>
3. 브라우저가 암호화한 세션키를 서버로 보내면 서버는 받아서 개인키로 복호화합니다. <br>
<br>
4. 데이터를 주고받을 때 서로 가지고 있는 공통키(세션키)를 가지고 암호화 및 복호화를 진행하면서 데이터를 주고 받습니다.<br>
<br>
<br>

### 인증서
1. 서버측에서 CA(Certification Authority) 서버에 돈을 주고 공개키를 공유하여 인증서를 만듭니다. <br>
<br>
2. CA 서버에서는 CA기업의 이름, 서버의 정보, 서버의 공개키 등을 CA 서버에서 준 개인키로 암호화합니다. <br>
<br>
3. 서버측은 CA 서버에서 인증서를 받아 가지고 있다가 요청이 오면 클라이언트에게 인증서를 넘겨줍니다.<br>
<br>
4. 클라이언트는 공개키로 인증서를 복호화하여 기업의 공개키를 얻습니다.<br>
(브라우저에서 CA 서버의 공개키를 이미 가지고 있습니다.) <br>
<br>









