---
title: "프로젝트 세팅하기 시리즈"
excerpt: "프로젝트 시작할때 필요한 환경 세팅에 관련된 시리즈입니다."

categories:
  - project-setting
tags:
  - [backend, project, project setting, setting]

permalink: /project-setting/home

toc: true
toc_sticky: true
published: true

date: 2024-07-31
last_modified_at: 2024-08-02

---

# 포스팅 계기

스위프 5기에 참여하게 되어 새로운 프로젝트를 진행하게 되었습니다. <br>
새로운 프로젝트를 진행하면서 환경세팅에 관련된 부분은 제가 맡게되면서 여기저기 찾아보다가 포스팅을 해야겠다고 생각이 들어 작성하게 되었습니다.
<br>

프로젝트 깃 : [https://github.com/swyp-lucky7/echo-eco](https://github.com/swyp-lucky7/echo-eco)
<br>
<br>

---

# 서비스 아키텍처
먼저 아키텍처는 다음과 같습니다.
<img width="1000" alt="image" src="https://github.com/user-attachments/assets/bd6c12e1-e7b8-4875-8e9b-89d1024399d7">

아마존에서는 EC2, S3 를 사용했고, 특이한 점이라면 데이터베이스를 직접 설치해서 진행했다는 점입니다.<br>
<br>

<br>



---

# 세팅하기

## Amazone

[EC2 설치하기](/project-setting/ec2)

[리눅스 서버 시간 변경](/project-setting/linux-time-change)

[S3 서버 연결하기](/project-setting/s3/setting)

<br>



**o 작성 예정 o**



localstack 으로 S3 서버 대신 사용하기

ec2 에 jenkins 세팅하기

ec2 에 mysql 세팅하기

로컬에 mysql 세팅하기

<br>

<br>



## 코드 세팅

에러 부분 세팅

ApiResponse 세팅

