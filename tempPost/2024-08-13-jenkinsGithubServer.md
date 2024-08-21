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



# Jenkins 빌드하고 배포하기

[이전 버전](/)

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





