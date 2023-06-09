---
title: "Facade Pattern(퍼사드 패턴)"
excerpt: "Design Pattern facade Pattern"

categories:
  - Design Pattern
tags:
  - [design pattern, facade]

permalink: /design-pattern/facade/

toc: true
toc_sticky: true

date: 2023-06-09
last_modified_at: 2023-06-09
---

## Facade Pattern
<img width="599" alt="wiki" src="https://github.com/pkt369/pkt369.github.io/assets/66049273/e2105e12-4c80-4bab-a01b-9434a31af007"><br>
facade Pattern 은 디자인 패턴의 구조 패턴 중 하나로 <span style="color:red">건물의 정면</span>을 뜻하는 단어로 디자인 패턴에서는 복잡한 것들은 내부로 넣고 외부로는 간단하게 사용한다는 의미를 가지고 있습니다. <br>
<br>

### 예시

자동차를 운전할려면 다음과 같은 행동이 필요합니다.<br>
시동 버튼 -> 배터리 전기 공급 -> 스타트 모터로 실린더 내 피스톤 상승/하강 -> 연쇄적인 폭발로 스타트 모터가 필요없어짐 -> 공회전 <br>
<br>
자동차에 관심이 없으면 위가 무슨 말인지도 알고싶지도 않습니다. 그냥 버튼 하나로 시동이 걸리는 것만 알고 있으면 됩니다.<br>
<br>
여기서 시동 버튼이 퍼사드 패턴을 적용한 것입니다.<br>
<br>

```java
// 배터리
class Battery {
    public void on() {
        //...
    }
}

// 스타트모터
class StartMoter {
    public void start() {
        //...
    }
}

// 실린더
class Cylinder {
    public void piston() {
        //...
    }
}
```

위처럼 차량 내부에는 부품들이 존재합니다. 각 부품들이 동작하는 방식을 알고 있어야 한다면 시동거는 것만 해도 정말 어려워 질 것입니다. <br>
<br>

```java
public class Human {
    public void carBoot() {
        Battery battery = new Battery();
        battery.on(); //시동 먼저 걸고..

        StartMoter startMoter = new StartMoter();
        startMoter.start(); //스타트 모터가 동작되고...

        //...
    }
}
```

위처럼 직접 사람이 모든 행동을 알고 직접 행동을 해야 합니다. <br>
여기서 퍼사드 패턴을 이용하면 사용자는 시동 버튼을 누르는 것만으로도 시동이 걸수 있게 됩니다.<br>
<br>

```java
public class CarFacade {
    public void boot() {
        Battery battery = new Battery();
        battery.on();

        StartMoter startMoter = new StartMoter();
        startMoter.start();

        Cylinder cylinder = new Cylinder();
        cylinder.piston();
    }
}

public class Human {
    public void bootButton {
        CarFacade car = new CarFacade();
        car.boot();
    }
}
```

이렇게 됨으로써 시동 버튼만 누르기만 복잡한 코드들을 알지 않아도 시동이 걸릴 수 있게 되었습니다.<br>
<br>
<br>

----

## Reference
[https://refactoring.guru/ko/design-patterns/facade](https://refactoring.guru/ko/design-patterns/facade) <br>
[https://lktprogrammer.tistory.com/42](https://lktprogrammer.tistory.com/42)
<br>



