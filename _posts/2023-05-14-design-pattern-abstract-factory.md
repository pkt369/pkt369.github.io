---
title: "Abstract Factory(추상팩토리 패턴)"
excerpt: "Design Pattern Abstract Factory Pattern"

categories:
  - Design Pattern
tags:
  - [design pattern, factory]

permalink: /design-pattern/abstract-factory/

toc: true
toc_sticky: true

date: 2023-05-14
last_modified_at: 2023-05-14
---

## Abstract Factory pattern
추상 팩토리 패턴은 조건에 따라 디테일한 팩토리들을 모두 만들고, 만든 팩토리들을 다시 큰 팩토리로 만든 패턴입니다. <br>
이전에 포스팅한 [팩토리 패턴](https://pkt369.github.io/design-pattern/factory/)보다 좀 더 캡슐화된 패턴이라고 볼 수 있습니다.<br>
<br>
<br>
자동차를 만드는 공장을 예시로 들어보겠습니다. <br>
자동차는 각 부품들이 합쳐져 만들어지는 산출물입니다. 자동차에는 핸들과 의자가 2가지의 부품이 필요하다고 가정해보겠습니다. <br>
<br>

```java
interface Handle {
  public String create();
}

interface Seat {
  public string create();
}

class TeslaHandle implements Handle {
  public TeslaHandle() {}

  public String create() {
    return "Tesla Handle create";
  }
}

class AvanteHandle implements Handle {
  public AvanteHandle() {}

  public String create() {
    return "Avante Handle create";
  }
}

class EmptyHandle {...}

class TeslaSeat implements Seat {
  public TeslaSeat() {}

  public String create() {
    return "Tesla Seat create";
  }
}

class AvanteSeat implements Seat {
  public AvanteSeat() {}

  public String create() {
    return "Avante Seat create";
  }
}

class EmptySeat {...}

public HandleFactory {
  public HandleFactory() {}

  public static Handle getHandle(String brand) {
    if (brand.equals("tesla")) {
      return new TeslaHandle();
    }
    if (brand.equals("avante")) {
      return new AvanteHandle();
    }
    return new EmptyHandle(); // 
  }
}

public SeatFactory {...}
```

위를 봤을 때 여러 팩토리들이 생성된 것을 볼 수 있습니다. <br>
추가적인 부품들도 밑의 코드에 추가된다고 했을 때 코드는 다음과 같습니다. <br>
<br>

```java
public Main {
  public createCar() {
    Scanner sc = new Scanner(System.in);

    String brand = sc.next(); //tesla
    Handle handle = new HandleFactory(brand);
    Seat seat = new SeatFactory(brand);
    Gear gear = new GearFactory(brand);
    //...여러부품들
  }
}
```
와 같을 때 구현 코드안에 많은 부품들이 생성되어 구현 코드가 많이 복잡해 보입니다. <br>
위와 같은 현상을 해결하기 위해 추상 팩토리 패턴을 사용합니다. <br>
<br>
코드를 보면 brand 를 공통으로 사용하는 것을 이용하여 추상 팩토리 메서드를 만들어보겠습니다. <br>
추상 팩토리 패턴을 사용하게 되면 하나의 큰 팩토리를 사용하여 구현코드에서 단순하게 사용할 수 있습니다.<br>
<br>

```java
public class CarFactory {
  public Handle createHandle();
  public Seat createSeat();
}

public class TeslaFactory implements CarFactory {
  public TeslaFactory() {}

  public Handle createHandle() {
    return new TeslaHandle();
  }
  public Seat createSeat() {
    return new TeslaSeat();
  }
}

public class AvanteFactory implements CarFactory {
  public AvanteFactory() {}

  public Handle createHandle() {
    return new AvanteHandle();
  }
  public Seat createSeat() {
    return new AvanteSeat();
  }
}
```

팩토리 패턴과 다르게 Handle 과 Seat 생성 부분을 어떤 타입에 따라 만드는 것이 아닌 정해진 팩토리(Tesla, Avante)안에 부품을 직접 선언하는 방식으로 사용합니다. <br>
여기서 이제 더 큰 팩토리를 만들어 어떤 타입에 따라 대상 팩토리를 리턴해주는 방식을 사용해보겠습니다. <br>
<br>

```java
public class FactoryOfCarFactory {
  public create(String brand) {
    CarFactory carFactory = null;

    if (brand.equals("tesla")) {
      carFactory = new TeslaFactory();
    } else if (brand.equals("avante")) {
      carFactory = new AvanteFactory();
    } else {
      carFactory = new EmptyFactory();
    }

    System.out.println(carFactory.createHandle);
    System.out.println(carFactory.createSeat);
  }
}

public Main {
  public createCar() {
    Scanner sc = new Scanner(System.in);

    String brand = sc.next(); //tesla
    FactoryOfCarFactory factory = new FactoryOfCarFactory(brand);
    factory.create();
  }
}
```

위와 같이 구현 코드에는 FactoryOfCarFactory 를 이용해 간단하게 구현된 것을 볼 수 있습니다. <br>
따라서 추상 팩토리 메서드는 팩토리 메서드에서 같은 타입으로 여러개의 팩토리가 생성될 때, 캡슐화를 통해 하나의 팩토리로 만들어 사용하는 것을 말합니다. <br>

<br>
<br>
<br>
<br>



