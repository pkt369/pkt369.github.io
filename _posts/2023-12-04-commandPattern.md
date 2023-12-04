---
title: "커맨드 패턴"
excerpt: "커맨드 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, command]

permalink: /CS/design-pattern/command

toc: true
toc_sticky: true

date: 2023-12-04
last_modified_at: 2023-12-04

---

# 커맨드 패턴

책에서 말하는 커맨드 패턴은…

> 요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화 할 수 있습니다. 이러면 유청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있습니다.

제가 생각하는 커맨드 패턴은…

> 요청하는 객체와 수행하는 객체를 분리하여 느슨한 결합을 가져가고, 요청 사항에 따라 해당하는 커맨드를 실행하는 패턴

이라고 생각합니다.

<br>

# 커맨드 패턴은 언제 사용할까

커맨드 패턴은 **먼저 요청하는 객체와 요청을 수행하는 객체를 분리**하고 싶다면 사용하면 됩니다.

<br>

AI 인공지능 비서를 예시로 들어보겠습니다.

우리는 커맨드AI 라는 인공지능 비서를 만들었는데 안에 명령에 따라서 인공지능 비서가 가지고 있는 행동을 실행시킵니다.

```java
public class AI {
  public void execute(String command) {
    if (command.equals("음악 틀어")) {
      System.out.println("발라드 트는중");
    } else if (command.equals("랩 해줘")) {
      System.out.println("쿵치팍치");
    }
  }
}
```

위처럼 명령이 있을 때 AI 는 해당하는 명령에 따라 실행됩니다.

하지만 여기에는 **커맨드가 명령될 때 마다 구현코드가 수정되어야 한다는 단점이 존재**합니다.

**이러한 문제를 해결하기 위해서 커맨드 패턴을 이용**합니다.

<br>

먼저 Command 를 인터페이스로 선언합니다.

```java
public interface Command {
  void execute();
}
```

<br>

그리고 Command 를 상속받아 실제 명령들을 구현합니다.

```java
public class MusicOnCommand implements Command {
  @Override
  public void execute() {
    System.out.println("발라드 나오는 중");
  }
}
public class MusicOffCommand implements Command {
  @Override
  public void execute() {
    System.out.println("음악 끄는 중");
  }
}
public class RapOnCommand implements Command {
  @Override
  public void execute() {
    System.out.println("쿵치팍치");
  }
}
public class RapOffCommand implements Command {
  @Override
  public void execute() {
    System.out.println("조용...");
  }
}
```

<br>

다음으로는 AI 부분입니다.

```java
public class AI {
  private Map<String, Command> map;

  public AI() {
    map = new HashMap<>();
  }
  public void addCommand(String message, Command command) {
    map.put(message, command);
  }
  public void execute(String message) {
    if (!map.containsKey(message)) {
      System.out.println("존재하지 않는 명령입니다.");
      return;
    }
    map.get(message).execute();
  }
}
```

<br>

실행코드는 다음과 같습니다.

```java
public class Main {
  public static void main(String[] args) {
    AI ai = new AI();
    ai.addCommand("음악 틀어줘", new MusicOnCommand());
    ai.addCommand("음악 꺼줘", new MusicOffCommand());
    ai.addCommand("랩 해줘", new RapOnCommand());
    ai.addCommand("랩 하지마", new RapOffCommand());

    ai.execute("랩 해줘"); // 쿵치팍치
    ai.execute("날씨 알려줘"); // 존재하지 않는 명령입니다.
  }
}
```

**이전 코드와 비교했을 때 커맨드 패턴을 이용하면 구현 코드인 AI 에서 캡슐화된 Command 에 의해 느슨한 결합을 가져가게 되고, 새롭게 명령이 생기더라도 무한정으로 추가될 수 있습니다.**

<br>

# 커맨드 패턴의 구조

![structure](/assets/images/posts_img/cs-commandPattern/structure.png)

- 클라이언트: 실행하는 주체, AI 예제에서는 우리가 클라이언트에 해당합니다.
- 인보커: 커맨드를 가지고 있는 객체, AI 예제에서는 AI 에 해당합니다.
- 리시버: 명령 실행, AI 예제에서는 Main 에서 execute 에 해당합니다.

커맨드 패턴은 클라이언트가 명령을 내리면 인보커에서 해당하는 조건에 맞는 커맨드를 가져와 리시버에게 전달합니다. 리시버는 전달 받은 커맨드를 실행시킵니다.

<br>

# 참조

헤드퍼스트 디자인패턴 책