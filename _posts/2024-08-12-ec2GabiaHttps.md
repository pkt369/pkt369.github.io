---
title: "GABIA 에서 산 도메인 EC2 에서 HTTPS 연결하기"
excerpt: "GABIA 에서 산 도메인 EC2 에서 HTTPS 연결하기"

categories:
  - project-setting
tags:
  - [backend, project, setting, mysql, ec2, gaiba, https, domain]

permalink: /project-setting/https/ec2-gabia

toc: true
toc_sticky: true
published: true

date: 2024-08-12
last_modified_at: 2024-08-12


---



# GABIA 에서 산 도메인 EC2 에서 HTTPS 연결하기

GABIA 에서 도메인을 사는 과정은 생략하고 어떻게 EC2 인스턴스와 가비아를 연결하고 HTTPS 로 사용할 수 있는지에 대한 포스팅을 시작하겠습니다.

포스팅의 순서는 다음과 같습니다.

> 가비아에서 산 도메인을 Amazon 에 인증하기 > SSl(TLS) 인증받기 > 보안그룹 설정하기 > 타겟 그룹 생성하기 > Load Balancer 생성 > 리스너 규칙 생성하기 > Health Check

하지만 이번 프로젝트의 경우 최대한 비용을 아끼고자 EC2 에서 docker 를 활용해 MYSQL 을 실행 하였습니다.

<br>

## 가비아에서 산 도메인 Amazon 에 인증하기 

해당 작업을 하는 이유는 **도메인을 우리가 진짜로 가지고 있는지 확인하는 작업**입니다.

<img width="1053" alt="" src="https://github.com/user-attachments/assets/d3bcceed-011c-4b7b-9775-8c0a19607b3a">

먼저 아마존에서 검색창에 Route53 을 검색하여 클릭해줍니다.

<br>

<img width="1674" alt="" src="https://github.com/user-attachments/assets/8de2f932-380e-40f4-b5e1-119dacfe447a">

그다음 DNS 관리로 들어가줍니다.

<br>

<img width="1622" alt="" src="https://github.com/user-attachments/assets/704e6a2c-fb4d-4b7a-92a8-19ec1df663a0">

그다음 **호스팅 영역 생성** 버튼을 눌러줍니다.

<br>

<img width="825" alt="" src="https://github.com/user-attachments/assets/482243d6-9a17-4a0e-a7e2-7b2ebca10442">

- **도메인 이름**:
  - 우리가 산 도메인 주소
- **유형**:
  -  퍼블릭 호스팅 영역 선택 ( 외부에서 접근 가능 시 퍼블릭 호스팅 )

그다음 **호스팅 영역 생성** 버튼을 눌러줍니다.

<br>

<img width="1345" alt="" src="https://github.com/user-attachments/assets/8408b39a-cc73-42a2-80b5-5ad28eb30345">

그러면 다음과 같이 생성이 되는데 **유형이 NS** 인 Row 에 해당하는 **값/트래핑 라우팅 대상**의 총 **4개**의 값을 복사합니다.

<br>

<img width="1274" alt="" src="https://github.com/user-attachments/assets/890f6343-29c2-470f-851d-ceda6ba14716">

그 다음 가비에 페이지에 접근해서 **도메인 정보 변경 > 네임서버 > 설정**에 접근해서 위에서 복사한 값을 **1~4차**까지 모두 넣어줍니다.

> 값 마지막에 . 을 제외하고 넣어주세요 ex) ns-000.awsdns-00.net

그 다음 **소유자 인증** 을 마친뒤 적용을 눌러주세요

<br>

<br>

---

## SSL(TLS) 인증받기

SSL (TLS) 는 Secure Socket Layer 와 Transport Layer Security 는 **인터넷 상에서 데이터를 안전하게 전송하기 위한 암호화 프로토콜**입니다.

TLS 는 SSL 의 후속버전으로, 요즘은 SSL 을 거의 사용하지 않고 TLS 를 사용합니다.

<br>

TLS 를 사용하는 이유는 다음과 같습니다.

- **대칭키와 비대칭키를 이용한 암호화**를 이용하여 데이터를 암호화하여 네트워크 중간에서 알지 못합니다.
- 위에서 암호화를 해뒀기때문에 **데이터의 무결성**이 보장됩니다.
- 서버에서 신원을 보증해 사용자가 **신뢰할 수 있는 서버**와 통신하고 있음을 보장합니다.

<br>

<img width="1059" alt="" src="https://github.com/user-attachments/assets/2f8b9020-de07-4cbf-b54c-eb8577c491f6">

검색창에 **Certificate Manager** 를 검색합니다.

<br>

<img width="1368" alt="" src="https://github.com/user-attachments/assets/eceab51c-9aa9-43eb-a429-4f7a12391fed">

그다음 **요청** 버튼을 누릅니다.

<br>

<img width="839" alt="" src="https://github.com/user-attachments/assets/62644343-4705-4233-95ab-3efaed602f97">

인증서 유형에서는 **퍼블릭 인증서 요청**을 선택하고 다음을 눌러줍니다.

<br>

<img width="832" alt="" src="https://github.com/user-attachments/assets/6c9dc557-ea20-4d4b-a3df-50eaa4e6851a">

도메인 이름에는 **가비아에서 산 도메인**을 입력해줍니다.

검증 방법은 **DNS 검증**, 키 알고리즘으로는 **RSA 2048** 을 선택해줍니다.

<br>

<img width="1361" alt="" src="https://github.com/user-attachments/assets/d5376c57-f13f-427e-9a46-a55d8e9357fc">

그러면 다음과 같이 상태가 검증 대기 중으로 새로운 Row 가 생기신 것을 보실 수 있습니다.

그다음 생긴 인증서 ID 를 클릭해줍니다.

<br>

<img width="1370" alt="" src="https://github.com/user-attachments/assets/7f244141-f1a7-4b37-b5fe-e50e3c01ff29">

Route 53 레코드 생성 버튼을 눌러줍니다.

<br>

<img width="828" alt="" src="https://github.com/user-attachments/assets/e4d972a9-9b5d-4899-bf6c-54cf69d68f02">

그 다음 우리가 방금 만든 **인증서를 체크**하고 **레코드 생성 버튼**을 눌러줍니다.

<br>

<img width="1322" alt="" src="https://github.com/user-attachments/assets/4110eca4-15e8-486a-989b-2bf6f63a6328">

그럼 위처럼 CNAME 이 생기신 것을 보실 수 있습니다.

여기서 **CNAME** Row 에서 **값/트래픽 라우팅 대상** 에 있는 값을 복사 한뒤 **가비아에서 DNS 관리**로 이동합니다.

<br>

<img width="1264" alt="" src="https://github.com/user-attachments/assets/6c24bdd9-bc08-4479-9f15-4e8ebceb740d">

위처럼 타입에서는 CNAME 을 값/위치에서는 아까 우리가 받은 **데이터를 붙혀넣기** 해줍니다.

> CNAME 레코드는 CA 에서 소유한 도메인인지 확인하기 위해 내려주는 값으로, 요청을 보내 확인하고 확인하면 SSL(TLS) 인증서를 발급해줍니다.

<br>

<br>

---

## 보안그룹 설정하기

보안그룹은 EC2 인스턴스와 같은 리소스에 대한 **네트워크 트래픽을 제한**하는 역할을 합니다. 

인바운드(수신), 아웃바운드(송신)에 대해 트래픽을 허용하거나 차단하는 규칙을 설정할 수 있습니다.

즉, HTTPS 를 사용할 경우 **인바운드 규칙에서 443 포트를 열어놔야 한다**는 소리입니다.

<br>

<img width="1089" alt="" src="https://github.com/user-attachments/assets/99dd22cf-2816-4e4d-9929-25a54b1c2b11">

먼저 EC2 를 검색해서 클릭해줍니다.

<br>

<img width="841" alt="" src="https://github.com/user-attachments/assets/bfcb9f67-e49d-4d91-96cd-a363cfacf69e">

그럼 다음과 같이 인스턴스가 나오는데 **인스턴스 ID** 를 클릭해줍니다.

<br>

<img width="649" alt="" src="https://github.com/user-attachments/assets/114e2c81-400e-4fdc-898b-79f472b5471f">

그 다음 보안 탭에 있는 보안그룹을 클릭해줍니다.

<br>

<img width="1402" alt="" src="https://github.com/user-attachments/assets/b7b619fe-3528-407d-95fa-d6078f258e35">

그럼 다음과 같이 나올텐데 **인바운드 규칙 탭**에서 **인바운드 규칙 편집** 버튼을 눌러줍니다.

<br>

<img width="1581" alt="" src="https://github.com/user-attachments/assets/40bd9224-88b3-464f-9972-b317a85aad4d">

유형에 HTTPS 를 선택하고 0.0.0.0/0(IPv4) 과 ::/0(IPv6) 를 입력해줍니다.

저는 IPv4 만 사용하나 예시를 위해 IPv6 도 적어두었습니다.

> IPv4 : 우리가 흔히 사용하는 ip 주소 ( ex) 172.00.000.1 )
>
> IPv6: IPv4 의 호환성을 위해 설계됐으며, 더 큰 주소 공간을 제공합니다. ( ex) 2001:0db8:85a3:0000:0000:8a2e:0370:7334 )

이후 저장을 눌러 변경사항을 저장해줍니다.

<br>

<br>



---

## 타겟 그룹 생성하기

타겟 그룹이란 **로드밸런서가 트래픽을 라우팅할 수 있는 특정 대상을 지정하는 역할**입니다.

<img width="1654" alt="" src="https://github.com/user-attachments/assets/8ad19572-349f-4966-8345-145765866c1e">

**EC2 > 로드 밸런싱 > 대상 그룹**으로 이동해줍니다.

그리고 **대상 그룹 생성** 버튼을 눌러줍니다.

<br>

<img width="816" alt="" src="https://github.com/user-attachments/assets/c87d98c6-ca05-463e-8744-1458ea9d8e46">

그럼 그룹 세부 정보 지정이라는 페이지가 나오게 되는데 

- 기본구성
  - 인스턴스 선택
- 대상 그룹 이름
  - 본인이 알 수 있는 이름

과 같이 작성해줍니다.

<br>

<img width="795" alt="" src="https://github.com/user-attachments/assets/6b074a37-e419-4ad5-816d-a6eb3824df12">

- 프로토콜: 포트
  - 현재 사용하고 있는 프로토콜과 포트를 적어줍니다. ( HTTP :: 8080 )
- IP 주소 유형
  - IPv4 ( ex) 172.00.000.1 )
- VPC
  - EC2 와 똑같게 설정
- 프로토콜 버전
  - HTTP1

<br>

<img width="813" alt="" src="https://github.com/user-attachments/assets/0fbda9ca-6ff7-4a03-8a1f-6c1d4deddb2e">

상태검사 (HealthCheck) 는 서버에 **요청을 보냈을 때 200 응답을 받을 수 있는 경로로 설정**해야 합니다.

보통 컨트롤러에서 /healthCheck 로 만들어두긴합니다만 저는 root 경로로 설정해두었습니다.

<br>

<img width="1344" alt="" src="https://github.com/user-attachments/assets/a1f1ba1d-0b24-4b2f-8edb-33bba190e7f6">

그다음 대상 등록 페이지가 나오는데 **사용가능한 인스턴스의 체크박스에 체크**하고 **사용하는 포트를 입력**하신후 **아래 보류 중인 것으로 포함** 버튼을 눌러줍니다.

그럼 다음과 같이 대상에 row 가 추가됩니다.

그다음 대상 **그룹 생성 버튼**을 눌러줍니다.

<br>

<br>

---

## Load Balancer 생성

로드 밸런서란 네트워크 트래픽을 여러 서버(또는 인스턴스)로 균등하게 분산시키는 역할을 하는 장치입니다.

하지만 같은 도메인이지만 다른 포트로 들어오는 (8080, 443) 에 대해서 위에서 설정한 대상 그룹으로 이어주는 작업을 진행합니다.

<img width="1402" alt="" src="https://github.com/user-attachments/assets/75e0610a-4423-4f60-9af6-fe69ecf6a539">

EC2 > 로드 밸런싱 > 로드 밸런서 로 들어와 **로드 밸런서 생성** 버튼을 눌러줍니다.

<br>

<img width="827" alt="" src="https://github.com/user-attachments/assets/62a4a987-e5f6-4f4d-93b4-49a945ab5d10">

그럼 다음과 같은 페이지가 나오는데 **Application Load Balancer 에 있는 생성 버튼**을 선택해줍니다.

<br>

<img width="1096" alt="" src="https://github.com/user-attachments/assets/50eb39ca-3f97-4b5a-b3f9-57cf9001f163">

- 로드 밸러서 이름
  - 사용자가 알 수 있는 이름
- 체계
  - 인터넷 경계
- 로드 밸런서 IP 주소 유형
  - IPv4

를 선택합니다.

<br>

<img width="1098" alt="" src="https://github.com/user-attachments/assets/b876ad5e-0250-4696-8ef5-1573ba60eee1">

네트워크 맵핑에는 **EC2 인스턴스에서 사용하고 있는 VPC** 를 선택해야 합니다.

서브넷은 2개 이상을 선택해야하는데 **둘 중 하나는 EC2 인스턴스가 사용하는 서브넷**과 같아야 합니다.

<br>

<img width="1101" alt="" src="https://github.com/user-attachments/assets/0c7f085d-1ed9-4e7b-a9ca-489dec721911">

보안 그룹은 인스턴스에서 사용하는 보안그룹을 선택합니다. ( default 는 안넣으셔도 됩니다. )

<br>

<img width="1095" alt="" src="https://github.com/user-attachments/assets/9b1a6539-64d3-4fb1-85f8-62756d35df74">

리스너 및 라우팅에서는 **사용하는 프로토콜과  포트, 그리고 프로토콜 HTTPS 포트는 443 으로 선택**합니다.

그리고 **기본 작업에는 8080 과 443 에 둘다 아까 설정한 대상 그룹을 넣습니다.**

<br>

<img width="1071" alt="" src="https://github.com/user-attachments/assets/bde3882d-91fd-4fae-9983-2d22ec94c843">

그 다음 보안 리스너 설정에서 

- 인증서 소스:
  - ACM에서
- 인증서
  - 우리가 생성했던 인증서

를 선택해줍니다.

그리고 **로드 밸런서 생성** 버튼을 눌러 생성해줍니다.

<br>

<br>



---

## 리스너 규칙 생성하기

<img width="1446" alt="" src="https://github.com/user-attachments/assets/3c5ad881-658c-4d7a-8876-43f983d7a186">

위에서 잘 저장했다면 저장된 Row 가 있을텐데 row 를 클릭하고, 그다음 리스너 및 규칙을 누르면 위 화면과 같게 나옵니다.

 **HTTPS 에 규칙 1개, SSL/TLS 인증서**가 붙어있고,  **HTTP 8080 같은 경우 1개**가 나와야 합니다. 저는 이미 설정을 완료해서 2개로 보입니다.

**HTTP:8080** 을 눌러줍니다.

<br>

<img width="1406" alt="" src="https://github.com/user-attachments/assets/ae02ffe8-c449-4741-915f-caae7e258987">

세부정보로 들어와 리스너 규칙을 보면 기본값 1개만 설정 되어 있을 것 입니다.

그다음 **규칙 추가** 버튼을 눌러줍니다.

<br>

<img width="836" alt="" src="https://github.com/user-attachments/assets/5c0c2f37-498c-4649-a012-9a15ef90ffa3">

다음 버튼을 바로 눌러줍니다.

<br>

<img width="854" alt="" src="https://github.com/user-attachments/assets/08815b6e-481c-4a13-bf60-eef2920eb8fa">

그다음 조건 추가 버튼을 눌러줍니다.

<br>

<img width="836" alt="" src="https://github.com/user-attachments/assets/c3289b94-c7f9-4a48-8c74-ad784ce8ebcc">

- 규칙 조건 유형:
  - 호스트 헤더
- 호스트 헤더:
  - 사용하는 도메인

이후 확인 버튼을 눌러줍니다.

> 호스트 헤더는 도메인 설정을 의미합니다. 예를 들어 api.echoeco.shop 으로 설정한 경우 api.echoeco.shop 으로 들어오는 요청은 여기를 타게 됩니다.

<br>

<img width="850" alt="" src="https://github.com/user-attachments/assets/36df0f3d-21c7-4890-a614-591f53ffacfe">

규칙 작업 정의에서 

- 라우팅 액션
  - 대상 그룹으로 전달
- 대상 그룹
  - 아까 만든 대상 그룹 선택

으로 세팅하고 **다음** 버튼을 눌러줍니다.

<br>

<img width="830" alt="" src="https://github.com/user-attachments/assets/7b1e176b-1a08-4282-b93e-7c2a93bef0e4">

우선 순위에 1 을 입력하고 **다음** 버튼을 눌러줍니다.

<br>

<img width="840" alt="" src="https://github.com/user-attachments/assets/045c4f9b-40ca-4891-8650-c763391892b3">

그 다음 페이지에서 바로 **생성** 버튼을 눌러줍니다.

<br>

<img width="1381" alt="" src="https://github.com/user-attachments/assets/46e5bb92-a4b5-403a-963b-97035f7bdba1">

잘 생성이 되었다면 위처럼 2개의 규칙이 존재하는 것을 보실 수 있습니다.



<br>

<br>

---

## A 레코드 추가하기

A 레코드는 **특정 도메인 요청이 왔을때 IP 주소로 반환**하는 역할을 하지만 아마존에서는 아까 설정한 **로드 밸런서로 이어주는 역할**을 하고 있습니다.

<img width="1116" alt="" src="https://github.com/user-attachments/assets/20f5cb1f-2504-4aa2-8dd8-7faf6c8f2848">

- 레코드 이름
  - 저는 루트 도메인에 대한 레코드 생성이라 비워뒀습니다.
- 레코드 유형
  - A 레코드
- 별칭 체크
- 트래픽 라우팅 대상
  - Application/Classic Load Balancer 에 대한 별칭
  - 아시아 태평양(서울)
  - 아까 설정한 로드 밸런서 선택

이후 레코드 생성을 통해 A 레코드를 만들어줍니다.

<br>

<br>

---

## 테스트 및 정리

<img width="158" alt="" src="https://github.com/user-attachments/assets/9febc787-0978-4a28-9f39-3a1d55f77080">

이제 https 로 요청하면 다음과 같이 200 OK 로 응답하는 것을 알수 있습니다.

<br>

정리하면

- 도메인 구매 ( 가비아 이용 )
- 아마존의 route 53 을 통해 호스팅
  - ns (네임서버)를 가비아에서 아마존으로 변경
- 아마존에서 가비아에서 산 도메인으로 인증서 발급
  - 가비아에 CNAME 으로 CA 에 나의 도메인 인증 => 발급
- 443 으로 들어올 수 있게 보안그룹 변경
- 로드밸런서를 사용하기 위한 타겟 그룹 선작업
  - EC2 인스턴스를 사용
- 로드 밸런서 세팅
  - 443 과 8080 으로 들어오는 요청들을 인스턴스로 이어주도록 규칙 세팅
- A 레코드 추가로 aws-ns 로 요청이 들어왔을 때 아마존에서는 로드 밸런서 연결해주도록 세팅

입니다. 

<br>

<br>

---



# 프로젝트 세팅하기 시리즈

[프로젝트 세팅하기](/project-setting/home)



<br>

<br>

---

# 참고

[https://woojin.tistory.com/93](https://woojin.tistory.com/93)





