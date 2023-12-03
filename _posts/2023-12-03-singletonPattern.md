---
title: "싱글턴 패턴"
excerpt: "싱글턴 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, singleton]

permalink: /CS/design-pattern/singleton

toc: true
toc_sticky: true

date: 2023-12-03
last_modified_at: 2023-12-03

---

# 싱글턴 패턴

책에서 말하는 싱글턴 패턴은…

> 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을  제공합니다.

제가 생각하는 싱글턴 패턴은…

> 하나의 인스턴스만 만들어 전역으로 제공하는 것입니다.
>

싱글턴 패턴은 스프링 팩토리처럼 이미 우리가 알게 모르게 많이 사용하고 있는 패턴입니다. 😀

<br>

# 싱글턴 패턴 구현

```java
public class Singleton {
  private static Singleton singleton;

  private Singleton() {}
  public static Singleton getInstance() {
    if (singleton == null) {
      singleton = new Singleton();
    }
    return singleton;
  }
}

class Main {
  public static void main(String[] args) {
    Singleton singleton = Singleton.getInstance();
  }
}
```

싱글톤은 위처럼 생성자를 private 하게 만들어 새롭게 인스턴스를 만들지 못하게 만들고, getInstance 함수를 통해 하나의 인스턴스만 가져오는 방식을 말합니다.

특히 위의 방식은 Lazy 방식으로 getInstance 함수를 불러야만 객체가 생성되기 때문에 메모리적인 이점이 존재합니다.

하지만 위의 코드에도 문제점이 존재합니다.

<br>

# 멀티스레드

스레드는 일꾼을 의미합니다. 그렇다면 싱글스레드는 하나의 일꾼으로 이해할 수 있고, 여러명의 일꾼을 멀티스레드라고 이해할 수 있습니다.

멀티 스레드에서 위의 코드에 접근하게 되면 어떻게 진행이 될까요?

<br>

동시에 접근한다면 두개의 Singleton 이 생성될 수 있습니다. 그렇다면 이것은 우리게 생각하지못한 문제가 됩니다.

이것을 해결하기 위해 여러가지 해결점이 존재합니다.

<br>



## 멀티스레드 해결방법

### Synchronized 이용

```java
public class Singleton {
  private static Singleton singleton;

  private Singleton() {}

  public static synchronized Singleton getInstance() {
    if (singleton == null) {
      singleton = new Singleton();
    }
    return singleton;
  }
}
```

synchronized 를 이용하면 **하나의 스레드가 접근했을 때 다음 스레드는 앞에 먼저 온 스레드가 일이 끝날 때 까지 기다렸다가 끝나면 다음 스레드가 이용하는 방식으로 동기방식**이라고 말합니다.

하지만 위처럼 이용하게 되면 멀티스레드의 이점이 전혀 없어, 잘사용하지 않는 방식입니다.

<br>

### 변수에 생성하기

```java
public class Singleton {
  private static Singleton singleton = new Singleton();

  private Singleton() {}

  public static Singleton getInstance() {
    return singleton;
  }
}
```

위처럼 사용하게되면 클래스가 로딩될 때 JVM 에서 바로 생성하게 됩니다.

> 클래스 로딩은 클래스 로더가 .class 파일을 찾고 JVM 에 메모리를 올려놓는 것을 의미합니다. JVM 은 필요할 때 마다 클래스 로더를 이용해 가져와 로딩합니다.
>

클래스 로더는

- 클래스의 인스턴스 생성 (new)
- 클래스의 정적 변수 사용 (final X)
- 클래스의 정적 메소드 호출

시점에서 로딩됩니다.

참고) [https://velog.io/@skyepodium/클래스는-언제-로딩되고-초기화되는가](https://velog.io/@skyepodium/%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%EC%96%B8%EC%A0%9C-%EB%A1%9C%EB%94%A9%EB%90%98%EA%B3%A0-%EC%B4%88%EA%B8%B0%ED%99%94%EB%90%98%EB%8A%94%EA%B0%80)

<br>

### DCL (Double-Checked Locking) 사용

```java
public class Singleton {
  private volatile static Singleton singleton;

  private Singleton() {}

  public static Singleton getInstance() {
    if (singleton == null) {
      synchronized (Singleton.class) {
        singleton = new Singleton();
      }
    }
    return singleton;
  }
}
```

위처럼 사용하면 멀티스레딩을 사용하더라도 초기화일때만 동기화가 되어 하나의 인스턴스만 생성하여 사용할 수 있습니다.

> volatile 는 Main Memory 에 저장하겠다는 것을 명시하는 것입니다. 
>
> 스레드는 변수 값을 조회할 때 각자 가지고 있는 CPU cache 를 타게되는데 CPU cache 에 저장하지 않았기 때문에 Main Memory 에 접근하게 됩니다.

![cpuCache](/assets/images/posts_img/cs-singletonPattern/cpuCache.png)

참고) [https://nesoy.github.io/articles/2018-06/Java-volatile](https://nesoy.github.io/articles/2018-06/Java-volatile)

위처럼 CPU cache 를 타지못해 **성능적인 단점이 존재**합니다.

<br>

### Enum 사용

```java
public enum Singleton {
  INSTANCE;
  int value;

  public int getValue() {
    return int value;
  }
  public void setValue(int value) {
    this.value = value;
  }
}
```

위처럼 사용하면 컴파일 시점에 생성하여 가지고 있게되어 단 하나의 인스턴스만 생성되는 것을 보장받습니다.
이 방식은 자바에서 권고하는 방식입니다.

하지만 단점으로는 **컴파일 시점에 먼저 가지고 있어, Lazy 방식보단 메모리적인 단점이 존재**합니다.

<br>

### LazyHolder 기법

```java
public class Singleton {

  private Singleton() {}

  public static Singleton getInstance() {
    return LazyHolder.singleton;
  }

  public static class LazyHolder {
    private static final Singleton singleton = new Singleton();
  }
}
```

위 방법은 가장 많이 사용되고 있는 방식으로, Lazy 방식을 통해 메모리적인 이점을 가져올 수 있고, 하나의 인스턴스만 생성되는 것을 보장합니다.

먼저 위처럼 가능한 이유는 **Class 를 로딩할 때 Thread Safe 를 보장하는 것을 이용**하였습니다. 즉, 위 방식의 경우 하나의 인스턴스만 생성되는 걸 보장합니다.



단점으로는 **역직렬화 수행시 새로운 객체가 생성되고, 리플렉션을 이용해 내부 생성자 호출**이 가능합니다.

> 역직렬화는 직렬화된 대상 객체를 다시 객체로 변환하는 것을 의미합니다.
> 리플렉션은 동적으로 클래스에 접근할 때 사용하는 자바 API 입니다.
>

자바 직렬화 역직렬화 참조) [https://steady-coding.tistory.com/576#google_vignette](https://steady-coding.tistory.com/576#google_vignette)

리플렉션 참조) [https://velog.io/@yeon/Reflection이란](https://velog.io/@yeon/Reflection%EC%9D%B4%EB%9E%80)

<br>
<br>

# 참조

헤드퍼스트 디자인 패턴 책

[https://1-7171771.tistory.com/121](https://1-7171771.tistory.com/121)

[https://nesoy.github.io/articles/2018-06/Java-volatile](https://nesoy.github.io/articles/2018-06/Java-volatile)

[https://steady-coding.tistory.com/576#google_vignette](https://steady-coding.tistory.com/576#google_vignette

[https://velog.io/@yeon/Reflection이란](https://velog.io/@yeon/Reflection%EC%9D%B4%EB%9E%80)