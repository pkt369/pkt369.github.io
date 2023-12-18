---
title: "컴포지트 패턴"
excerpt: "컴포지트 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, composite]

permalink: /CS/design-pattern/composite

toc: true
toc_sticky: true
published: true

date: 2023-12-18
last_modified_at: 2023-12-18

---

# 컴포지트 패턴

책에서 말하는 컴포지트 패턴은…

> 객체를 트리구조로 구성해서 부분-전체 계층구조를 구현합니다. 컴포지트 패턴을 사용하면 클라이언트에서 개별 객체와 복합 객체를 똑같은 방법으로 다룰 수 있습니다.

제가 생각하는 컴포지트 패턴은…

> 하나의 공통된 인터페이스를 부모와 자식에게 같이 사용하여 둘다 같은 방식으로 사용할 수 있게 해주는 패턴이라고 생각합니다.

<br>

# 컴포지트 패턴은 언제 사용하나

컴포지트 패턴은 트리구조로 자식이 얼마나 늘어날지 모르는 상태에서 부모와 자식을 같은 방식으로 사용하고 싶을 때 사용합니다.

<br>

예를 들어 맥북에 있는 폴더와 파일을 예시로 들어보겠습니다.

![folderFile](/assets/images/posts_img/cs-compositePattern/folderFile.png)

위와 같이 폴더와 파일이 있을 때 폴더는 폴더를 가질 수도 있고, 파일을 가질 수도 있습니다.

```java
public abstract class Composite {
  List<Composite> list = new ArrayList<>();
  String name;

  public void add(Composite composite) {
    list.add(composite);
  }

  public void print(String s) {
    s = s + "/" + name;
    System.out.println(s);
    for (Composite composite : list) {
      composite.print(s);
    }
  }
}
```

Composite 는 폴더와 파일의 공통된 슈퍼 클래스입니다.

<br>

```java
public class Folder extends Composite {
  public Folder(String name) {
    super.list = new ArrayList<>();
    super.name = name;
  }
}

public class File extends Composite {
  public File(String name) {
    super.name = name;
  }

  @Override
  public void add(Composite composite) {
    System.out.println("file 에는 새로운 객체를 추가할 수 없습니다.");
  }
}
```

폴더와 파일의 생성자에서 super.name 을 사용하여 이름을 넣도록 하였고, 파일의 add 는 사용하지 않도록 막아두었습니다.

 <br>

```java
class Main {
  public static void main(String[] args) {
    Composite a = new Folder("a");
    Composite folderTest = new Folder("folderTest");
    Composite textFile = new File("textFile");
    a.add(folderTest);
    a.add(textFile);

    Composite folder = new Folder("study");
    folder.add(a);

    folder.print("~");
  }
}
```

![result](/assets/images/posts_img/cs-compositePattern/result.png)

위처럼 print 하나로 모든 파일에 접근할 수 있으며, 이전에 작성했던 반복자 패턴처럼 모든 객체에 접근할 수 있게 되었습니다.

<br>

**컴포지트 패턴은 클라이언트가 복합객체(폴더)와 단일객체(파일)를 똑같이 사용할 수 있도록 하는 장점이 있지만, 코드에서 어디까지 데이터를 담고 있을지 몰라 데이터를 한눈에 알기 어렵다는 단점이 존재합니다.**

<br>



# 참조

헤드퍼스트 디자인패턴 책