---
title: "http 와 https 차이"
excerpt: "http 와 https 차이"

categories:
  - network
tags:
  - [network, http, https]

permalink: /network/http-https/

toc: true
toc_sticky: true

date: 2023-05-17
last_modified_at: 2023-05-17
---

## HTTP
http 는 80포트를 사용하고 있는 프로토콜입니다. <br>
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









