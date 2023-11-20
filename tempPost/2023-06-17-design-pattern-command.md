---
title: "Command Pattern(커맨드 패턴)"
excerpt: "Design Pattern Command Pattern"

categories:
  - Design Pattern
tags:
  - [design pattern, command]

permalink: /design-pattern/command/

toc: true
toc_sticky: true

date: 2023-06-17
last_modified_at: 2023-06-17
---

## Command Pattern

커맨드 패턴은 행위 패턴중 하나로 실행될 기능을 캡슐화하여 수정이 되어야 할때 구현 코드에서는 수정이 일어나지 않고 command 코드만 수정하면 되는 패턴입니다. <br>
전략 패턴과 비슷하며 둘다 의존성을 제거하는 것이 특징입니다. <br>
<br>
전략 패턴과 굳이 비교하자면 <span style="color:red">사용목적</span>에 따라 다릅니다. <br>
커맨드 패턴은 어떤 작업(What)을 선택할 때 사용하고, 전략 패턴은 어떤 방법(How)을 선택할 때 사용됩니다.<br>
<br>
<br>

### 커맨드 패턴 구조
커맨드 패턴은 Client, Invoker, Command, Receiver 로 총 4개로 나뉩니다. <br>
<br>

- Client: 사용하는 객체를 의미합니다.
- Invoker: 커맨드를 실행 시키는 역할입니다. (구현 코드)
- Command: 캡슐화역할을 담당하여 Invoker 와 Receiver 를 이어주는 역할을 합니다.
- Receiver: 객체 코드를 의미합니다. (기능 담당)
<br>
![commandPatternStructure](https://github.com/pkt369/pkt369.github.io/assets/66049273/4cef7d52-5b55-42e7-be88-088ca0424048)
<br>
<br>

### 예시
제가 사용하고 있는 맥북의 전원 버튼으로 예시로 들어보겠습니다.<br>
PowerButton 은 Invoker 이고, PowerOn 기능이 Recevier 입니다. <br>

```java
class PowerButton {
    public PowerButton() {}

    public void execute() {
        System.out.println("power on");
    }
}
```

전원버튼은 눌렀을 때 다음과 같이 전원 버튼을 사용하여 전원을 킬 수 있습니다.<br>
그다음 기능은 전원이 켜져있는 상태에서 전원 버튼을 누르면 절전모드 상태가 되는 기능을 개발할려고 합니다.<br>
<br>

```java
class PowerButton {
    private String mode;

    public PowerButton() {}

    public void setMode(String mode) {
        this.mode = mode;
    }

    public void execute() {
        if (mode.equals("on")) {
            System.out.println("power on");
        } else if (mode.equals("off")) {
            System.out.println("power off");
        }
    }
}
```

위처럼 수정하면 Solid 의 OCP 를 위배하게 됩니다. <br>
이때 커맨드 패턴을 이용하여 코드를 다음과 같이 수정하게 됩니다. <br>

```java
class PowerOn {
    public void on() {
        System.out.println("power on");
    }
}
class PowerOff {
    public void off() {
        System.out.println("power off");
    }
}
```

먼저 기능을 on 과 off 를 담당하는 객체로 나눕니다.<br>
<br>


```java
interface Command {
    public void execute();
}

class PowerOnCommand implements Command {
    private PowerOn powerOn;

    public PowerOnCommand(PowerOn powerOn) {
        this.powerOn = powerOn;
    }

    @Override
    public void execute() {
        powerOn.on();
    }
}

class PowerOffCommand implements Command {
    private PowerOff powerOff;

    public PowerOnCommand(PowerOff powerOff) {
        this.powerOff = powerOff;
    }

    @Override
    public void execute() {
        powerOff.off();
    }
}
```

각 기능을 커맨드 인터페이스를 통해 캡슐화를 시켜줍니다. <br>
<br>

```java
class PowerButton {
    private Command command;
    public PowerButton() {}

    public setCommand(Command command) {
        this.command = command;
    }

    public void execute() {
        this.command.execute();
    }
}
```
이제 PowerButton 은 Command 를 통해 기능을 실행하게 되었습니다. <br>
이렇게 되면 새로운 커맨드가 추가가 되거나 수정이 되어도 PowerButton 은 더이상 수정되지 않아도 됩니다. <br>
<br>
<br>

----

## Reference
[https://gmlwjd9405.github.io/2018/07/07/command-pattern.html](https://gmlwjd9405.github.io/2018/07/07/command-pattern.html) <br>
[https://tinkerbellbass.tistory.com/74](https://tinkerbellbass.tistory.com/74)
<br>



