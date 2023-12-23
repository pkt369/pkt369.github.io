---
title: "상태 패턴"
excerpt: "상태 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, State]

permalink: /CS/design-pattern/State

toc: true
toc_sticky: true
published: true

date: 2023-12-23
last_modified_at: 2023-12-23

---

# 상태 패턴

책에서 말하는 상태 패턴은…

> 내부 상태가 바뀜에 따라 객체의 행동이 바뀔 수 있도록 해줍니다. 마치 객체의 클래스가 바뀌는 것 같은 결과를 얻을 수 있습니다.

제가 생각하는 상태 패턴은…

> 상태를 객체화하여 상태가 행동을 할 수 있도록 해주는 패턴입니다.

<br>



# 상태 패턴은 언제 사용할까

**상태 패턴은 하나의 객체가 상태에 따라 다르게 행동해야 할때 상태에게 행동을 부여하여 확장성을 높이고 캡슐화할 때 사용합니다.**

<br>

예를 들어 배달의 민족에서 배달의 대한 상태값이 있다고 가정해보겠습니다.

![state](/assets/images/posts_img/cs-statePattern/state.png)

<br>



```java
public class Delivery {
  static final int DEFAULT = 0;
  static final int SHOP_CHECK_BEFORE = 1;
  static final int SHOP_MAKE_FOOD = 2;
  static final int SHOP_DONE_FOOD = 3;
  static final int DELIVERY_START = 4;
  static final int DELIVERY_END = 5;

  private int state = DEFAULT;

  public void start() {
    if (state != DEFAULT) {
      System.out.println("상태값이 맞지 않습니다.");
      return;
    }
    state = SHOP_CHECK_BEFORE;
    System.out.println("상점에서 확인하고 있습니다.");
  }

  public void makeFood() {
    if (state != SHOP_CHECK_BEFORE) {
      System.out.println("상태값이 맞지 않습니다.");
      return;
    }
    state = SHOP_MAKE_FOOD;
    System.out.println("요리를 만들고 있습니다.");
  }

  public void doneFood() {
    if (state != SHOP_MAKE_FOOD) {
      System.out.println("상태값이 맞지 않습니다.");
      return;
    }
    state = SHOP_DONE_FOOD;
    System.out.println("요리가 완료되었습니다.");
  }
  
  public void DeliveryStart() {
    if (state != SHOP_DONE_FOOD) {
      System.out.println("상태값이 맞지 않습니다.");
      return;
    }
    state = DELIVERY_START;
    System.out.println("배달이 시작되었습니다.");
  }
  
  public void DeliveryEnd() {
    if (state != DELIVERY_START) {
      System.out.println("상태값이 맞지 않습니다.");
      return;
    }
    state = DELIVERY_END;
    System.out.println("배달이 완료되었습니다.");
  }
}
```

위처럼 배달이 들어오고 나서 많은 상황에 따라 상태값이 변경이 되는 상황입니다.

하지만 여기서 주문을 하고 나서 시간을 예측할 수 있게 상점에서 시간을 입력해주는 기능을 넣어달라고 요청이 왔습니다.

![time](/assets/images/posts_img/cs-statePattern/time.png)

<br>

```java
private int time = -1;

public void makeFood(int time) {
  if (state != SHOP_CHECK_BEFORE) {
    System.out.println("상태값이 맞지 않습니다.");
    return;
  }
  state = SHOP_MAKE_FOOD;
  this.time = time;
  System.out.println("요리를 만들고 있습니다.");
}
```

이런식으로 구현코드가 수정이 되어야 하고, 더 큰 기능이 들어온다면 오류를 초래할 수 있습니다.

이때 상태 패턴을 이용하면 구현코드를 수정하지 않고, 상태 클래스만 수정하여 구현코드를 수정하지 않는 이점을 가질 수 있습니다.

<br>



### 변경 후

```java
public abstract class State {
  public abstract void next(Delivery delivery);
}

public class Delivery {
  private State state = new DeliveryDefault();
  private int time = -1;

  public void setState(State state) {
    this.state = state;
  }

  public void setTime(int time) {
    this.time = time;
  }

  public void nextStep() {
    state.next(this);
  }
}
```

먼저 State 상속 클래스를 만들어 인터페이스화 하였고, 기존 클래스에서는 nextStep 함수로만 다음 단계를 진행하도록 하여 간단하게 수정하였습니다.

<br>



```java
public class ShopCheckBefore extends State {
  public ShopCheckBefore() {
    System.out.println("상점에서 확인하고 있습니다.");
  }

  @Override
  public void next(Delivery delivery) {
    delivery.setState(new ShopMakeFood(delivery));
  }
}

public class ShopMakeFood extends State {
  public ShopMakeFood(Delivery delivery) {
    Scanner sc = new Scanner(System.in);
    System.out.println("요리 만드는 시간을 입력해주세요.");
    int time = sc.nextInt();
    delivery.setTime(time);

    System.out.println("요리를 만들고 있습니다.");
  }

  @Override
  public void next(Delivery delivery) {
    delivery.setState(new ShopDoneFood());
  }
}

public class ShopDoneFood extends State {
  public ShopDoneFood() {
    System.out.println("요리가 완료되었습니다.");
  }

  @Override
  public void next(Delivery delivery) {
    delivery.setState(new DeliveryStart());
  }
}
```

다음은 상점에 관련된 객체들인데 모두 생성자를 통해서 무언가를 해결하고, next 함수에서 setState 함수를 통해 다음의 단계를 넣어주도록 되어 있습니다.

**이렇게 개발하게 되면 클라이언트에서는 다음 단계를 전혀 알수 없는데 이부분이 전략 패턴과 다른점입니다.**

<br>



```java
public class DeliveryStart extends State {
  public DeliveryStart() {
    System.out.println("배달이 시작되었습니다.");
  }

  @Override
  public void next(Delivery delivery) {
    delivery.setState(new DeliveryEnd());
  }
}

public class DeliveryEnd extends State {
  public DeliveryEnd() {
    System.out.println("배달이 완료되었습니다.");
  }

  @Override
  public void next(Delivery delivery) {
    System.out.println("더이상 다음 단계를 진행할 수 없습니다.");
  }
}
```

배송에 관련된 상태 클래스들이며, 배송이 끝났으면 다음단계를 더이상 진행할 수 없도록 개발하였습니다.

<br>

```java
class Main {
  public static void main(String[] args) {
    Delivery delivery = new Delivery();
    delivery.nextStep();
    delivery.nextStep();
    delivery.nextStep();
    delivery.nextStep();
    delivery.nextStep();
    delivery.nextStep();
  }
}
```

![result](/assets/images/posts_img/cs-statePattern/result.png)

**이처럼 상태 패턴을 이용하면 기능이 확장되거나 변경이 될때 구현코드 (Delivery) 를 수정하는게 아닌 상태코드를 수정함으로써 OCP 를 지킬 수 있게 되었습니다.**

<br>



# 참조

헤드퍼스트 디자인패턴 책