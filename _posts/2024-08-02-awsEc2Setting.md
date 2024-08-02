---
title: "프로젝트 세팅하기 시리즈::EC2 설치하기"
excerpt: "AWS 의 EC2 를 설치하는 방법입니다."

categories:
  - project-setting
tags:
  - [backend, project, project setting, setting]

permalink: /project-setting/ec2

toc: true
toc_sticky: true
published: true

date: 2024-08-02
last_modified_at: 2024-08-02

---


# Amazon EC2
아마존에서 제공하는 EC2 는 Amazon Elastic Compute Cloud 의 줄임말로 **클라우드 컴퓨팅**입니다. 

다른 말로는 **가상 컴퓨터**라고도 불립니다. 

<br>

즉, 우리가 직접 컴퓨터를 구매하고 운영하는게 아닌 아마존의 컴퓨터를 돈을 주고 이용하는 시스템입니다. 

<br>

---



## 인스턴스 생성하기

인스턴스를 생성하기전에 **서울**로 먼저 지정해야 합니다.

<img width="157" alt="image" src="https://github.com/user-attachments/assets/c82f8a19-a4a4-410c-90c2-7fb0978607da">



바로 EC2 세팅하기 본론을 들어가서 왼쪽의 인스턴스 탭으로 인스턴스로 들어옵니다.

<img width="1715" alt="스크린샷 2024-08-02 오후 4 16 49" src="https://github.com/user-attachments/assets/2ee9372c-bb6e-4e6e-8318-07ba719ea1f7">

들어온 뒤에 오른쪽 위에 있는 **인스턴스 시작**을 누릅니다.

> 인스턴스는 하나의 컴퓨터를 의미합니다.



<br>

<br>



<img width="1216" alt="스크린샷 2024-08-02 오후 4 50 28" src="https://github.com/user-attachments/assets/b60b8184-5477-4627-a62f-0a928f1d70c8">

<img width="774" alt="스크린샷 2024-08-02 오후 4 43 20" src="https://github.com/user-attachments/assets/5074b6ad-c20f-43b9-9d0b-715facfe7ee5">

인스턴스를 시작하면 다음과 같이 페이지가 나오는데 다음과 같이 설정해주시면 됩니다.

- 이름: 자신이 알아볼 수 있는 사용할 아무 이름을 작성하면 됩니다. 
- 애플리케이션 및 OS 이미지: Amazon Linux 2 AMI - SSD Volume Type 프리티어 사용



<br>

<br>

<img width="800" alt="image" src="https://github.com/user-attachments/assets/4ef30974-2714-4048-98d1-2dd4373717e8">

- 인스턴스 유형: t2.micro
- 키페어: 만든 키페어 선택 > 없다면 새 키페어 생성

> 키페어란: 인스턴스에 접근하기 위한 물리적인 인증 키
>
> .pem 이 있어야 자신의 컴퓨터에서 인스턴스로 접근이 가능합니다.



<img width="600" alt="image" src="https://github.com/user-attachments/assets/863c31f7-b808-4565-a36e-ffe2deaef947"> 

- 키 페어 이름: 본인이 알아볼 수 있는 key pair 이름
- 키 페어 유형: RSA
- 프라이빗 키 파일 형식: .pem , 저자의 경우 맥북의 터미널로 접근해서 pem 으로 받아 사용하였습니다.



<br>

<br>



<img width="788" alt="스크린샷 2024-08-02 오후 5 16 48" src="https://github.com/user-attachments/assets/f0f06108-5778-45fb-bc4b-5a2ccc3b6841">

네트워크 설정 부분입니다.

위와 같이 자동으로 설정되어 있는 세팅으로 나두고 다음 탭으로 넘어갑니다.

> VPN: 가상사설망
>
> VPC: Virtual Private Cloud 의 줄임말로, 여러 인스턴스들을 하나의 망으로 묶어주는 역할을 합니다.
> 	VPC 별로 네트워크를 구성할 수 있고, 각각 다르게 네트워크 설정을 할 수 있습니다.
>
> 서브넷: 서브넷은 VPC 보다 좀 더 디테일한 네트워크 세팅입니다. 즉, VPC 안에 서브넷이 들어갑니다.
>
> 
>
> 위처럼 나누는 이유는 더 많은 네트워크 망을 만들기 위함입니다.





<br>

<br>

<img width="795" alt="스크린샷 2024-08-02 오후 5 29 38" src="https://github.com/user-attachments/assets/45cd8df7-2df1-43ae-aeee-ef52872a16b2">

<img width="266" alt="image" src="https://github.com/user-attachments/assets/8ff86357-09f0-40ea-b762-a457f7ddc835">

스토리지는 EBS 라고도 부르며, 저장소를 의미합니다. 

- 크기: 8GB 
- 타입: 범용 SSD 나 마그네틱 사용

저자의 경우에는 크기가 8 기가 바이트로도 많이 부족하여 16GB 로 사용하고 있고, 실제 서비스를 목표로 만드는 프로젝트라 범용 SSD gp2 로 선택하였습니다.



<br>

<br>

<img width="390" alt="스크린샷 2024-08-02 오후 5 37 09" src="https://github.com/user-attachments/assets/8d36925d-b236-404f-ac2b-3e07ffb2dfdd">

요약에서 인스턴스 시작 버튼을 누르면 바로 인스턴스가 생성 되게 됩니다.

<img width="180" alt="image" src="https://github.com/user-attachments/assets/fe1b52b3-d153-4b7a-9fff-e3c217c3183f">





---



## 가상컴퓨터 접속하기

<img width="1442" alt="스크린샷 2024-08-02 오후 5 40 30" src="https://github.com/user-attachments/assets/306f374b-e6cd-4c7f-8cd9-d81d2c1b269e">

다음 처럼 생긴 인스턴스를 클릭하고 연결 버튼을 눌러줍니다.



### 1. 웹으로 접근하기

<img width="837" alt="스크린샷 2024-08-02 오후 5 42 19" src="https://github.com/user-attachments/assets/e7bdbebd-f757-4c3c-9ce6-53a5ab6795aa">

연결을 누르면 접속이 됩니다.



<br>

<br>



### 2. SSH 로 접근하기 ( MAC 환경 )

<img width="857" alt="스크린샷 2024-08-02 오후 8 46 23" src="https://github.com/user-attachments/assets/742b6318-5f5f-43c7-8335-3acf5e4e98e3">

맨밑에 빨간 박스로 쳐져 있는 부분을 "id_rsa" 부분만 수정해서 터미널에서 입력해줍니다.

id_rsa 는 현재 컴퓨터에 저장되어 있는 pem 키를 의미합니다.

ex) ssh -i ~/project/ssh/test.pem ec2-user@ec2-1-11-111-111.ap-northeast-2.compute.amazonaws.com

<br>

<br>



---





## 탄력적 IP 할당하기

탄력적 IP 를 할당해야 하는 이유는 서버를 껏다 킬때마다 퍼블릭 ip 가 변환되기 때문입니다.

그렇기때문에 **고정적인 탄력적 IP 를 할당**받아 사용합니다.

<img width="178" alt="image" src="https://github.com/user-attachments/assets/0e27bc15-4cf2-4253-bae4-47dff09b103b">

네트워크 및 보안 > 탄력적 IP 클릭

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/8c42f425-5226-4466-b550-17d32a4b7f79">

탄력적 IP 주소 할당을 눌러줍니다.

<br>

<br>

<img width="829" alt="스크린샷 2024-08-02 오후 8 58 42" src="https://github.com/user-attachments/assets/f325e755-9b18-4ddf-aac0-554c84f97791">

여기서 할당을 눌러줍니다.

<br>

<br>



<img width="1445" alt="" src="https://github.com/user-attachments/assets/6ddbd2ab-c822-45a4-96df-ea7130d8cb73">

그럼 위처럼 나오게 되고 이전에 만들어둔 인스턴스와 연결해줘야 합니다.

<img width="198" alt="" src="https://github.com/user-attachments/assets/cc7b2c8a-53de-4b3b-b8c9-388567d9aab1">

탄력적 IP 를 선택하고 작업 > 탄력적 IP 주소 연결을 눌러줍니다.

<br>

<br>

<img width="837" alt="" src="https://github.com/user-attachments/assets/171d4e9f-8ec5-4c78-94c2-7792c255342a">

위처럼 인스턴스를 선택하고 인스턴스에서 아까 만든 인스턴스를 넣어준 후 연결을 눌러주면 탄력적 IP 가 연결됩니다.



> 탄력적 IP 는 사용하지 않는 경우 삭제를 꼭 진행해주세요.
>
> 삭제하지 않으면 비용이 계속 나가게 됩니다.

<br>

<br>

---










# 프로젝트 세팅하기 시리즈
[프로젝트 세팅하기](/project-setting/home)



<br>

<br>

---



# 참고

[VPC 개념 잡기](https://medium.com/harrythegreat/aws-%EA%B0%80%EC%9E%A5%EC%89%BD%EA%B2%8C-vpc-%EA%B0%9C%EB%85%90%EC%9E%A1%EA%B8%B0-71eef95a7098#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUyNmQ5MTdiMWZlOGRlMTMzODJhYTdjYzlhMWQ2ZTkzMjYyZjMzZTIiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMDI5OTc3OTM0OTcyMjUwOTYzMzAiLCJlbWFpbCI6InBrdDA3NThAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsIm5iZiI6MTcyMjU4NjQ5NCwibmFtZSI6IuuwleyEuOykgCIsInBpY3R1cmUiOiJodHRwczovL2xoMy5nb29nbGV1c2VyY29udGVudC5jb20vYS9BQ2c4b2NJUm1GdW50OWFxYkx5M3plSEZqbXRTYW9SZkVLZXVQcHhYNGppY0xTeFlxcWFZX2I0PXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6IuyEuOykgCIsImZhbWlseV9uYW1lIjoi67CVIiwiaWF0IjoxNzIyNTg2Nzk0LCJleHAiOjE3MjI1OTAzOTQsImp0aSI6IjYyY2YwMzFlN2FkMzg4YjdlNzRiYTU3ZjA3ZTcyYTY3YTU3NzZhZjgifQ.av_F2st3YuX4I-775uW0sUyYg439ry8osgQPgqENjjd2_Nm6oNpZHcWWnUVvmm6oRFf029lY-ivOzst92ZSa1251oR1LD11bzMt9Ee1DD6fIcC3WR1oYp7AKojJ2FKbSlYJRrBRSZJ1HYIu3pgiFKkItkJmdo_NwNhdfPHFA0FZtMfmVrFkSGpV2P41ZBV37iP2oDgXj7l_dHmfjfQPe43N0xEUcpqXeA7SyXJSLj-289Mkgg3D8pvXtLAnOn0JY-KEppnGEaYkTTLP7iUai8Yktf4x0h2ycc_4ico0oGylAyZJvucLQ-6F_We9uIka-XbhDcQwucVsBczMbEbgrVw)

[Inpa blog - ec2](https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-EC2-%EA%B0%9C%EB%85%90-%EC%82%AC%EC%9A%A9-%EA%B5%AC%EC%B6%95-%EC%84%B8%ED%8C%85-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-EBS-AMI)

