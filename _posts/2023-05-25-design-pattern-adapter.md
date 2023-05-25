---
title: "Adapter Pattern(어댑터 패턴)"
excerpt: "Design Pattern Adapter Pattern"

categories:
  - Design Pattern
tags:
  - [design pattern, adapter]

permalink: /design-pattern/adapter/

toc: true
toc_sticky: true

date: 2023-05-25
last_modified_at: 2023-05-25
---

## Adapter Pattern
Adapter Pattern 은 구조 패턴 중 하나로 이전에 사용하던 객체와 다르게 <span style="color:red">호환되지 않는 새로운 객체가 존재할 때 중간에서 연결해주는 역할</span>을 하는 것이 어댑터 패턴입니다.<br>
<br>
예를 들어 전기 콘센트를 예시로 들수 있습니다.<br>
한국은 220V, 일본은 110V 를 사용합니다. <br>
<br>
한국 콘센트로 일본에서 사용할려면 돼지코라고 불리는 어댑터가 필요한대 돼지코가 어댑터 패턴의 역할을 하고 있는 것 입니다.<br>
<br>
<br>

### 예시
한국에서 사용하는 전기 콘센트를 예시로 코드를 작성해 보겠습니다.
```java
public interface Volt110{

    void insert110();
}

public interface Volt220 {

    void insert220();
}

public class A implements Volt110 {
    @Override
    public void insert110() {
        System.out.println("insert 110 electric");
    }
}

public class B implements Volt220 {
    @Override
    public void insert220() {
        System.out.println("electric 220 insert");
    }
}
```

위처럼 A 는 110 볼트를 받아서 사용하는 제품이고, B 는 220 볼트를 받아서 사용하는 제품입니다. <br>
만약 한국에서 사용하던 제품을 일본에서 사용할려고 하면 위에서 언급한 돼지코라는 어댑터를 사용해 볼트 변경을 해주어야 합니다.<br>
<br>
<br>

```java
public class VoltAdapter implements Volt110{
    private Volt220 volt220;
    public VoltAdapter(Volt220 volt220) {
        this.volt220 = volt220;
    }


    @Override
    public void insert110() {
        this.volt220.insert220();
    }
}

public class Main {
    public static void main(String[] args) {
        //korea
        Volt220 b = new B();
        b.insert220(); // electric 220 insert

        // japan
        Volt110 bAdapter = new VoltAdapter(b);
        bAdapter.insert110(); // electric 220 insert
    }
}
```
위 코드를 살펴보면 b 는 220 볼트임에도 불구하고 VoltAdpater 에 의해 110 볼트를 사용할 수 있게 되었습니다. <br>
위처럼 기존에 사용하던 클래스에서 사용할 수 없는 환경이 되었을 때 중간 어댑터를 이용해 사용할 수 있도록 하는 패턴이 Adapter 패턴입니다. <br>

<br>
<br>
<br>

----

## Reference
[https://refactoring.guru/ko/design-patterns/adapter](https://refactoring.guru/ko/design-patterns/adapter) <br>
[https://jusungpark.tistory.com/22](https://jusungpark.tistory.com/22)
<br>



