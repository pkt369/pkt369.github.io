---
title: "팩토리 패턴과 추상 팩토리 패턴"
excerpt: "디자인 패턴중 팩토리 패턴과 추상 팩토리 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, factory, abstract factory]

permalink: /CS/design-pattern/factory

toc: true
toc_sticky: true

date: 2023-12-01
last_modified_at: 2023-12-01

---

# 팩토리패턴과 추상팩토리패턴

# 팩토리 패턴

먼저 책에서 말하는 팩토리 패턴은…

> 객체를 생성할 때 필요한 인터페이스를 만듭니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정합니다. 팩토리 메소드를 사용하면 인스턴스 만드는 일을 서브 클래스에 맡길 수 있습니다.
>

제가 생각하는 팩토리 패턴은…

> 구현코드에 존재하는 객체 생성 코드를 외부로 빼내어 구현코드를 느슨하게 만들어주는 패턴이라고 생각합니다.

<br>

<br>



## 심플 팩토리

팩토리 패턴에도 Simple Factory 와 Factory Pattern 이 존재하는데 먼저 Simple Factory 는 말그대로 간단하게 생성부분만 담당하는 코드입니다.

예를 들어 햄버거를 예시로 들어보겠습니다.

<br>

맥도날드에서 햄버거를 만들어낼 때 다음과 같은 코드로 만들어 내고 있었습니다.

```java
public class Mcdonald {
  public void createHamBurger() {
    SusuBurger susuBurger = new SusuBurger();

    susuBurger.getBread();
    susuBurger.getMeat();
    susuBurger.getVegitable();
    susuBurger.getSauce();
  }
}
```

<br>

맥도날드에서 구현 객체인 SusuBurger 를 사용해 **하나의 클래스에 의존적**입니다. 이때 이전 포스팅에서 배웠던 객체 지향에서 **구현보다는 인터페이스에 맞춰서 프로그래밍 해야한다**에 맞춰 변경해보겠습니다.

```java
// 기존
SusuBurger susuBurger = new SusuBurger();
// 변경
Burger susuBurger = new SusuBurger();
```

햄버거라는 추상클래스를 만들어 susuBurger 가 햄버거 인터페이스에 값을 받고 있습니다. 여기서 말하는 인터페이스는 자바의 인터페이스가 아니라 슈퍼클래스 즉, 하나의 큰틀을 의미하고 있습니다.

<br>

근데 여기서 문제점이 생깁니다. 제가 먹고싶은데 슈슈버거가 아닌 빅맥을 먹고 싶어졌습니다.

그러면 구현코드인 Mcdonald 의 createHamburger 함수를 수정해야 하는데 이때 OCP 를 지키지 못하게 됩니다. 이때 팩토리 패턴을 이용하면 됩니다.

```java
public class Mcdonald {
  public void createHamBurger(String name) {
    BurgerFactory burgerFactory = new BurgerFactory();
    Burger burger = burgerFactory.getBurger(name);

    burger.getBread();
    burger.getMeat();
    burger.getVegitable();
    burger.getSauce();
  }
}

class BurgerFactory {
  public Burger getBurger(String name) {
    if (name.equals("빅맥")) {
      return new BigMacBurger();
    }
    if (name.equals("슈슈버거")) {
      return new SusuBurger();
    }
    return new BaseBurger();
  }
}
```

class 인 팩토리를 하나 만들어서 구현코드에서는 **어떤 객체가 만들어지는지 모르도록** 만들었습니다.

위처럼 간단하게 Factory 를 만들어서 해결한 것이 Simple Factory 입니다.

> 간단한 팩토리(Simple Factory) 는 패턴이 아닌 프로그래밍에서 자주 쓰이는 관용구에 가깝다고 합니다.

<br>

<br>



## 함수를 이용한 팩토리 패턴

맥도날드가 미국에서 대성공으로 다른나라에 체인점을 내기 시작했습니다. 하지만 각 나라마다 추가된 햄버거가 존재하는데 우리나라는 불고기버거가 들어오게 되었습니다. 

![bugogi](/assets/images/posts_img/cs-factoryPattern/bugogi.png)

<br>

그렇다면 계속 새로운 팩토리를 만들어서 진행을 해야할까요?

물론 그렇게 진행할 수도 있겠지만 하나의 역할만 하는 불필요한 클래스들이 많이 늘어나게 됩니다.

이러한 문제점들을 해결할 수 있게 추상 클래스를 사용해보겠습니다.

```java
public abstract class Mcdonald {
  public void createBurger(String name) {
    Burger burger = orderBurger(name);

    burger.getBread();
    burger.getMeat();
    burger.getVegitable();
    burger.getSauce();
  }
  
  public abstract Burger orderBurger(String name);
}
```

추상 클래스를 선언했고, orderBurger 가 팩토리 역할을 하게 됩니다. 이렇게 되면 불필요한 클래스를 생성하지 않아도 됩니다.

```java
public class KoreaMcdonald extends Mcdonald {
  @Override
  public Burger orderBurger(String name) {
    if (name.equals("빅맥")) {
      return new BigMacBurger();
    }
    if (name.equals("슈슈버거")) {
      return new SusuBurger();
    }
    if (name.equals("불고기버거")) {
      return new BulGogiBurger();
    }
    return new BaseBurger();
  }
}

public class JapanMcdonald extends Mcdonald {
  @Override
  public Burger orderBurger(String name) {
    if (name.equals("빅맥")) {
      return new BigMacBurger();
    }
    if (name.equals("슈슈버거")) {
      return new SusuBurger();
    }
    if (name.equals("스시버거")) {
      return new SusiBurger();
    }
    return new BaseBurger();
  }
}
```

위처럼 선언하게 되면 버거의 createBurger 는 상속받아서 만드는 방법은 고정시킬 수 있고, 각 나라마다 새로운 메뉴를 추가할 수 있습니다.

> 만약 여기서 모든 나라에 공통적인 버거가 있다면 데코레이터 패턴을 이용해 간단하게 추가해 사용할 수도 있을 것 같습니다.

<br>

팩토리 패턴을 이용한다면 **객체 생성부분을 외부에 둠으로써 함수가 실행될 때까지 어떤 객체가 생성될지 모르게 됩니다**. 이처럼 팩토리 패턴은 이용하면 **DIP (Dependency Inversion Principle) 의존성 뒤집기 원칙을 지킬 수 있게 됩니다.**

<br>

<br>

# 추상 팩토리 패턴

책에서 말하는 추상 팩토리 패턴은…

> 구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공합니다. 구상 클래스는 서브클래스에서 만듭니다.
>

제가 생각하는 추상 팩토리 패턴은…

> 연관된 여러개의 객체를 묶어 추상화한 패턴이라고 생각합니다.

<br>

<br>



## 추상 팩토리 패턴은 언제 사용할까

추상 팩토리 패턴은 연관된 객체가 여러개로 묶여 있을 때 추상화하여 구현코드에서 직접 바라보지 않도록 변경하는 것입니다.

<br>

위에서 예를 들었던 맥도날드를 예시로 들어보겠습니다.

맥도날드에서 각 나라마다 가격이 싼 채소와 고기가 달라 나라마다 재료를 다르게 선택해야 했습니다.

```java
public class BigMacBurger extends Burger {
  Vegetable vegetable;
  Meat meat;
  Sauce sauce;
  Bread bread;
	
  public BigMacBurger() {
    this.vegetable = new Lettuce();
    this.meat = new Pig();
    this.sauce = new Salsa();
    this.bread = new BasicBread();
  }
}
```

위처럼 버거에 각각의 재료들이 존재할때 햄버거는 재료들에게 의존적입니다. 특히 여러개의 인스턴스가 생성되면서 구현에 의존하게 되었습니다.

이러한 부분을 해결하기 위해 추상 팩토리 패턴을 이용하면 쉽게 변경할 수 있습니다.

<br>

먼저 재료 Factory 를 만듭니다.

```java
public interface IngredientFactory {
  Vegetable getVegetable();
  Meat getMeat();
  Sauce getSauce();
  Bread getBread();
}

public class KoreaBigMacIngredientFactory {
  public Vegetable getVegetable() {
    return new Lettuce();
  }
  public Meat getMeat() {
    return new Pig();
  }
  public Sauce getSauce() {
    return new Salsa();
  }
  public Bread getBread() {
    return new BasicBread();
  }
}
```

<br>

팩토리를 통해 버거 구현소스에서 받아 해결할 수 있습니다.

```java
public class BigMacBurger extends Burger {
  Vegetable vegetable;
  Meat meat;
  Sauce sauce;
  Bread bread;
	
  public BigMacBurger(IngredientFactory ingredientFactory) {
    vegetable = ingredientFactory.getVegetable();
    meat = ingredientFactory.getMeat();
    sauce = ingredientFactory.getSauce();
    bread = ingredientFactory.getBread();
  }
}

class Main {
  public static void main(String[] args) {
    IngredientFactory ingredientFactory = new KoreaBigMacIngredientFactory();
    new BigMacBurger(ingredientFactory);
  }
}
```

위처럼 BigMacBurger 에서는 구현코드가 빠지게 되면서 느슨한 구조를 이루게 됩니다.

추상 팩토리 패턴을 이용한다면 팩토리 패턴과 같이 구현 코드에서 생성 코드가 빠지게 되면서 **DIP (Dependency Inversion Principle) 의존성 뒤집기 원칙을 지킬 수 있게 됩니다.**

<br>

<br>

# 팩토리 패턴 vs 추상 팩토리패턴

공통점: 

- 둘다 구현코드에서 생성 부분을 떼어내 구현코드의 구조를 느슨하게 만들어준다.

다른점:

- 팩토리 패턴은 값을 받아 어떤 값에 해당 값을 내려줄때 사용한다.
- 추상 팩토리 패턴은 여러개의 구성을 추상화하여 하나의 객체를 만드는데 사용합니다.
    - 따라서 구성의 변경이 많은 경우 비추천합니다.

<br>

<br>

# 참조

헤드퍼스트 디자인 패턴