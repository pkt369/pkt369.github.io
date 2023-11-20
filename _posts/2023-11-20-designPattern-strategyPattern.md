---
title: "디자인 패턴과 전략 패턴"
excerpt: "디자인 패턴과 전략 패턴에 대해서"

categories:
  - CS
tags:
  - [design pattern, strategy]

permalink: /CS/design-pattern/strategy

toc: true
toc_sticky: true

date: 2023-11-20
last_modified_at: 2023-11-20
---

# 디자인 패턴

디자인 패턴은 먼저 **선배 개발자들이 겪은 문제들을 패턴으로 풀어놓은 것**을 의미합니다.

우리가 패턴을 배우지 않았더라도 회사에서 선배 개발자들이 사용하던 방식을 따라하다보면 우리도 모르는 사이 패턴을 적용하고 사용할 수 있을 수도 있습니다.



## 디자인 패턴은 무엇을 기초로 만들어지는가

디자인 패턴은 객체지향 프로그래밍을 토대로 

- 추상화
- 캡슐화
- 다형성
- 상속

을 활용해서 디자인 패턴을 만들어 사용합니다.

즉, 코드들의 재사용성을 높이고, 변경이 쉬워지며 관리하기 쉬운 시스템을 만들게 해줍니다.



## 디자인 패턴을 왜 공부해야하는가

앞에서 이미 좋은 코드를 작성하기 위해서 디자인 패턴을 공부해야 한다고 말하기도 했지만 다른 이유도 있습니다.

회사에서 설계를 진행할 때 

> 귀를 기울이는 모든 객체를 추적할 수 있고, 새로운 데이터가 들어올때마다 모든 청취자들에게 데이터를 보내주고 청취자는 언제든지 나갔다 들어올 수 있는 형태로 만드는게 좋겠어
>

라고 한다면 우리는 머리속으로 방금 지나온 말들을 상상하고 옵저버 패턴이라고만 하면 되잖아 라고 생각할 수 있습니다. 

이처럼 회사에서 대화를 할 때 전문적인 지식으로 얘기를 하다보면 우리의 의견을 조금더 명확하게 전달할 수 있고, 이미 앞서 경험한 선배 개발자들의 지혜를 빌려 나쁜 길(?) 로 빠지지 않을 수 있게 될 수 있습니다.



### 필자의 회사에서의 이야기

저는 디자인 패턴 중에 간단한 몇가지만 알고 있는 상황이었고, 새롭게 개발하는 큰 아키텍쳐에 대해서 설계를 진행해야 했습니다. 물론 선배 개발자 들과 함께 진행했습니다.

팀원 A 인 선배 개발자는

> 여기서는 팩토리 메소드 패턴을 이용해서 상태값에 따른 값을 뽑은 뒤 전략 패턴(setter)로 넣어주자
> 그리고 너무 복잡한건 해롭기(?) 때문에 퍼사드 패턴으로 이부분은 은닉화해놓자
>

그리고 팀원 B 인 또 다른 선배 개발자는

> 팩토리 메소드 패턴은 불필요한 것 같아. 코드가 한눈에 들어오지 않아서 이 부분은 그냥 외부 코드에 사용하자
>

라고 말했습니다.

하지만 부끄럽게도 이해한거라곤 전략 패턴뿐이어서 선배 개발자분께 패턴인지에 대해 물어봤었습니다. 

이러한 부끄러운 경험이 있기에 패턴이 공부하기로 결심을 했고, 패턴을 공부했었습니다.

물론 이 글은 이미 공부한 내용들을 복습하고 다시 머리속에 집어 넣을 겸 작성하고 있습니다 😀

# 전략 패턴

헤드퍼스트 디자인패턴 책에서 말하는 전략 패턴은…

> 알고리즘군을 정의하고 캡슐화해서 각각의 알고리즘군을 수정해서 쓸 수 있게 해주는 패턴입니다. 
> 전략 패턴을 사용하면 클라이언트로 부터 알고리즘을 분리해서 독립적으로 변경할 수 있습니다.
>

제가 생각하는 전략 패턴은…

> 실행 시 **객체를 쉽게 변경할 수 있도록 도와주는 패턴**입니다.



## 전략 패턴은 언제 사용하는가

전략 패턴은 고정이 되어있는게 아닌 **나중에라도 변경이 될수 있는 코드**이면 사용합니다.

예를 들어 배달 어플을 만들었습니다. 이 배달 어플에서 VIP 회원에게 할인을 해주기로 기획자들이 정의하였습니다.

하지만 정률할인을 해줄지 아니면 정해진 금액을 할인해줄지에 대해 아직 정해지지 않았고, 개발자는 개발날짜가 타이트해서 먼저 개발을 시작해야 했습니다.

이럴 때 개발자는 나중에 쉽게 변경할 수 있도록 할인이라는 인터페이스를 정의하여 사용할 수 있습니다.

```java
public interface Discount {
	public int discountPrice(int price);
}

public class TempDiscount implements Discount {
	public int discountPrice(int price) {
		return 1000;
	}
}

public class RateDiscount implements Discount {
	public int discountPrice(int price) {
		return price / 100 * 10;
	}
}

// 실제 사용 객체
public class Order {
	Discount discount;
	int allPrice;
	
	...
	public void setDiscount(Discount discount) {
		this.discount = discount;
	}
  
	public int calculate() {
		allPrice -= discount.discountPrice();
	}
}

public static void main(String[] args) {
	Order order = new Order();
	order.setDiscount(new TempDiscount()); // 실행시 넣어주는 방식
	order.calculate();
}
```

위처럼 언제든지 변경이 될수 있어야할 때 전략 패턴을 사용합니다.



## 다른 예시를 들려줘

사실 디자인 패턴이라는건 글로 공부하고 바로 적용하기란 쉽지 않습니다.

그래서 여러 경험을 통해 전략패턴이라는 것을 인지하고 필요한 상황이 되었을 때 예시처럼 적용하는게 중요하다고 생각합니다.



저는 자동차를 좋아합니다. 그래서 자동차를 예시로 들어볼까 합니다.

요즘은 AI 시대로 자동차가 알아서 운전해주는 시대가 오고 있습니다. 마치 테슬라 오토파일럿처럼 말이죠 😀

직장인 A 씨는 테슬라에서 제공하는 자율주행 기능인 FSD를 구매하지 않았고 기존 오토파일럿 기능만 사용하고 있었습니다.

```java
public interface Car {
	public void drive();
}

public Tesla implements Car {
	DriveMode driveMode;

	public Tesla(DriveMode driveMode) {
		this.driveMode = driveMode;
	}

	public void setDriveMode(DriveMode driveMode) {
		this.driveMode = driveMode;
	}

	public void autoDrive() {
		driveMode.drive();
	}
}

public interface DriveMode {
	public void drive();
	public void go();
	public void stop();
}

public class GeneralDriveMode implements DriveMode {
	public void drive() { 
		System.out.println("일반 운전");
	} // 일반적인 자동 운전 
	public void go() { ... } // 감
	public void stop() { ... } // 멈춤
}

public static void main(String[] args) {
	DriveMode generalMode = new GeneralDriveMode();
	Car car = new Tesla(generalMode);
	car.autoDrive(); // 일반 운전
}
```



하지만 이제 직장인 A 씨는 돈이 생겼고 완벽한 자율주행인 FSD (Full Self Driving)을 구매할 수 있게 되었습니다. 

```java
public class FSDMode implements DriveMode {
	public void drive() { 
		System.out.println("완벽한 자율 주행");
	}
	public void go() { ... } // 감
	public void stop() { ... } // 멈춤
}

public static void main(String[] args) {
	DriveMode generalMode = new GeneralDriveMode();
	Car car = new Tesla(generalMode);
	car.autoDrive(); // 일반 운전

	// 직장인 A 씨의 자율 주행 기능 구매
	DriveMode fSDMode = new FSDMode();
	car.setDriveMode(fSDMode);

	car.autoDrive(); // 완벽한 자율 주행
}
```

이처럼 직장인 A 씨가 구매함으로써 자동차를 바꾸는게 아닌 테슬라의 주행모드를 FSD자율주행모드로 변경할 수 있게 되었습니다. 



크게 보면 우리주변의 모든 사물들은 비슷하게 설계가 되어있는 것을 볼수 있습니다. 예시로 컴퓨터라던지, 주변에 사물들을 생각하면서 되새김하다보면 머리속에 잘들어올거라고 생각합니다. 그래도 아직 헷갈리신다면 혼자 직접 닌텐도 게임기를 설계해보시기를 추천드립니다 🙂



### 참조 및 레퍼런스

이 글은 헤드퍼스트 디자인패턴 책의 내용을 이해한 것을 기준으로 작성한 블로그입니다.







