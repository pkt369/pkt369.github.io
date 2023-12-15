---
title: "템플릿 메서드 패턴"
excerpt: "템플릿 메서드 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, template, method]

permalink: /CS/design-pattern/template-method

toc: true
toc_sticky: true
published: true

date: 2023-12-15
last_modified_at: 2023-12-15

---

# 템플릿 메소드 패턴

책에서 말하는 템플릿 메소드 패턴은…

> 알고리즘의 골격을 정의합니다. 템플릿 메소드를 사용하면 알고리즘의 일부 단계를 서브클래스에서 구현할 수 있으며, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의할 수도 있습니다.

제가 생각하는 템플릿 메소드 패턴은…

![wiki](/assets/images/posts_img/cs-templateMethod/wiki.png)

> 슈퍼 클래스에서 사용할 템플릿을 정의하고, 서브 클래스에서 다른 부분을 구현하여 코드를 재활용성을 높이는 디자인 패턴입니다.

<br>

# 템플릿 메소드 패턴은 언제 사용하는가?

템플릿 메소드는 **공통적인 부분을 정의하고 다른 부분을 자식 클래스에서 구현하도록 하여, 코드의 재사용성을 늘릴려고 할때 사용하는 패턴**입니다.

<br>

예를 들어 햄버거 판매점이 있습니다.

이 햄버거점은 무조건 빵두개와 중간에 고기가 들어가고 야채와 소스 등이 들어갑니다.

```java
public class BulGogiBurger {
  public void createBurger() {
    putBread();
    putMeat();
    putVegetable();
    putSauce();
  }
  private void putBread() {
    System.out.println("일반빵 세팅");
  }
  private void putMeat() {
    System.out.println("불고기 세팅");
  }
  private void putVegetable() {
    System.out.println("채소 세팅");
  }
  private void putSauce() {
    System.out.println("불고기소스 세팅");
  }
}

public class ShrimpBurger {
  public void createBurger() {
    putBread();
    putMeat();
    putVegetable();
    putSauce();
  }
  private void putBread() {
    System.out.println("일반빵 세팅");
  }
  private void putMeat() {
    System.out.println("세우 세팅");
  }
  private void putVegetable() {
    System.out.println("채소 세팅");
  }
  private void putSauce() {
    System.out.println("칠리소스 세팅");
  }
}
```

위 햄버거들을 봤을 때 공통적인 것은 일반빵을 세팅하고, 채소를 넣습니다.

다른 점으로 고기와 소스가 서로 다릅니다.

이러한 점을 이용하여 템플릿 메소드를 이용하여 수정해보겠습니다.

<br>

```java
public abstract class Burger {
  public final void createBurger() {
    putBread();
    putMeat();
    putVegetable();
    putSauce();
  }
  private void putBread() {
    System.out.println("일반빵 세팅");
  }
  private void putVegetable() {
    System.out.println("채소 세팅");
  }
  protected abstract void putMeat();
  protected abstract void putSauce();
}

public class BulGogiBurger extends Burger {
  @Override
  public void putMeat() {
    System.out.println("불고기 세팅");
  }

  @Override
  public void putSauce() {
    System.out.println("소스 세팅");
  }
}

public class ShrimpBurger extends Burger {
  @Override
  protected void putMeat() {
    System.out.println("새우 고기 세팅");
  }

  @Override
  protected void putSauce() {
    System.out.println("칠리 소스 세팅");
  }
}
```

위처럼 **Burger 슈퍼 클래스를 만들고, 함수에 final 을 붙힘으로써 자식에서는 오버라이딩 못하게 만듦으로써 템플릿화**하였습니다.

그리고 **abstract 클래스라 자식을 생성해야지만 사용할 수 있게 하여 인터페이스화하였고, abstract 함수를 사용하여 자식클래스에서 만들도록 하여 서로 다른 부분을 구현**하도록 만들었습니다.

![BurgerVer1](/assets/images/posts_img/cs-templateMethod/BurgerVer1.png)

<br>



### Hook

하지만 만약 여기서 채소가 여러개가 들어갈 수 있도록 수정하고 싶어졌습니다.

그렇다면 여기서 훅(Hook)을 사용하여 특수한 경우에만 채소가 추가가되도록 해줄 수 있습니다.

```java
public abstract class Burger {
  public final void createBurger() {
    putBread();
    putMeat();
    putVegetable();
    if (isAddVegetable()) {
      addVegetable();
    }
    putSauce();
  }
  private void putBread() {
    System.out.println("일반빵 세팅");
  }
  private void putVegetable() {
    System.out.println("채소 세팅");
  }
  protected abstract void putMeat();
  protected abstract void putSauce();
  protected boolean isAddVegetable() {
    return false;
  }
  protected void addVegetable() {}
}

public class AddBigMacBurger extends Burger {
  @Override
  protected void putMeat() {
    System.out.println("소고기 세팅");
  }
  @Override
  protected void putSauce() {
    System.out.println("빅맥 소스 세팅");
  }
  @Override
  protected boolean isAddVegetable() {
    return true;
  }
  @Override
  protected void addVegetable() {
    System.out.println("양배추 추가");
  }
}
```

여기서 특징은 isAddVegetable 를 오버라이드해서 사용할 수 있게 했고, addVegetable 함수를 통해서 필요에 의해 추가할 수 있도록 하였습니다.

<br>

```java
class Main {
  public static void main(String[] args) {
    BulGogiBurger bulGogiBurger = new BulGogiBurger();
    bulGogiBurger.createBurger();

    System.out.println("---------------------");

    AddBigMacBurger addBigMacBurger = new AddBigMacBurger();
    addBigMacBurger.createBurger();
  }
}
```

![result](/assets/images/posts_img/cs-templateMethod/result.png)

최종적으로는 훅을 통해서 채소를 더한 햄버거만 채소를 더 추가하도록 할 수 있었습니다.

![BurgerVer2](/assets/images/posts_img/cs-templateMethod/BurgerVer2.png)

<br>



# 참조

헤드퍼스트 디자인패턴 책