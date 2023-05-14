---
title: "Singletone"
excerpt: "Design Pattern Singletone"

categories:
  - Design Pattern
tags:
  - [design pattern, singletone]

permalink: /design-pattern/singletone/

toc: true
toc_sticky: true

date: 2023-05-14
last_modified_at: 2023-05-14
---

## Singletone
싱글톤 패턴은 디자인 패턴 중 생성 패턴에 속하며 하나의 객체당 하나의 인스턴스만 가지는 패턴입니다. <br>
<br>

### 예시

```java
class Singletone{
  private static final Singletone Instance = new Singletone();
  
  private Singletone() {}

  public static Singletone getInstance() {
    return this.Instance;
  }
}

class Main {
  public static void main() {
    Singletone a = Singletone.getInstance();
    Singletone b = Singletone.getInstance();
    System.out.println(a.equals(b)); // true

    Singletone c = new Singletone(); // error
  }
}
```

위와 같이 싱글톤패턴은 생성자를 private 로 막아 더이상 생성못하게 하고 static final 로 Instance 를 생성해 하나의 클래스는 하나의 객체만 가지도록 하는 방식입니다.
