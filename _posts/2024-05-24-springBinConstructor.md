---
title: "스프링 빈 생성 순서"
excerpt: "스프링에서 빈 생성할때 생성자에서 어떻게 다른 빈을 가져오는지에 대하여"

categories:
  - JAVA
tags:
  - [java, spring, bin, constructor]

permalink: /CS/JAVA/spring-bin-constructor

toc: true
toc_sticky: true
published: true

date: 2024-05-24
last_modified_at: 2024-05-24

---

# 서론
포스팅을 작성한 이유는 위에 제목처럼 스프링 빈을 생성할때 생성자 주입을 통해 넣을 경우 어떻게 스프링이 관련된 의존관계를 넣어줄 수 있는지에 대해 궁금증에 시작되었습니다. <br>
<br>
처음 예측한 것은 스프링에서 Lazy 로 생성할때 CGLIB 프록시 기술을 이용하는 것 처럼 생성때 프록시를 넣고 생성 후 나중에 진짜 객체를 넣어주는 형식으로 생각했으나 공부 후 완전히 다른 것을 알았고, 이 사실에 대하여 공유해야겠다는 생각이 들어 포스팅하게 되었습니다.
<br>
<br>

# 스프링 빈 생성 방법
빈 생성 시 의존관계에 대한 오브젝트를 주입해주는데 방법이 크게 3가지가 존재합니다. <br>
1. 생성자 주입
2. setter 주입
3. 필드 주입

<br>
생성자 주입은 말그대로 생성자를 통해 넣어주는 것을 의미합니다.

```java
public class A {
  private B b;

  @Autowired
  public A(B b) {
    this.b = b;
  }
}
```

<br>
setter 주입은 set 함수를 통해 넣어주는 방식입니다.

```java
public class A {
  private B b;

  public A() {}

  @Autowired
  public void setB(B b) {
    this.b = b;
  }
}
```

<br>
필드 주입은 필드에 @Autowired 를 통해 넣어주는 것입니다. 하지만 사용을 지양하고 있습니다.

```java
public class A {
  @Autowired private B b;

  public A() {}
}
```

이 중 생성자 주입에 대해 더 알아보겠습니다.

<br>
<br>

# 스프링 빈 생성할때 어떻게 생성자에서 필요한 의존관계를 가져올까
크게 스프링은 다음 단계를 거쳐 빈을 생성하는데 방법으로는 크게 2가지가 있습니다.
1. 컴포넌트 스캔 ( 자동 )
2. ApplicationContext ( 스프링 컨테이너 ) ( 수동 )

위 두가지 방법을 이용하여 스프링 빈을 생성하는데 다음 순서대로 생성됩니다.
1. 스프링 컨테이너에서 모든 빈을 탐색하고, 관련된 모든 정보를 수집합니다.
2. 정보를 바탕으로 의존관계에 있어 필요한 빈들을 먼저 생성합니다.
3. 이후 먼저 생성된 빈을 생성자 주입을 통해 새로운 빈을 생성합니다.
4. 생성자 주입이 아닌 경우 모든 빈이 생성 된 이후에 Injection(주입) 이 이루어집니다.

<br>

## 테스트
위 사항을 확인하기 위해 생성자에 system.out.println 을 붙혀 확인을 해보겠습니다.
먼저 다음과 같은 코드들이 있습니다.

```java
@Component
public class A {
    @Autowired
    public A() {
        System.out.println("A.A");
    }
}
@Component
public class B {
    private final C c;

    @Autowired
    public B(C c) {
        System.out.println("B.B");
        this.c = c;
    }
}
@Component
public class C {
    @Autowired
    public C() {
        System.out.println("C.C");
    }
}
@Component
public class D {
    private final A a;
    private final B b;

    public D(A a, B b) {
        System.out.println("D.D");
        this.a = a;
        this.b = b;
    }
}
```

위와 같은 코드가 존재한다고 할때 위에서 언급한대로 동작하면
1. 의존 관계가 없는 A, C 가 먼저 print 가 찍혀야 합니다.
2. 의존 관계가 C 하나 있는 B 가 찍혀야 합니다.
3. 의존 관계가 A, B 두개가 있는 D 가 찍혀야 합니다.

실제로 실행해보면 <br>
<img width="51" alt="image" src="https://github.com/pkt369/pkt369.github.io/assets/66049273/6adc3190-f559-41f4-9a2b-0d7932328134">
<br>
와 같이 나오는데 위에서 언급한대로 잘 나오고 있습니다. 정리하면

> 생성자 주입의 DI (Dependency Injection) 의 경우에는 스프링이 빈의 의존관계들을 파악해서 필요한 객체들을 먼저 만들어 주입해주는 것을 알 수 있습니다.

