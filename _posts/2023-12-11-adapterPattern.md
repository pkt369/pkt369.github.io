---
title: "어댑터 패턴"
excerpt: "어댑터 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, adapter]

permalink: /CS/design-pattern/adapter

toc: true
toc_sticky: true
published: true

date: 2023-12-11
last_modified_at: 2023-12-11

---

# 어댑터 패턴

책에서 말하는 어댑터 패턴은…

> 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환합니다. 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와줍니다.

제가 생각하는 어댑터 패턴은…

> 타입이 달라 사용할 수 없는 객체를 호환이 가능하도록 중간 객체에서 변환하여 사용할 수 있도록 하는 것을 어댑터 패턴이라고 생각합니다.

<br>

간단하게 일본과 한국에서 사용하는 어댑터를 생각하시면 편리하실 것 같습니다.

![volt](/assets/images/posts_img/cs-apaterPattern/volt.png)

<br>

# 어댑터 패턴은 언제 사용할까

어댑터 패턴은 위에서 언급했듯이 사용할 수 없는 인터페이스를 사용할 수 있도록 해주는 패턴입니다.

<br>

예를 들어 휴대폰 충전기가 있습니다.

아이폰 12를 사용하던 사용자가 아이폰 15로 기기 변경을 하면서 충전구가 달라졌습니다.

아이폰 12는 8핀 충전기를 사용합니다.

![8fin](/assets/images/posts_img/cs-apaterPattern/8fin.png)

<br>

아이폰 15에서는 C 타입 충전기를 사용합니다.

![ctype](/assets/images/posts_img/cs-apaterPattern/ctype.png)

<br>

사용자가 8핀 충전기로 C 타입을 충전하는 상황을 예시로 들어보겠습니다.

CType 충전기에 관련한 코드 입니다.

```java
public interface CType {
  void charge();
}
public class CTypeCharger implements CType {
  @Override
  public void charge() {
    System.out.print("C타입 충전");
  }
}
```

<br>

8핀에 관련된 코드 입니다.

```java
public interface EightFin {
  void charge();
}
public class EightFinCharger implements EightFin {
  @Override
  public void charge() {
    System.out.print("8핀 충전");
  }
}
```

<br>

그리고 IPhone15 는 CType 충전기를 필요하고 있습니다.

```java
public class IPhone15 {
  CType cType;

  public IPhone15(CType cType) {
    this.cType = cType;
  }

  public void charge() {
    cType.charge();
  }
}

class Main {
  public static void main(String[] args) {
    IPhone15 iPhone15 = new IPhone15(new CTypeCharger());
    iPhone15.charge();
  }
}
```

위 코드를 보시면 아이폰 15에서 C 타입으로 충전을 할 수 있음을 알 수 있습니다.

하지만 **8핀 충전기로 CType 충전을 하고 싶다면 다음과 같이 어댑터를 사용**하여야 합니다.

![apater](/assets/images/posts_img/cs-apaterPattern/apater.png)

<br>

```java
public class EightFinToCTypeAdapter implements CType {
  EightFinCharger eightFinCharger;

  public EightFinToCTypeAdapter(EightFinCharger eightFinCharger) {
    this.eightFinCharger = eightFinCharger;
  }

  @Override
  public void charge() {
    eightFinCharger.charge();
  }
}

class Main {
  public static void main(String[] args) {
    EightFinToCTypeAdapter cType = new EightFinToCTypeAdapter(new EightFinCharger());
    IPhone15 iPhone15 = new IPhone15(cType);
    iPhone15.charge();
  }
}
```

Adapter 를 이용해 8핀으로 C type 으로 변환이 되어 이제 8핀으로도 충전할 수 있게 되었습니다.

이처럼 **어댑터 패턴은 중간에서 두개의 객체가 서로 이어질 수 있도록 해주는 역할을 담당**하고 있습니다.

<br>

# 참조

헤드퍼스트 디자인패턴 책