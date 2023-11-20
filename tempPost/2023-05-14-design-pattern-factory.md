---
title: "Factory Pattern(팩토리 패턴)"
excerpt: "Design Pattern Factory Pattern"

categories:
  - Design Pattern
tags:
  - [design pattern, factory]

permalink: /design-pattern/factory/

toc: true
toc_sticky: true

date: 2023-05-14
last_modified_at: 2023-05-14
---

## Factory pattern
팩토리 패턴은 객체의 생성부분을 Factory 라는 클래스로 빼는 것을 말합니다. <br>
<br>

먼저 팩토리패턴은 어떻게 사용해야 하는지 알아보겠습니다. <br>
팩토리 패턴을 적용하기 전 코드는 밑과 같습니다. <br>
```java
interface Car {
  public void drive();
}

class Porsche implements Car{
  public Porsche() {}

  public void drive() {
    System.out.println("porsche911 drive");
  }
}

class Tesla implements Car {
  public Tesla() {}

  public void drive() {
    System.out.println("teslaS drive");
  }
}


class Main {
  public static void main() {
    //구현 코드 부분
    Car car = new Porsche();

    car.drive(); // porsche911 drive
  }
}
```

포르쉐를 가지고 있는 주인이 있다고 가정해보겠습니다. <br>
만약 주인이 포르쉐를 팔고 테슬라로 차량 변경을 했다고 가정했을 때 위 코드처럼 구현코드에 직접적으로 생성되어 있다면 구현코드를 수정해야 합니다.<br>
<br>

```java
class Main {
  public static void main() {
    //구현 코드 부분
    Car car = new Tesla();

    car.drive(); // teslaS drive
  }
}
```

이렇게 구현 코드를 수정하는 것은 OCP(Open Close Principle) 에 위배되는 행동입니다. <br>
이렇게 생성 부분을 캡슐화를 통해 OCP 를 위배되지 않게 해주는 패턴이 팩토리 패턴입니다. <br>
<br>
<br>

```java
class EmptyCar implements Car{
  public String drive() {
    System.out.println("no car");
  }
}
class CarFactory {
  public static Car getInstance(String name) {
    if (name.equals("porsche")) {
      return new Porsche();
    }
    if (name.euqals("tesla")) {
      return new Tesla();
    }
    return EmptyCar();
  }
}

class Main {
  public static void main() {
    //구현 코드 부분
    Scanner sc = new Scanner(System.in);
    Car car = CarFactory.getInstance(sc.next()); //talsa

    car.drive(); // teslaS drive
  }
}
```

위의 코드와 같이 input 을 동적으로 받고 팩토리패턴을 이용해 생성을 해주게되면 수정이나 확장이 되어야 할때도 구현코드를 수정하지 않아도 됩니다. <br>

```java
class Avante implements Car{
  public void drive() {
    System.out.println("Avante drive");
  }
}
class CarFactory {
  public static Car getInstance(String name) {
    if (name.equals("porsche")) {
      return new Porsche();
    }
    if (name.euqals("tesla")) {
      return new Tesla();
    }
    if (name.equals("avante")) {
      return new Avante();
    }
    return EmptyCar();
  }
}

class Main {
  public static void main() {
    //구현 코드 부분
    Scanner sc = new Scanner(System.in);
    Car car = CarFactory.getInstance(sc.next()); //avante

    car.drive(); // avante drive
  }
}
```

위와 같이 avante 라는 객체를 추가했음에도 불구하고 구현코드가 수정되지 않은 것을 확인할 수 있습니다. 
<br>
<br>
<br>
<br>



