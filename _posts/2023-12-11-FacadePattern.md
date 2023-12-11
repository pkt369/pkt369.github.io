---
title: "퍼사드 패턴"
excerpt: "퍼사드 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, facade]

permalink: /CS/design-pattern/facade

toc: true
toc_sticky: true
published: true

date: 2023-12-11
last_modified_at: 2023-12-11

---

# 퍼사드 패턴

책에서 말하는 퍼사드 패턴은…

> 서브시스템에 있는 일련의 인터페이스를 통합 인터페이스로 묶어 줍니다. 또한 고수준 인터페이스도 정의하므로 서브시스템을 더 편리하게 사용할 수 있습니다.

제가 생각하는 퍼사드 패턴은…

![wiki](/assets/images/posts_img/cs-facadePattern/wiki.png)

> 사전 의미는 건물 외벽을 의미하며, 패턴에서는 **복잡한 기능을 숨겨 사용자에게는 간단하게 사용할 수 있도록 해주는 패턴**이라고 생각합니다.

<br>

# 언제 퍼사드 패턴을 사용할까

자동차에서 우리가 시동버튼을 누를 때 내부에서 어떤 방식으로 엔진이 켜지는지 알고 계신가요?

이처럼 **시동 버튼을 퍼사드 패턴을 적용한 사례**라고 생각하시면 됩니다.

![button](/assets/images/posts_img/cs-facadePattern/button.png)

<br>

시동 버튼을 누르면 내부에서는 이렇게 동작합니다.

> 시동 버튼 ⇒ 배터리 전기 공급 ⇒ 스타트 모터로 실린더 내 피스톤 상승/하강 ⇒ 연쇄적인 폭발로 스타트 모터가 필요없어짐 ⇒ 공회전

<br>

자동차에서 시동 관련 함수들이 간단하게 있다고 가정해보겠습니다.

```java
public class Battery {
  public void start() {
    System.out.println("배터리에서 전기 공급 시작");
  }
}
public class Motor {
  public void start() {
    System.out.println("스타트 모터로 실린더 내 피스톤 상승/하강");
  }

  public void doing() {
    System.out.println("공회전");
  }
}
```

<br>

그리고 엔진에서는 이러한 시동이 걸리게 되는 부분을 퍼사드로 적용하였습니다.

```java
public class EngineFacade {
  Battery battery;
  Motor motor;

  public EngineFacade(Battery battery, Motor motor) {
    this.battery = battery;
    this.motor = motor;
  }

  public void start() {
    battery.start();
    motor.start();
    motor.doing();
  }
}

class Main {
  public static void main(String[] args) {
    Battery battery = new Battery();
    Motor motor = new Motor();
    EngineFacade engineFacade = new EngineFacade(battery, motor);
    engineFacade.start(); //시동 버튼 클릭
  }
}
```

EngineFacade 클래스에서 start 가 퍼사드 패턴의 핵심 코드라고 할 수 있으며, 사용하는 Main 에서는 내부에서 **어떠한 방식으로 이루어지는 또한 어떤 순서로 이루어지는 몰라도 사용할 수 있도록 하였습니다.**

![diagram](/assets/images/posts_img/cs-facadePattern/diagram.png)

<br>

# 참조

헤드퍼스트 디자인패턴 책