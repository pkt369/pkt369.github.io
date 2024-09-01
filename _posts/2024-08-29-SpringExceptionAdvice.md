---
title: "Spring Exception 관리하기"
excerpt: "Spring Exception 를 RestControllerAdvice 로 관리하기"

categories:
  - project-setting
tags:
  - [backend, project, setting, spring, exception, RestControllerAdvice]

permalink: /project-setting/exception

toc: true
toc_sticky: true
published: true

date: 2024-08-29
last_modified_at: 2024-08-29

---

Spring 에서 에러를 그대로 유저에게 노출시키는건 좋지 않은 방법이며, 에러를 모두 컨트롤할 수 있어야 합니다.

이러한 부분을 Spring 에서는 **RestControllerAdvice annotation** 을 통해서 에러를 관리할 수 있도록 만들어두었습니다.

---

# @ExceptionHandler

먼저 에러를 컨트롤하기위해서는 **@ExceptionHandler** 가 존재합니다.

**@Controller 어노테이션이 존재하는 컨트롤러에서 사용**이 가능하며 각 컨트롤러마다 ExceptionHandler 어노테이션을 사용해 관리할 수 있습니다.

```java
@Controller
public class homeController {
  @ExceptionHandler(value = "Exception.class")
  public ResponseApi<String> errorHandler(Exception e) {
  	return ResponseApi.error(400, e.getMessage()); 
  }
}
```

하지만 위처럼 사용하게 될 경우 **모든 컨트롤러에 각각 함수를 만들어줘야 하는 불편함**이 존재합니다.

그래서 스프링에서는 공통으로 에러를 관리할 수 있는 **RestControllerAdvice** 를 제공합니다.

<br>

---

# @RestControllerAdvice

```java
@RestControllerAdvice
@Slf4j
public class ExceptionControllerAdvice {

    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ExceptionHandler(IllegalArgumentException.class)
    public ApiResponse<String> illegalExHandler(IllegalArgumentException e) {
        log.error("error message: {}", e.getMessage(), e);
        return ApiResponse.res(HttpStatus.BAD_REQUEST.value(), e.getMessage(), "error");
    }
}
```

모든 API 컨트롤러에 대해서 에러를 관리하기 위해 위와 같은 코드를 설정합니다.

- **ResponseStatus**
  - 어떤 **응답값**으로 내려줄지 설정하는 부분입니다.
- **ExceptionHandler**
  - 특정타입의 예외가 발생했을때 이를 처리하는 **메서드를 지정**해서 사용합니다.
  - 괄호를 적지 않으면 특정타을 제외한 에러에 대해 사용하겠다는 의미입니다.
  - 두개 이상을 적어 사용할 수도 있습니다.

> 보통 Logback 과 같이 사용하여 유저에게는 간단한 메세지 형태로, 에러는 log 로 남기는 방식을 많이 채택합니다.

<br>

---

# 프로젝트 세팅하기 시리즈

[프로젝트 세팅하기](/project-setting/home)





