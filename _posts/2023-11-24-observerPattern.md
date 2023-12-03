---
title: "옵저버 패턴"
excerpt: "디자인 패턴중 옵저버 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, observer]

permalink: /CS/design-pattern/observer

toc: true
toc_sticky: true

date: 2023-11-24
last_modified_at: 2023-11-24

---

# 옵저버 패턴

헤드퍼스트 디자인 패턴 책에서 말하는 옵저버 패턴은…

> 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(one-to-many) 의존성을 정의합니다.
> 

제가 생각하는 옵저버 패턴은…

![observe-wiki](/assets/images/posts_img/cs-observerPattern/observe-wiki.png)

> 옵저버는 영어로 **발견하다, 관찰하다** 라는 뜻을 가지고 있습니다.
**주체가 정보를 주고 싶을때 주체를 관찰하고 있는 옵저버들에게 정보를 주는 방식을 옵저버 패턴**이라고 생각합니다.

<br>

# 옵저버 패턴은 언제 사용할까

옵저버 패턴은 **주체가 자신을 바라보고있는 객체들에게 정보를 줄 때 사용**합니다.

<br>

### 예시

우리는 휴대폰에 자주 광고 알림이 발생합니다.

![home-screen](/assets/images/posts_img/cs-observerPattern/home-screen.jpeg){: width="40%" height="40%"}

이러한 광고는 **해당 업체를 주체라고 할 수 있고 우리가 옵저버**라고 할수 있습니다.

주체는 옵저버들을 관리하고 알려줄 소식이 있을 때 옵저버 패턴을 이용해 옵저버들에게 정보를 알려줍니다.

<br>

그럼 주체(업체)는 어떻게 우리들을 관리할까요?

![application-setting](/assets/images/posts_img/cs-observerPattern/application-setting.jpeg){: width="40%" height="40%"}

옵저버들은 위처럼 알림 설정을 통해서 알림에 들어갔다 나갔다 할 수 있습니다. 

<br>

그림으로는 이해했으니 코드로 알아보겠습니다.

```java
public interface Subject {
  public void register(Observer observer);
  public void remove(Observer observer);
  public boolean sendAd(String ad);
}

public interface Observer {
  public boolean alertMessage(String message);
}
```

주체(업체)는 옵저버를 등록과 제거하는 함수가 존재하고 옵저버에게 광고를 전달하는 함수가 있습니다.

<br>

옵저버는 주체의 광고를 받아서 사용자에게 알려주는 기능을 가지고 있습니다.

```java
public class Company implements Subject {
  private List<Observer> observerList;

  public Company() {
    this.observerList = new ArrayList<>();
  }

  @Override
  public void register(Observer observer) {
    observerList.add(observer);
  }

  @Override
  public void remove(Observer observer) {
    observerList.remove(observer);
  }

  @Override
  public boolean sendAd(String ad) {
    observerList.stream().forEach(observer -> observer.alertMessage(ad));
    return true;
  }
}
```

위 코드는 주체를 구현한 코드이고 핵심 포인트는 리스트를 가지고 옵저버를 더하거나 쉽게 뺄 수 있다는 것입니다.

또한 sendAd 함수를 통해 등록되어 있는 모든 옵저버들에게 광고를 보낼 수 있습니다.

```java
public class IPhone implements Observer {
  private String name;
  public IPhone(String name) {
    this.name = name;
  }

  @Override
  public boolean alertMessage(String message) {
    System.out.println(name + "의 폰: " + message);
    return true;
  }
}
```

로 간단하게 주체로부터 받은 메세지를 바로 보여줄 수 있습니다.

```java
public class Main {
  public static void main(String[] args) {
    Company company = new Company();

    IPhone myPhone = new IPhone("세준");
    IPhone otherPhone = new IPhone("그루트");
    company.register(myPhone); // 어플 설치 및 광고 수락
    company.register(otherPhone);

    // 우리 회사에서 할인이벤트를 하니까 등록된 고객님들에게 메세지를 보내야지
    company.sendAd("특급 프로모션 50프로 할인!!");
	}
}
```

위를 실행시키면

![push-result](/assets/images/posts_img/cs-observerPattern/push-result.png){: width="80%" height="80%"} 

그림처럼 옵저버들에게 데이터를 보낸 것을 볼 수 있습니다.

<br>

# 옵저버 풀로 설계하기

위에서는 푸쉬 방식으로 옵저버들에게 메세지를 밀어넣어주는 방식으로 사용하고 있습니다.

Push 방식대신 Pull 방식으로도 설계할 수 있습니다.

**물론 옵저버들에게 무언가를 전달해준다는 방식 하나는 똑같습니다.**

```java
public interface Observer {
  public void inform(); //추가된 메서드
  public boolean alertMessage();
}

public interface Subject {
  public void register(Observer observer);
  public void remove(Observer observer);
  public boolean update(); //변경된 메서드
  public String getAd(); //추가된 메서드
}

public class Company implements Subject {
  private List<Observer> observerList;
  private String Ad; //추가된 변수

  //생성자, register, remove 코드 생략

  @Override
  public boolean update() {
    observerList.forEach(Observer::inform);
    return true;
  }

  @Override
  public String getAd() {
    return Ad;
  }

  public void setAd(String ad) {
    this.Ad = ad;
  }
}

public class IPhone implements Observer {
  private String name;
  private Subject subject;
  private String message;

  public IPhone(String name, Subject subject) {
    this.name = name;
    this.subject = subject;
  }

  @Override
  public void inform() {
    message = subject.getAd();
    alertMessage();
  }
  //alertMessage 함수 코드 생략
}
```

수정된 코드를 보면 광고를 직접 알려주는게 아니라 옵저버에게 변경이 되었다고 알려주고 **옵저버가 직접 메세지를 가져가는 형식**으로 변경이 되었습니다. 

따라서 주체에게 직접 접근하게 되었습니다.

```java
public class Main {
  public static void main(String[] args) {
    Company company = new Company();

    IPhone myPhone = new IPhone("세준", company);
    IPhone otherPhone = new IPhone("그루트", company);
    company.register(myPhone); // 어플 설치 및 광고 수락
    company.register(otherPhone);

    // 우리 회사에서 할인이벤트를 하니까 등록된 고객님들에게 메세지를 보내야지
    company.setAd("완전 특가! 99프로 할인!");
    company.update();
  }
}
```

여기 코드를 보시면 먼저 setAd 함수를 통해서 먼저 메세지를 넣은 후 update 함수를 통해 옵저버들에게 알려주고만 있습니다.

이후 옵저버들이 가져온 메세지로 휴대폰에 뿌려줍니다.

![pull-result](/assets/images/posts_img/cs-observerPattern/pull-result.png){: width="80%" height="80%"} 

<br>

# Push 와 Pull 무엇을 선택해야 하나

Push 의 장점으로는

- 주체를 몰라도 되므로 Pull 보다 결합도가 낮다.
- Pull 에 비해 데이터의 움직임이 적다.
    - Pull 은 알람을 받은 뒤 다시 get 을 통해 메세지를 받아오는 방식이고 Push 는 바로 메세지를 받는 형태입니다.
- Pull 에 비해 코드가 간단하다

<br>

Pull 의 장점으로는

- 필요의 경우 원하는 데이터만 얻어올 수 있다.
    - 만약 많은 데이터를 들고와야 한다면 Push 에 비해 빠른 속도를 낼 수 있다.

개인적인 생각으로는 **정해져있는 패턴의 경우 Push 를 사용하는 것이 좋을 것 같고, 데이터의 양이 많거나 코드가 많이 복잡해 질 경우 Pull 방식이 괜찮은 것 같습니다.**

<br>

# 마무리

디자인 패턴 중 옵저버 패턴에 대해 알아봤습니다.

옵저버 패턴은 하나의 주체가 여러개의 객체에게 연락할 수 있는지에 대해 알려주는 패턴이었고, **상호작용하는 객체 사이에서는 가능하면 느슨한 결합을 사용해야하는 원칙을 준수**하고 있었습니다.

옵저버 패턴은 장점만 있는 것이 아니라 **옵저버 패턴은 알림 순서를 제어할 수 없다는 단점도 존재**합니다만 상황에 맞게 잘사용하면 좋은 패턴이라고 생각합니다.

<br>

### 참고

자바에도 Observable 이라는 객체가 존재하나 자바9부터 deprecated 되었습니다.

<br>

# 참조

헤드퍼스트 디자인패턴 책