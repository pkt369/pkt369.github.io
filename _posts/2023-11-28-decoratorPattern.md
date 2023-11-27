---
title: "데코레이터 패턴"
excerpt: "디자인 패턴중 데코레이터 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, decorator]

permalink: /CS/design-pattern/decorator

toc: true
toc_sticky: true

date: 2023-11-28
last_modified_at: 2023-11-28

---

# 데코레이터 패턴

헤드퍼스트 디자인패턴 책에서 말하는 데코레이터 패턴은…

> 객체에 추가 요소를 동적으로 더할 수 있습니다. 데코레이터를 사용하면 서브클래스를 만들때보다 훨씬 유연하게 기능을 확장할 수 있습니다.

제가 생각하는 데코레이터 패턴은…

![wiki](/assets/images/posts_img/cs-decoratorPattern/wiki.png)

> **장식하다**라는 의미를 가지고 있는데, **기본 기능에 추가로 무언가를 더하여 더 멋진 기능이 되도록 하는 패턴**이라고 생각합니다.

<br>

# 데코레이터 패턴은 언제 사용하는가

만약 새로운 햄버거 가게가 생겼다고 가정해봅시다.

그 가게는 기본 버거는 1000원이고 토핑을 추가해서 먹는 햄버거집입니다.

```java
public class Hamburger {
  int price;
  String name;
  Vegetable vegetable;
  Meat meat;

  public Hamburger() {
    this.price = 1000;
    this.name = "햄버거";
  }

	hasVegetable() {...}
	hasMeat() {...}
	ironBake() { System.out.println("철판"); }
}
```

만약 여기서 새롭게 Egg 를 추가하게 되면 **OCP (Open Close Princle)를 위배했다라고 말하며, 객체 지향에서 원하는 설계가 아닙니다.**

<br>

만약 철판에서 한번 굽고 이후 오븐에 굽는 햄버거가 나온다면 

```java
public class Hamburger {
  int price;
  String name;
  Vegetable vegetable;
  Meat meat;

  public Hamburger() {
    this.price = 1000;
    this.name = "햄버거";
  }

	ironBake() { System.out.println("철판"); }
	ovenBake() { System.out.println("오븐"); }
}
```

이전 코드와 중복되는 코드도 생깁니다. 

**이전 코드를 수정하지 않고, 중복되지 않으며, 확장도 할수 있는 패턴이 바로 데코레이터 패턴**입니다.

<br>

## 데코레이터 패턴으로 예제 수정하기

```java
public abstract class Hamburger {
  String name;

  public abstract int getPrice(); //변수 대신 함수로 사용

  public String getName() {
    return name;
  }
}

public abstract class Topping extends Hamburger {
  Hamburger hamburger;
  public abstract String getName();
}
```

먼저 햄버거와 토핑을 분리하였습니다. 토핑은 자기자신을 포함한 Hamburger 를 받아 부모 클래스의 기능을 그대로 사용할 수 있습니다.

price 를 변수로 사용하지 않고 무조건 구현하도록 abstract 로 만들어 가격을 무조건 가지도록 하였습니다.

<br>

```java
public class BaseBurger extends Hamburger {
  public BaseBurger() {
    this.name = "기본버거";
  }

  @Override
  public int getPrice() {
    return 1000;
  }
}
```

기본 버거를 class 를 만들었고 모든 버거는 Hamburger 를 바라 보도록 설계를 했습니다.

![hamburger](/assets/images/posts_img/cs-decoratorPattern/hamburger.png)

<br>

```java
public abstract class Topping extends Hamburger {
  Hamburger hamburger;
  public abstract String getName();
}

public class Chicken extends Topping {
  public Chicken(Hamburger hamburger) {
    this.hamburger = hamburger;
  }

  @Override
  public int getPrice() {
    return hamburger.getPrice() + 1000;
  }

  @Override
  public String getName() {
    return "치킨고기 " + hamburger.getName();
  }
}

public class Lettuce extends Topping {
  public Lettuce(Hamburger hamburger) {
    this.hamburger = hamburger;
  }

  @Override
  public int getPrice() {
    return hamburger.getPrice() + 200;
  }

  @Override
  public String getName() {
    return "상추 " + hamburger.getName();
  }
}
```

또한 토핑은 따로 클래스로 만들어 햄버거를 상속받아 진행하였습니다.

이렇게 함으로써 **토핑은 자기 자신을 포함한 햄버거를 상속받아 추가적인 토핑을 추가할 수 있게 되었습니다.**

![topping](/assets/images/posts_img/cs-decoratorPattern/topping.png)

<br>

이렇게 함으로써 결과는 다음과 같습니다.

![result](/assets/images/posts_img/cs-decoratorPattern/result.png)

<br>

# 정리

이처럼 데코레이터 패턴을 사용함으로써 무언가를 추가할 때 새로운 데코레이터를 추가하기만 하면되고, 기존 코드는 수정하지 않아도 됨으로써 좋은 객체지향 코드를 작성할 수 있습니다.

<br>

하지만 단점으로는 **자잘한 객체가 매우 많이 추가가 될 수 있고, 너무 많이 사용하면 코드가 많이 복잡해져 유지보수성이 떨어집니다.**

<br>

# 참조

헤드퍼스트 디자인패턴 책