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

date: 2024-08-09
last_modified_at: 2024-08-09


---



# GABIA 에서 산 도메인 EC2 에서 HTTPS 연결하기

GABIA 에서 도메인을 사는 과정은 생략하고 어떻게 EC2 인스턴스와 가비아를 연결하고 HTTPS 로 사용할 수 있는지에 대한 포스팅을 시작하겠습니다.

포스팅의 순서는 다음과 같습니다.

> 가비아에서 산 도메인을 Amazon 에 인증하기 > SSl(TLS) 인증받기 > 타겟 그룹 생성하기 > Load Balancer 생성 > 리스너 규칙 생성하기 > Health Check

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







## 데이터베이스 만들기

데이터베이스를 만들기 위해 도커로 MYSQL 을 접속을 합니다.

```bash
docker exec -it mysql_container /bin/bash
```

위 명령어를 입력하면 mysql 로 접근이 됩니다.

<br>

```bash
mysql -uroot -p
```

이후 다음 명령어를 입력하면 password 를 적으라고 나오는데 위에서 설정한 비밀번호를 입력하시면 됩니다.

ex) root

<br>

<img width="1338" alt="" src="https://github.com/user-attachments/assets/a8d9fcbc-58ce-49af-b80e-bd2173f73c11">

그럼 다음과 같이 `mysql>` 이 나오게 됩니다.

<br>

우리는 데이터베이스를 먼저 만들어야 하는데 다음 명령어를 작성합니다.

```sql
CREATE DATABASE IF NOT EXISTS eco;
```

eco 부분에는 원하는 데이터베이스 이름을 넣어 사용하면 됩니다.

<br>

<img width="181" alt="" src="https://github.com/user-attachments/assets/ebce1f88-cc2c-42da-bbfc-c8218fbcb92d">

```sql
show databases;
```

명령어를 통해 eco 데이터베이스가 만들어진 것을 볼 수 있습니다.

<br>

로컬과 똑같은 구조로 로컬이나 실서버가 변경이 될때 같이 변경이 되면 됨으로 **개발 및 테스트 환경 일치** 측면에도 이점이 있습니다.

<br>

<br>



---



# 프로젝트 세팅하기 시리즈

[프로젝트 세팅하기](/project-setting/home)



<br>

<br>





