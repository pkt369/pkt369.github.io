---
title: "EC2 에서 MYSQL 직접 사용하기"
excerpt: "Amazon EC2 환경에서 RDS 가 아닌 직접 도커로 설치해서 진행하기"

categories:
  - project-setting
tags:
  - [backend, project, setting, mysql, ec2, docker]

permalink: /project-setting/mysql/direct-ec2

toc: true
toc_sticky: true
published: true

date: 2024-08-07
last_modified_at: 2024-08-07


---



# EC2 에서 MYSQL 직접 사용하기

EC2 환경에서 보통 Amazon 의 RDS 를 활용해서 데이터베이스를 만들고, EC2 와 연결하는 방법이 가장 표준입니다.

하지만 이번 프로젝트의 경우 최대한 비용을 아끼고자 EC2 에서 docker 를 활용해 MYSQL 을 실행 하였습니다.

<br>

## docker-compose

먼저 도커 컴포즈에서 다음과 같이 작성했습니다.

```dockerfile
services:
  mysql:
    image: mysql:8.0
    container_name: mysql_container
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - TZ=Asia/Seoul  # timezone 설정
    ports:
      - "3306:3306"
    volumes:
      - ./db/mysql/data:/var/lib/mysql
      - ./db/mysql/init:/docker-entrypoint-initdb.d
```

<br>

`docker-compose up -d`  으로 실행한 뒤 `docker ps` 명령어로 세팅한 서비스가 정상 작동하고 있는지 확인합니다.

<img width="1352" alt="image" src="https://github.com/user-attachments/assets/cb8be1b0-d2ff-4959-9cd5-9ca548af32b8">

<br>

<br>



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





