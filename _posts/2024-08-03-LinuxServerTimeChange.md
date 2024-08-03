---
title: "리눅스 서버 시간 변경"
excerpt: "서버에서 서울 시간이 아닌 경우 변경하는 방법"

categories:
  - project-setting
tags:
  - [backend, project, project setting, setting, linux time, time change]

permalink: /project-setting/linux-time-change

toc: true
toc_sticky: true
published: true

date: 2024-08-03
last_modified_at: 2024-08-03

---


# 리눅스 시간 설정
저자의 경우에는 리눅스에서 **시간이 서울보다 9시간 전으로 세팅**이 되어있었고, 그것때문에 12시에 리셋이 안되는 버그가 생겼었습니다.<br>

<br>

디버깅을 하다보니 서버 시간이 9시간 전이라 한국시간 밤 12시가 되어도 리셋이 되지 않는 것이었습니다.

<br>

---



## 현재 시간 확인 방법

### 1. 현재 날짜 확인하기

먼저 현재 날짜가 어떻게 세팅 되어 있는지 다음과 같은 명령어로 확인할 수 있습니다.

```bash
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

<img width="342" alt="" src="https://github.com/user-attachments/assets/82d26f85-b5a5-4115-952b-87828613e05f">



<br>

<br>

### 2. 현재 Time zone 확인하기

현재 Time zone 을 확인할려면 다음 명령어를 입력하면 됩니다.

```bash
timedatectl
```

<img width="323" alt="" src="https://github.com/user-attachments/assets/a37df84e-65bd-43b0-8919-738ce2a61127">

Time zone 을 보면 Asia/Seoul 인데 아닐 경우 변경이 필요합니다.

<br>



---





## 서버 시간 변경하기

### 1. Time Zone 으로 변경하기

```bash
timedatectl set-timezone Asia/Seoul
```

변경 후 다음 명령어 다시 입력

```bash
timedatectl
```



<br>

<br>

### 2. /etc/localtime 심볼릭 링크파일 수정

```bash
sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

변경후 다음 명령어 입력

```bash
sudo cat /etc/localtime
```

그럴 경우 **KST-9** 가 나오면 정상적으로 잘 변경된 것 입니다.



<br>



---



# 참고

[https://gowoonsori.com/blog/settings/timezone/](https://gowoonsori.com/blog/settings/timezone/)

[https://gr-st-dev.tistory.com/215](https://gr-st-dev.tistory.com/215)
