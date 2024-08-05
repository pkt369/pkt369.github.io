---
title: "local 에서 S3 서버 대신 사용하기"
excerpt: "로컬에서 S3 서버 연결하기 - localstack 이용하기"

categories:
  - project-setting
tags:
  - [backend, project, project setting, setting, S3, localstack, local]

permalink: /project-setting/s3/localstack

toc: true
toc_sticky: true
published: true

date: 2024-08-05
last_modified_at: 2024-08-05


---



# local 에서 서버와 S3 연결하기

실서버에서 이미지 서버로 S3 를 사용해서 이전 포스팅에서 AWS S3 와 Spring Server 를 어떻게 연결하는지에 대해 알아봤습니다.

하지만 로컬에서도 실서버와 같은 이미지 서버를 사용할 수 없기에 대신해서 사용하는 것이 **LocalStack** 입니다.

<br>

## localstack

https://www.localstack.cloud/

localstack 은 오픈소스로 만들어진 프레임워크입니다. localstack 을 이용하면 클라우드 환경을 로컬에서 직접 구현이 가능하며, 테스트 할 수 있습니다.

<br>

### docker-compose

localstack 을 실행하기 위해서는 testContainer 과 도커를 이용할 수 있는데 프런트 작업을 하면서 자주 껐다 켰다 하면서 서버가 로딩되는 속도가 느려짐을 확인해서 도커로 localstack 을 띄우기로 하였습니다.

또한 도커로 localstack 을 띄우는데 본인의 경우에는 팀프로젝트라 docker-compose 를 작성해 팀원들과 공유하였습니다.

작성한 코드는 다음과 같습니다.

```dockerfile
services:
  localstack:
    container_name: "localstack"
    image: localstack/localstack:0.14.3
    ports:
      - "4566:4566" # localstack port
      - "4572:4572" # aws s3 port
    environment:
      - SERVICES=s3
      - DEBUG=1
      - DATA_DIR=/tmp/localstack/data
      - DOCKER_SOCK=unix:///var/run/docker.sock
      - AWS_ACCESS_KEY_ID=test
      - AWS_SECRET_ACCESS_KEY=test
      - AWS_DEFAULT_REGION=ap-northeast-2
    volumes:
      - "./localstack:/tmp/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
```

해당 코드의 Access_key 와 Secret_access_key 는 properties 에 적어줘야 하므로 기억해둡니다

<br>

`docker-compose up -d`  으로 실행한 뒤 `docker ps` 명령어로 세팅한 서비스가 정상 작동하고 있는지 확인합니다.

<img width="1352" alt="image" src="https://github.com/user-attachments/assets/cb8be1b0-d2ff-4959-9cd5-9ca548af32b8">

<br>

<br>



---

### 스프링 서버 세팅하기

```gradle
implementation 'org.springframework.cloud:spring-cloud-starter-aws:2.2.6.RELEASE'
implementation 'com.amazonaws:aws-java-sdk-s3:1.12.749'

//test
testImplementation 'cloud.localstack:localstack-utils:0.2.20'
```

Gradle 에 먼저 밑과 같이 작성합니다.

<br>

```properties
## aws
cloud.aws.s3.bucket=test
cloud.aws.stack.auto=false
cloud.aws.region.static=ap-northeast-2
cloud.aws.credentials.access-key=test
cloud.aws.credentials.secret-key=test
```

그리고 다음과 같이 application-local.properties 에 작성합니다.

> local 에 작성했기때문에 실행시 profile 에 local 이 들어가야 합니다.
>
> <img width="796" alt="" src="https://github.com/user-attachments/assets/0ce1ac2a-9d5d-4f74-a5e6-a2412c3be2bc">



<br>

```java
@Profile(value = "local")
@Configuration
public class S3LocalConfig {

    @Value("${cloud.aws.s3.bucket}")
    private String bucket;

    @Value("${cloud.aws.region.static}")
    private String AWS_REGION;
    private final String AWS_ENDPOINT = "http://127.0.0.1:4566";

    @Value("${cloud.aws.credentials.access-key}")
    private String accessKey;
    @Value("${cloud.aws.credentials.secret-key}")
    private String secretKey;

    @Bean
    public AmazonS3 amazonS3() {
        AwsClientBuilder.EndpointConfiguration endpoint = new AwsClientBuilder.EndpointConfiguration(AWS_ENDPOINT, AWS_REGION);
        BasicAWSCredentials credentials = new BasicAWSCredentials(accessKey, secretKey);

        AmazonS3 amazonS3 = AmazonS3ClientBuilder.standard()
                .withEndpointConfiguration(endpoint)
                .withCredentials(new AWSStaticCredentialsProvider(credentials))
                .withPathStyleAccessEnabled(true)
                .build();

        if (!amazonS3.doesBucketExistV2(bucket)) {
            amazonS3.createBucket(new CreateBucketRequest(bucket, AWS_REGION));
        }
        return amazonS3;
    }
}
```

그리고 S3LocalConfig 파일을 통해서 LocalStack 을 이용해 만든 S3 서버 정보를 빈으로 등록합니다.

<br>

```java
@Service
@RequiredArgsConstructor
public class FileService {
    private final AmazonS3 amazonS3;
    @Value("${cloud.aws.s3.bucket}")
    private String bucket;

    public String upload(MultipartFile file) {
        File uploadFile = convert(file);
        String url = uploadToS3(uploadFile);
        return url;
    }

    public File convert(MultipartFile multipartFile) {
        File file = new File(multipartFile.getOriginalFilename());
        try (FileOutputStream fos = new FileOutputStream(file)) {
            fos.write(multipartFile.getBytes());
        } catch (IOException e) {
            throw new RuntimeException();
        }
        return file;
    }

    private String uploadToS3(File uploadFile) {
        try {
            amazonS3.putObject(bucket, uploadFile.getName(), uploadFile);
            return amazonS3.getUrl(bucket, uploadFile.getName()).toString();
        } catch (SdkClientException e) {
            throw e;
        } finally {
            removeNewFile(uploadFile);
        }
    }

    private void removeNewFile(File uploadFile) {
        uploadFile.delete();
    }
}
```

다음은 Service 코드인데 위에서 등록한 AmazonS3 빈을 여기서 불러와 사용하게 됩니다.

<br>

```java
@RequiredArgsConstructor
@RequestMapping("/admin/file")
@RestController
public class S3Controller {
    private final FileService fileService;

    @PostMapping("/upload")
    public ApiResponse<String> upload(@RequestParam("file") MultipartFile file) {
        String url = fileService.upload(file);
        return ApiResponse.success(url);
    }

}
```

추가로 컨트롤러 코드입니다.



<br>

<br>

---



# 프로젝트 세팅하기 시리즈

[프로젝트 세팅하기](/project-setting/home)



<br>

<br>

---



# 참고

[https://techblog.woowahan.com/2638/](https://techblog.woowahan.com/2638/)



