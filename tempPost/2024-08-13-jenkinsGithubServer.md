---
title: "EC2 서버에서 젠킨스 세팅하고 배포하기"
excerpt: "Amazon 의 EC2 서버에서 젠킨스 세팅하고 배포하기"

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



# EC2 서버에서 Jenkins 세팅하고 배포하기

---

## Github 와 젠킨스 연결하기

먼저 젠킨스와 Github 를 연결하기 위해서는 github 에서 토큰을 발급받아야 합니다.

<img width="311" alt="" src="https://github.com/user-attachments/assets/698028c2-c351-4cb5-85cf-bba500ca111c">

github 에서 오른쪽 위 상단의 프로필을 클릭하고, Settings 를 눌러줍니다.

<br>

<img width="309" alt="" src="https://github.com/user-attachments/assets/1f704e03-3335-4e18-8cd8-1766c7e164fd">

그다음 맨 왼쪽 아래 Developer Settings 를 눌러줍니다.

<br>

<img width="1121" alt="" src="https://github.com/user-attachments/assets/580bb189-3f89-45a9-93c5-f323bb202e9d">

Personal access tokens > Tokens(classic) > Generate new token > Generate new token (classic) 을 눌러줍니다.

<br>

<img width="600" alt="" src="https://github.com/user-attachments/assets/9010906b-7fe1-49a7-9b9e-06b4fd6c31e9">

- **Note**:
  -  구분할 수 있는 이름
- **repo**:
  - 젠킨스가 Github repository 를 읽고, 쓰며, 배포 상태를 관리하는 등 리포지토리와 관련된 작업을 수행할 수 있게 합니다.
- **admin:org**:
  - 젠킨스가 조직 내 팀관리, 프로젝트 관리, Github Action Runner 관리 등을 수행할 수 있게 합니다.
  - 그룹을 관리하는 권한이기 때문에 선택에 따라 체크해주시면 됩니다.
- **admin:repo_hook**:
  - Jenkins 가 Github 리포지토리 웹훅을 설정하고, 이 훅을 통해 Jenkins 와 Github 가 통신할 수 있게 합니다. 예를 들어 커밋이 발생하면 자동으로 Jenkins 에서 빌드를 시작하도록 설정할 수 있습니다.

<br>

<img width="788" alt="" src="https://github.com/user-attachments/assets/2ee221e8-a617-4c73-a3fb-9f9c1921687a">

그럼 다음과 같이 키값이 노출되는데 이부분을 복사합니다.

<br>

<img width="1694" alt="" src="https://github.com/user-attachments/assets/87ba61fa-f68b-4a26-acb8-f3268719df2c">

젠킨스로 접속하여 Jenkins 관리 > System 을 눌러줍니다.

<br>

<img width="289" alt="" src="https://github.com/user-attachments/assets/04adc491-c76e-4673-991b-bf6cc9492a47">

그럼 설정창이 나올텐데 command + F 로 Git 을 검색해서 해당 설정을 찾습니다.

그다음 **Add GitHub Server > GitHub Server** 을 눌러줍니다.

<br>

<img width="1437" alt="" src="https://github.com/user-attachments/assets/ccedcf74-6055-41c8-89ec-7a80f20541e9">

그럼 다음과 같이 나오는데 Name 에는 식별할 수 있는 이름, 그다음 **Add > Jenkins** 를 눌러줍니다.

<br>

<img width="1331" alt="" src="https://github.com/user-attachments/assets/217ceeb7-c710-44e2-9af8-38fd4e3c6e99">

- **Domain**
  - Global credentials 선택
- **Kind**
  - Secret text 선택
- **Scope**
  - Global 선택
- **Secret**
  - 아까 Github token 키 값 붙혀넣기
- **ID**
  - 내가 식별할 수 있는 이름

이후 **ADD 버튼**을 눌러 저장해줍니다.

<br>

<img width="1436" alt="" src="https://github.com/user-attachments/assets/482e7389-b480-4159-bc71-3382c3ee63e6">

- **Credentials**
  - 방금 만든 설정
- **Manage hooks**
  - 체크

그 다음으로 **Test connection** 을 눌러 `Credentials verified for user <git id>` 가 뜨는 지 확인합니다.

확인이 되었다면 **저장**을 눌러줍니다.

<br>

정리하면 위 세팅을 하게되면 젠킨스에서 토큰에 넣은 권한을 가지고 private repository 에 접근하거나, 웹훅을 관리하는 등 여러가지 작업을 알아서 해주게 됩니다.

<br>

<br>

---

## Jenkins Job 생성하기

Jenkins Job 은 Git 의 repository 를 pull 받고 테스트 하는 등 역할을 부여합니다.

<img width="334" alt="" src="https://github.com/user-attachments/assets/2004f713-c711-407f-b26d-728e0fdcc910">

먼저 새로운 Item 을 눌러줍니다.

<br>

<img width="971" alt="" src="https://github.com/user-attachments/assets/037fae2b-1da0-48c8-9d77-707524c47507">

그러면 위 화면같이 나올텐데 구분할 수 있는 식별 이름과, Pipeliine 을 선택해줍니다.

> 요즘은 Freestyle project 로 많이 진행하는 추세입니다만 pipeline 은 작성해둔 스크립트만 붙혀넣으면 더 간단하므로 pipeline 을 선택하였습니다.

<br>

<img width="1156" alt="" src="https://github.com/user-attachments/assets/12a40de7-0b9a-4b9b-9349-a737ead9d7bb">

그러면 다음 페이지가 나오는데 GitHub project 에 github 프로젝트 url 을 붙혀넣어줍니다.

<br>

<img width="354" alt="" src="https://github.com/user-attachments/assets/51ebe83c-1fe0-46f2-8b6e-e029f921efe9">

그다음 밑에 있는 Build Triggers 에는 **GitHub hook trigger for GITScm polling** 을 클릭하여 줍니다.

이부분은 github 에서 webhook 을 통해서 trigger 하면 자동으로 빌드가 되도록 할 수 있습니다.

<br>

```groovy
pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/jenkins_home/deploy/'
        JAR_NAME = 'echoeco-0.0.1-SNAPSHOT.jar'
        CONFIG_CREDENTIAL_ID = 'application-prod-properties'
        CONFIG_CREDENTIAL_TEST_ID = 'application-test-properties'
    }

    stages {
        stage('Check Branch') {
            when {
                branch 'master'
            }
            steps {
                echo "Building for branch: ${env.BRANCH_NAME}"
            }
        }
        stage('Cleanup Workspace') {
            steps {
                deleteDir()
            }
        }
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/swyp-lucky7/echo-eco.git'
            }
        }
        stage('Create Config') {
            steps {
                // Credentials에서 파일 내용 읽기
                sh "chmod 777 ${WORKSPACE}/src/main/resources"
                withCredentials([file(credentialsId: CONFIG_CREDENTIAL_ID, variable: 'CONFIG_FILE')]) {
                    sh "cp $CONFIG_FILE ${WORKSPACE}/src/main/resources/application-prod.properties"
                }
                
                sh "chmod 777 ${WORKSPACE}/src/test/resources"
                withCredentials([file(credentialsId: CONFIG_CREDENTIAL_TEST_ID, variable: 'CONFIG_FILE')]) {
                    sh "cp $CONFIG_FILE ${WORKSPACE}/src/test/resources/application-test.properties"
                }
            }
        }
        stage('Build') {
            steps {
                sh 'chmod +x gradlew'
                sh './gradlew clean build'
            }
        }
        stage('moveJar') {
            steps {
                script {
                    // 현재 날짜를 가져와서 형식화
                    def date = new Date().format('yyyyMMdd')
                    // DEPLOY_JAR_NAME 변수에 날짜 포함
                    env.DEPLOY_JAR_NAME = "app-${date}.jar"

                    sh "mkdir -p ${DEPLOY_DIR}"
                    sh "cp build/libs/${JAR_NAME} ${DEPLOY_DIR}${DEPLOY_JAR_NAME}"
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



<br>

<br>

---

## 환경 변수 세팅하기

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





