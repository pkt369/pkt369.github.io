---
title: "HTTP 2.0 과 다른 버전 차이"
excerpt: "HTTP 2.0 과 다른 버전 차이"

categories:
  - network
tags:
  - [network, http, https]

permalink: /network/http-version/

toc: true
toc_sticky: true

date: 2023-05-21
last_modified_at: 2023-05-21
---

## HTTP 공통
HTTP TCP/IP 는 3 way Handshake 을 통해 연결을 먼저 합니다. <br>
3 way Handshake 은 클라이언트: SYN -> 서버: SYN + ACK -> 클라이언트: ACK 로 서로 연결시키는 과정입니다. <br>
![3 way handshake](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F225A964D52F1BB6917)<br>
3 way handshake 를 거치게 되면 서로 데이터를 전송할 준비가 되었다는 의미이고, 신뢰성 있는 연결을 통해 데이터의 신뢰를 보장해줍니다. <br>
<br>
<br>

----

## HTTP 1.0
HTTP 1.0 의 특징은 3 way handshake 를 하고 나서 한번의 요청과 응답이 왔다갔다 하고나서 연결을 끊는 것이 특징입니다.<br>
![http1.0](/assets/images/posts_img/network-http-https/http1-0.png) <br>
<br>
한번마다 연결을 하게되면 연결을 하는데 많은 비용을 사용하게 됩니다.<br>
그래서 나온 것이 keep-alive 옵션입니다. <br>
<br>
<br>

----

## HTTP 1.1
HTTP 1.1 은 1.0을 보완한 프로토콜입니다. 위에서 언급한 keep-alive 는 한번의 연결로 많은 요청과 응답을 주고 받을 수 있게 되었습니다.<br>
![http1.1](/assets/images/posts_img/network-http-https/http1-1.png) <br>
<br>
하지만 많은 요청과 응답을 하는 사이트가 나타났고, 한 요청이 오래걸리면서 다음 요청들이 기다려야 하는 현상이 벌어졌습니다. (HOL Blocking - head of line Blocking) <br>
이 문제가 이슈가 되었고, 이 문제를 보완한 다음 버전인 HTTP 2.0 이 나오게 되었습니다. <br>
<br>
<br>

----

## HTTP 2.0
HTTP 2.0 은 HTTP 1.0 과 같이 하나의 연결을 가지는건 같지만, Stream 을 통해 병렬처리가 가능하게 되었습니다.<br>
![stream](https://web-dev.imgix.net/image/C47gYyWYVMMhDmtYSLOWazuyePF2/0bfdEw00aKXFDxT0yEKt.svg) 
<br>
출처) [https://web.dev/performance-http2/](https://web.dev/performance-http2/)
<br>

### Stream
Stream 은 프레임을 주고 받는 하나의 통로입니다. <br> 
<br>
프레임은 HTTP 2.0의 제일 작은 하나의 단위이며, 프레임이 속한 스트림을 구별하는 프레임 헤더가 속해있습니다. Header 와 Data 둘 중 하나를 가지고 있습니다.<br>
<br>
메세지는 HTTP 1.x 과 같이 요청 또는 응답 단위이며, 다수의 프레임으로 이루어져 있습니다. <br>
<br>
정리하면 Frame > Message > Stream 으로 이루어져 있습니다.<br>
<br>
<br>
그렇다면 Stream 은 어떻게 병렬처리를 할까요? <br>
<br>

HTTP 1.x 에서는 하나의 연결에 하나의 request and response message 만 가졌습니다. <br> 
위의 그림을 보시면 Stream 은 여러개의 메세지를 가지고 있습니다. 즉, 하나의 연결에 여러개의 Message 가 담긴 하나의 Stream을 가지고 통신 하기 때문에 한번에 많은 요청과 응답을 처리할 수 있어졌습니다.<br>
<br>
<br>

#### Stream 재조립
그렇지만 하나의 스트림이 오래걸린다면 HTTP 1.x 에서 말한 HOL Blocking 이 일어날 수 있지 않을까요? <br>
위의 문제에 대해 HTTP 2.0 버전에서 해결을 했고, 그 방법은 Stream 의 재조립입니다 <br>
![request](https://web-dev.imgix.net/image/C47gYyWYVMMhDmtYSLOWazuyePF2/4RwALfscCwB7MDa1bGsV.svg) <br>
출처) [https://web.dev/performance-http2/](https://web.dev/performance-http2/)
<br>
stream 1번이 오래걸린다고 가정하고 다른 스트림은 기다려야 하는 문제점이 있을 때, 위 그림과 같이 Stream 1 을 분해하고 stream 3번 뒤에 재조립을 합니다. <br>
<br>
위 방식 처럼 stream 3번을 먼저 처리할 수 있게 되면서 HOL Blocking 을 해결할 수 있게 되었습니다. <br>
<br>
<br>


#### 우선 순위
위에서 재조립이 된다고 했는데 어떤 기준으로 어떤 스트림이 먼저 처리가 될까요? <br>
정답은 스트림이 가지고 있는 종속성과 가중치로 우선 순위 지정 트리를 만들어 해결합니다. <br>
<br>
종속성은 어떤 상위 스트림을 가지는지 입니다. 상위 스트림이 응답처리를 끝내야 해당 스트림이 응답을 보낼 수 있습니다. <br>
만약 종속성이 비워져 있으면 루트 스트림에 종속됩니다.<br>
<br>
가중치는 한정된 CPU, 메모리 등 리소스를 할당하는 것입니다. <br>
할당하는 기준은 <span style="color:red">같은 노드의 합산 / 각 스트림의 가중치</span>입니다.<br>
<br>
예를 들어 스트림 A 와 B 가 존재한다면 스트림 A 의 가중치는 15, 스트림 B 의 가중치는 3 를 가집니다.<br>
가중치에 따라 스트림 A 는 15 / 18 = 5 / 6 이고, 스트림 B는 3 / 18 = 1 / 6 입니다. <br>
<br>
따라서 A 는 리소스의 5 / 6을 받고, B 는 리소스의 1 / 6 을 받습니다. <br>
<br>

![우선순위](https://web-dev.imgix.net/image/C47gYyWYVMMhDmtYSLOWazuyePF2/ydLldhPadjknvvrUiCai.svg) <br>

그렇다면 위의 그림에서는 어떻게 진행될까요?<br>
왼쪽에서 부터 오른쪽으로 순서대로 보면<br>
1. A와 B 둘다 루트 스트림에 종속되므로 A 와 B 는 가중치에 따라 리소스가 할당됩니다. A 는 12, B 는 4 입니다. 따라서 A 는 3 / 4, B 는 1 / 4 만큼 리소스를 할당받게 됩니다. <br>
<br>
2. C 는 D 에 종속적이라 D 가 응답이 끝난 후에 C 가 응답합니다. 여기서 혼자 D 의 자식노드이기 때문에 C 의 가중치는 상관없습니다.<br>
<br>
3. D 가 응답이 끝난 후에 C 에 리소스 할당하고, C가 응답이 끝난 후에 A 와 B 가 리소스 할당 받습니다.<br>
A와 B 는 1번과 같이 동작됩니다.<br>
<br>
4. D 가 응답이 끝난 후에 E 와 C 가 5:5 비율로 리소스를 할당 받고, C 가 응답이 끝나야 A와 B 가 리소스를 할당받습니다. <br>
<br>
참조) 4번의 경우 헷갈려 챗지피티를 통해 문의를 했고 답변 받았습니다.<br>

![network-stream-weight](/assets/images/posts_img/network-http-https/network-stream-weight.png) <br>
<br>
<br>

#### 추가 성능 개선
HTTP 2.0 에서 지원하는 또다른 기능으로는 하나의 요청에 여러개의 응답을 보낼수 있다는 것입니다.<br>
예를 들어 html 요청했을 때 html 뿐만 아니라 css, image 등을 보내줄 수 있게 되었습니다.<br>
![http-server-push](/assets/images/posts_img/network-http-https/http-server-push.png) <br>
<br>
<br>

또 다른 성능 개선으로는 중복으로 보내던 Header 를 제거하고, 허프만이 만든 HPack 압축 방식을 통해 개선하였습니다. <br>

![http-header](https://web-dev.imgix.net/image/C47gYyWYVMMhDmtYSLOWazuyePF2/IYfczfC6ZCTxUVboaEZy.svg)<br>
출처) [https://web.dev/performance-http2/](https://web.dev/performance-http2/) <br>
<br>

서버와 클라이언트는 이미 보낸 헤더를 테이블로 저장해두고 보낼때 헤더 테이블의 인덱스와 바뀐 값만 HPACK 압축을 통해 보내줍니다.<br>
위처럼 하게 되면 헤더의 크기가 85%~88%가 개선된다고 합니다.<br>
<br>
<br>

----

## Reference
[https://web.dev/performance-http2/](https://web.dev/performance-http2/) <br>
[https://jaehoney.tistory.com/281](https://jaehoney.tistory.com/281) <br>
[https://brunch.co.kr/@sangjinkang/3](https://brunch.co.kr/@sangjinkang/3) <br>










