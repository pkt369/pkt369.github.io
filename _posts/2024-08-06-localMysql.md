---
title: "local 에서 MYSQL 사용하기"
excerpt: "로컬에서 docker 를 이용해서 MYSQL 사용하기"

categories:
  - project-setting
tags:
  - [backend, project, project setting, setting, mysql, local]

permalink: /project-setting/mysql/local

toc: true
toc_sticky: true
published: true

date: 2024-08-06
last_modified_at: 2024-08-06


---



# local 에서 MYSQL 사용하기

본인의 경우에는 팀프로젝트를 진행하였기때문에 docker 를 docker-compose 를 작성하여 팀원들과 공유하여 사용했습니다.

그래서 docker compose 로 세팅하는 방법을 알아보겠습니다.

<br>

## docker-compose

먼저 도커 컴포즈에서 다음과 같이 작성합니다.

localstack 은 오픈소스로 만들어진 프레임워크입니다. localstack 을 이용하면 클라우드 환경을 로컬에서 직접 구현이 가능하며, 테스트 할 수 있습니다.

<br>

```dockerfile
services:
  mysql:
    image: mysql:8.0
    container_name: mysql_container
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3306:3306"
    volumes:
      - ./db/mysql/data:/var/lib/mysql
      - ./db/mysql/init:/docker-entrypoint-initdb.d
```

- ports: 3306 으로 데이터베이스가 보통 사용하는 포트를 사용하고 있습니다.
- environment: 여기에는 비밀번호같이 필요한 설정을 적습니다. 
- volumes: 데이터베이스를 실행할때 init 할 데이터와 도커를 끄더라도 데이터가 남도록 하는 설정입니다.

<br>

`docker-compose up -d`  으로 실행한 뒤 `docker ps` 명령어로 세팅한 서비스가 정상 작동하고 있는지 확인합니다.

<img width="1352" alt="image" src="https://github.com/user-attachments/assets/cb8be1b0-d2ff-4959-9cd5-9ca548af32b8">

<br>

<br>

---





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

<br>



---

## 스프링 서버 세팅하기

```gradle
implementation 'mysql:mysql-connector-java:8.0.32'
```

Gradle 에 먼저 위와 같이 작성합니다.

<br>

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/사용하는 데이터베이스
spring.datasource.username=root
spring.datasource.password=root
```

그리고 다음과 같이 application-local.properties 에 작성합니다.

> local 에 작성했기때문에 실행시 profile 에 local 이 들어가야 합니다.
>
> <img width="796" alt="" src="https://github.com/user-attachments/assets/0ce1ac2a-9d5d-4f74-a5e6-a2412c3be2bc">



위 설정으로 MYSQL 세팅이 완료되었습니다.

<br>

<br>

---



# 프로젝트 세팅하기 시리즈

[프로젝트 세팅하기](/project-setting/home)



<br>

<br>





