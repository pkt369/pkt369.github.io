---
title: "java 체크 예외(checked Exception)와 언체크 예외(Unchecked Exception)"
excerpt: "checked Exception 과 Unchecked Exception의 차이"

categories:
  - JAVA
tags:
  - [java, unchecked, checked, exception]

permalink: /JAVA/checked-unchecked-exception/

toc: true
toc_sticky: true

date: 2023-05-14
last_modified_at: 2023-05-14
---

## 에러
자바에서 에러는 Error 와 Exception 으로 나눠지는데 이 둘은 Throwable 객체를 상속받습니다. <br>
![error-screen](/assets/images/posts_img/java-checked-unchecked-exception/error-screen.png) <br>
![exception-screen](/assets/images/posts_img/java-checked-unchecked-exception/exception-screen.png) <br>
<br>
<br>

### Error
Error 는 주로 JVM 에서 발생하는 에러로 <span style="color:red">애플리케이션 코드에서 사용하면 안되는 에러</span>입니다. <br>
<br>
<br>

### Exception
Exception 은 error 와 반대로 <span style="color:red">애플리케이션 코드에서 예외가 발생</span>했을 때 사용합니다. <br>
Exception 은 checked Exception 과 unchecked Exception, 2가지로 나눠집니다. <br>
<br>

#### checked Exception
checked Exception 는 Exception 을 상속받는 예외 클래스입니다. <br>
체크 예외는 복구가 가능한 예외로 **반드시** 예외처리를 해야합니다. 예외 처리를 하지않으면 컴파일 에러가 발생합니다. <br>
![IOException-checked](/assets/images/posts_img/java-checked-unchecked-exception/IOException-checked.png) <br>
<br>
<br>

#### unchecked Exception
unchecked Exception 는 Runtime Exception 을 상속하는 예외 클래스입니다. <br>
언체크 예외는 checked 와 달리 컴파일 시점에 에러가 발생하지 않고 <span style="color:red">런타임 시점</span>에 에러가 발생하는 예외입니다.<br>
![IllegalException-unchecked](/assets/images/posts_img/java-checked-unchecked-exception/IllegalException-unchecked.png) <br>
<br>
<br>

## checked Exception 과 unchecked Exception 의 차이

|        비교        | checked Exception | unchecked Exception |
|-------------------| ----------------- | ------------------- |
| 에러 시점 | 컴파일 | 런타임
| 예외 발생 시 트랜잭션 처리 | roll-back X | roll-back O |
| 대표 예시 | - IOException<br> - SQLException<br> | - NullPointerException<br> - IllegalArgumentException<br> - IndexOutOfBountException |



