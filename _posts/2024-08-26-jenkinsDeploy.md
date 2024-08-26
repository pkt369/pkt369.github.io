---
title: "Jenkins Pipeline 으로 빌드하고 배포하기"
excerpt: "Amazon 의 EC2 서버에서 젠킨스 파이프라인으로 빌드하고 배포하기"

categories:
  - project-setting
tags:
  - [backend, project, setting, ec2, jenkins, build, deploy, pipeline]

permalink: /project-setting/jenkins/deploy

toc: true
toc_sticky: true
published: true

date: 2024-08-26
last_modified_at: 2024-08-26


---



# Jenkins pipeline 으로 빌드하고 배포하기

[젠킨스 세팅하고 웹훅 설정하기](/project-setting/jenkins/webhook/setting) 의 다음 포스팅으로 이전 작업을 먼저 진행해주셔야 이번 포스팅 따라하기가 원할합니다.

<br>

---

## 

## pipeline 방식

Jenkins pipeline 은 배포과정을 자동화하는 일련의 단계를 정의한 워크플로우입니다. groovy 언어로 작성하고,이전 step 이 완료되어야 다음 step 이 실행되어 빌드, 테스트 등 여러 작업을 순차적으로 실행할 수 있습니다.

파이프 라인을 **Declarative Pipeline**과 **Scripted Pipeline** 두가지 방식으로 작성할 수 있습니다.

<br>

### Declarative Pipeline

Declarative Pipeline 은 yaml 과 비슷한 방식으로, 구조가 명확하고 읽고 쓰기 쉽습니다.

또한 Declarative Pipeline 은 groovy 언어를 기반으로 하지만, 젠킨스 파이프라인을 정의할 때는 **필요한 필수적인 구문만 구조를 미리 정해둔 형태**로 사용합니다.

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Deploy') {
            steps {
                sh 'scp my-app user@server:/path/to/deploy'
            }
        }
    }
}
```

<br>

### **Scripted Pipeline**

Scripted Pipeline 는 **groovy 언어를 전체를 사용**하여 복잡한 로직을 유연하게 구현할 수 있습니다.

```groovy
node {
    stage('Build') {
				sh './gradlew clean build'
    }
    stage('Test') {
        sh './gradlew test'
    }
    stage('Deploy') {
        sh 'scp my-app user@server:/path/to/deploy'
    }
}
```

<br>

<br>

---

## Pipeline 작성하기

파이프라인은 Declarative Pipeline 방식으로 작성해보겠습니다.

```groovy
pipeline {
    agent any

    environment {
        JAR_NAME = 'echoeco-0.0.1-SNAPSHOT.jar'
        CONFIG_CREDENTIAL_ID = 'application-prod-properties'
        CONFIG_CREDENTIAL_TEST_ID = 'application-test-properties'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                deleteDir()
            }
        }
        stage('Clone Repository') {
            steps {
                git branch: 'master',
                url: 'https://github.com/swyp-lucky7/echo-eco.git'
            }
        }
        stage('Create Config in workspace') {
            steps {
                withCredentials([file(credentialsId: CONFIG_CREDENTIAL_ID, variable: 'CONFIG_FILE')]) {
                    sh "cp $CONFIG_FILE ${WORKSPACE}/src/main/resources/application-prod.properties"
                }

                withCredentials([file(credentialsId: CONFIG_CREDENTIAL_TEST_ID, variable: 'CONFIG_FILE')]) {
                    sh "cp $CONFIG_FILE ${WORKSPACE}/src/test/resources/application-test.properties"
                }
            }
        }
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // 파일 전송 및 명령 실행
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'ec2 server',
                                verbose: true,
                                transfers: [
                                    sshTransfer(
                                        cleanRemote:false,
                                        sourceFiles: 'build/libs/echoeco-0.0.1-SNAPSHOT.jar',
                                        removePrefix: 'build/libs',
                                        remoteDirectory: '/project/deploy/'
                                    ),
                                    sshTransfer(
                                        execCommand: 'bash /home/ec2-user/project/deploy/deploy.sh'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/build/libs/*.jar', allowEmptyArchive: true
            junit 'build/test-results/**/*.xml'
        }
    }
}
```

먼저 environment 를 보시면 CONFIG_CREDENTIAL_ID, CONFIG_CREDENTIAL_TEST_ID 가 있는데 젠킨스 global setting 값으로 바로 뒤에 서술할 예정입니다.

<br>

- **Cleanup Workspace**
  - deleteDir 함수는 기존 workspace [ ex) /home/ec2-user/project/docker/jenkins/data/workspace/echo-eco-v2 ] 폴더 내부를 초기화 해주는 함수입니다.
- **Clone Repository**
  - 깃 프로젝트와 연결하는 지점이며, 해당 깃 프로젝트에서 master 브랜치로 git clone 명령어를 사용하는 부분입니다.
- **Create Config in workspace**
  - 우리가 다운로드 받은 프로젝트에는 config 파일의 중요한 정보때문에 config 파일이 프로젝트에 들어가있지 않습니다. ( gitignore 로 제외 )
    따라서 그 파일을 직접 넣어주는 부분입니다.
- **Build**
  - gradlew 을 사용해서 Build 하는 부분입니다.
  - clean 옵션을 사용하면 build 폴더가 삭제됩니다 
    - 위에서 deleteDir 함수를 이용했기 때문에 이 옵션을 삭제하고 진행해도 아무 이상없습니다.
- **Deploy**
  - ssh 를 통해서 파일을 서버로 옮기고, 배포까지 하는 방식입니다.
  - 밑에서 자세히 서술할 예정입니다.

<br>

<br>

---

## 환경설정하기

위에서 언급했듯이 깃 프로젝트를 다운로드 받았을때 프로젝트에서 gitignore 를 사용해서 제외시켰기에 중요한 파일들이 없습니다.

그래서 빌드하기전에 미리 중요한 파일들을 넣어줘야 합니다.

<img width="1690" alt="" src="https://github.com/user-attachments/assets/285e8875-8a49-4459-9b39-fe796d1db991">

Jenkins 관리 > Credentials 를 눌러줍니다.

<br>

<img width="1476" alt="" src="https://github.com/user-attachments/assets/cd840368-22e1-438b-933b-909472ddca8c">

그다음 global 을 눌러줍니다.

<br>

<img width="1480" alt="" src="https://github.com/user-attachments/assets/4d6726a1-db97-47ea-89ad-6acbcb3af293">

그 다음 Add Credentials 버튼을 눌러줍니다.

<br>

<img width="1488" alt="" src="https://github.com/user-attachments/assets/ba5b52a9-4c08-42b1-bc83-b40d44cc3425">

- **Kind**
  - Secret file
- **File**
  - 사용할 파일
- **ID**
  - 파이프라인에서 사용할 이름 ( CONFIG_CREDENTIAL_ID )

그 다음 Create 버튼을 눌러 저장해줍니다.

아까 파이프라인을 참조해 이름을 변경해 사용하시면 됩니다.

<br>

<br>

---

## 배포 스크립트 작성하기

배포 스크립트는 젠킨스에서 ssh 를 통해 서버와 연결하고 스크립트를 실행시켜 서버가 해당 스크립트를 직접 실행시키도록 하기 위해 작성하는 것입니다.

배포 스크립트를 작성하기 전에 먼저 폴더를 만들어 보겠습니다.

```bash
$ cd ~/project
$ mkdir -m 755 deploy
```

저의 경우 project 하위에 만들었으며, 폴더를 만듦과 동시에 권한을 부여해주었습니다.

<br>

그 다음 다음 명령어를 입력해 deploy.sh 스크립트를 만들어보겠습니다.

```bash
vi deploy.sh
```

위 명령어를 입력하면 vi 창이 나올텐데 밑의 명령어를 복사 붙혀 넣기합니다.

```bash
echo "start deploy.sh"
pkill -f "java -jar" || true  # 기존 애플리케이션 종료
if [ $? -eq 0 ]; then
        echo "old application stopped successfully"
else
        echo "Failed to stop the old application" >&2
fi

nohup java -jar /home/ec2-user/project/deploy/echoeco-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod > /dev/null 2>&1 &
if [ $? -eq 0 ]; then
        echo "New application started successfully"
else
        echo "Failed to start the new application" >&2
        exit 1
fi

echo "Deployment finished"
```

- **pkill -f "java -jar" || true**
  - 기존 서버에 떠 있는 프로젝트를 종료시키는 역할입니다.
  - || true 옵션을 사용해서 종료할 프로젝트가 없더라도 0 을 반환합니다.
- **nohup java -jar /home/ec2-user/project/deploy/echoeco-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod > /dev/null 2>&1 &**
  - **nohup**
    - no hang up 의 줄임말로, ssh 가 종료되어도 해당 프로세스를 계속 실행시킬때 사용합니다.
  - **java -jar /home/ec2-user/project/deploy/echoeco-0.0.1-SNAPSHOT.jar**
    -  Java 애플리케이션을 실행할 때 사용하는 명령어입니다.
  - **--spring.profiles.active=prod**
    - profile 을 prod 로 설정하는 부분입니다. prod 로 설정되어 있으면 application-prod.properties 를 읽을 수 있습니다.
  - **> /dev/null**
    - 표준 출력은 리디렉션하는 명령입니다. /dev/null 은 아무것도 기록하지 않는 특수 파일로, 출력 내용을 무시하고 삭제합니다.
    - 따라서 아무것도 기록하지 않겠다는 명령어입니다.
  - **2>&1**
    - 2 는 표준 오류 출력을 의미하고, 2>&1는 표준 오류출력을 1(표준 출력) 으로 리디렉션하는 명령어입니다.
    - 따라서 오류 출력도 기록하지 않겠다는 명령어입니다.
  - **&**
    - 명령을 백그라운드에서 실행시켜라는 명령어입니다.
    - 터미널이 제어권을 가지게되고, 사용자가 다른 작업을 할 수 있습니다.

- **$? -eq 0**
  - 리눅스에서 $? 는 직전에 사용된 명령어의 종료 상태를 의미합니다.
  - -eq 는 == 으로 같은지 확인합니다.
  - 0 은 성공했을 때 반환하는 응답값입니다.
  - 정리하면 이전 명령이 성공이 되었는지 확인하는 명령어입니다.

<br>

<br>

---

## 웹서버로 젠킨스 SSH 세팅하기

해야 할 작업은 젠킨스가 웹 서버에 접근할 수 있도록 ssh key 를 발급하고, 서버에 pub 을 등록하는 부분입니다.

그리고 젠킨스에서 ssh 플러그인을 설치하고, 설정에서 젠킨스가 웹 서버로 접근할 수 있도록 등록해야 합니다.

먼저, jenkins 서버에 접근합니다.

저의 경우 docker 로 실행시키고 있기때문에 다음 명령어로 접근하였습니다.

```bash
docker exec -it jenkins-master /bin/bash
```

<img width="1369" alt="" src="https://github.com/user-attachments/assets/8ac97255-8c86-4bf7-bc6a-a7e99cd6082c">

<br>

<img width="775" alt="" src="https://github.com/user-attachments/assets/fb91a954-3575-4295-a517-4221cff94718">

그 다음에 .ssh 폴더를 만들어주고, 다음 명령어를 통해 ssh key 를 만들어줍니다.

```bash
$ mkdir -m 755 .ssh
$ cd .ssh/
$ ssh-keygen -t rsa -b 4096 -C "deploy-key-for-ec2"
```

위 명령어를 입력하면 어떻게 파일을 저장할지 나오는데 저는 .ssh 폴더안에 deploy-key-for-ec2 라는 이름으로 사용했습니다.

**참고로 id_rsa 가 기본값입니다. 만약 기본값을 사용하지 않으면 ssh 접근시 -i 옵션으로 이름을 직접 입력해줘야 합니다.**

비밀번호는 입력하지 않고 바로 엔터, 엔터로 만들어줍니다.

> 비밀번호를 입력하면 접속할때마다 비밀번호를 입력해줘야하는 불편함이 존재하고, 젠킨스 서버가 해킹당하지 않는한 안전합니다.

<br>

<img width="397" alt="" src="https://github.com/user-attachments/assets/6447570e-ee17-4d96-b8cf-00edfd781a19">

```bash
$ cat deploy-key-for-ec2.pub
```

cat 명령어를 통해 공개키를 복사합니다. 참고로 공개키에서 **ssh-rsa 부터 deploy-key-for-ec2 까지 모두 복사**해주시면 됩니다.

<br>

이제 웹 서버( ec2 ) 로 넘어가줍니다. 

```bash
$ exit
```

저의 경우 docker 로 접근해 있음으로 간단하게 exit 명령어로 나와줍니다.

<br>

```bash
$ echo <위에서 복사한 공개키> >> ~/.ssh/authorized_keys
```

그 다음 복사한 공개키를 authorized_keys 에 등록해줍니다. 

> authorized_keys 에 등록하는 이유는 간단하게 설명해서 개인키로 서명(암호화)한 것을 등록한 공개키로 복호화할때 사용하기 위함입니다.

<br>

<img width="869" alt="" src="https://github.com/user-attachments/assets/24890420-0533-4d1e-bd99-d2964d062296">

다 등록이 되었으면 젠킨스에서 ssh 로 웹서버에 들어가봅니다.

```bash
$ ssh -i deploy-key-for-ec2 ec2-user@ec2-<ip 주소>-ap-northeast-2.compute.amazonaws.com
```

참고로 처음 들어가는 거면 known_list 가 아니라서 `Are you sure you want to ~` 이렇게 뜨는데 `yes` 를 입력해주시면 됩니다.

그럼 다음과 같이 ec2 에 연결된 것이 보이는데 이렇게 되면 성공적으로 끝난 것 입니다.

<br>

<br>

---

## 젠킨스에서 SSH Server 설정하기

### 플러그인 설치하기

<img width="1694" alt="" src="https://github.com/user-attachments/assets/627d20ae-6828-4df9-852f-343599012455">

Jenkins 관리 > Plugins 를 눌러줍니다.

<br>

<img width="1552" alt="" src="https://github.com/user-attachments/assets/b98379e0-ef50-4d10-8c5e-34b84ca36d25">

Available plugins > Publish Over ssh 검색 후 설치해줍니다.

저는 이미 설치되어 있기때문에 위처럼 Installed plugins 에 있습니다.

<Br>

<br>

### SSH Server 세팅하기

<img width="1232" alt="" src="https://github.com/user-attachments/assets/7550e239-cd1f-46b7-869e-a4eeaf51ae2b">

Jenkins 관리 > System 을 눌러줍니다.

<br>

<img width="1443" alt="" src="https://github.com/user-attachments/assets/89e8b556-3c59-48b9-9011-e02358a71c0c">

ssh server 파트에서 추가 버튼을 누르면 위 화면이 뜨는데 다음과 같이 설정해주시면 됩니다.

- **Name**
  - 이 부분은 파이프라인에서 입력한 **configName** 과 일치해야 합니다.
- **Hostname**
  - hostname 은 ssh 접근할때 사용하는 ip 주소를 입력해주시면 됩니다.
- **Username**
  - ssh 접근할때 사용할 유저를 입력해주시면 됩니다.

**그 다음 고급을 눌러 다음과 key 의 경로를 입력해줍니다. ( key 에 직접 입력해도 됩니다. )**

<br>

<img width="1388" alt="" src="https://github.com/user-attachments/assets/f03bf3f2-3dba-49c4-b87a-ee28a951cdd7">

그리고 **Test Configuration 버튼을 눌러 Success** 가 나오는지 확인해줍니다.

그리고 저장을 눌러줍니다.

<br>

<br>

---

## 테스트하기

긴 설정이 끝났습니다!! 따라오느라 고생많으셨습니다.

이제 모든 설정이 끝났음으로 자동으로 CD (Continous Deploy) 가 되는지 확인해야 합니다.

<img width="485" alt="" src="https://github.com/user-attachments/assets/e2f0b08a-644c-4f9b-9a84-4754e06c74be">

master 에서 commit 을 남깁니다.

<br>

<img width="341" alt="" src="https://github.com/user-attachments/assets/8b69f480-2515-4bd2-938b-21baa95831b3">

그럼 웹훅을 통해 자동으로 빌드가 되는 것을 확인할 수 있습니다

<br>

<img width="1685" alt="" src="https://github.com/user-attachments/assets/3bb309d5-f4fb-4794-a132-c4104f108578">

빌드가 다되면 밑에 **#숫자** 를 눌러 detail 로 들어가줍니다.

<br>

<img width="889" alt="" src="https://github.com/user-attachments/assets/07b0d514-036e-4f19-a755-a41868c438c8">

그 다음 Console output 을 누르면 다음과 같이 정상적으로 잘 작동하고 있는 것을 볼 수 있습니다.

<br>

<img width="323" alt="" src="https://github.com/user-attachments/assets/59668e4f-9491-41ae-a319-7c45e01210e7">

그리고 보시면 ssh 로 파일을 잘 전달하는 것을 볼 수 있습니다.

<br>

<img width="722" alt="" src="https://github.com/user-attachments/assets/bb81e47c-fc34-4fd3-99e0-7010537e9b48">

그리고 명령이 잘 실행이 되었고 다음 명령어로 서버가 잘 띄어져 있는지 확인할 수 있습니다.

```bash
$ lsof -i :8080
or
$ ps aux | grep "java -jar"
```

<img width="540" alt="" src="https://github.com/user-attachments/assets/d6830aad-0cb3-4103-9f8e-9ef1163b6c88">



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





