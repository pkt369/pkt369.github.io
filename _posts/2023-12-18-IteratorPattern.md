---
title: "반복자 패턴"
excerpt: "반복자 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, iterator]

permalink: /CS/design-pattern/iterator

toc: true
toc_sticky: true
published: true

date: 2023-12-18
last_modified_at: 2023-12-18

---

# 반복자 패턴

책에서 말하는 반복자 패턴은…

> 컬렉션의 구현 방법을 노출하지 않으면서 집합체 내의 모든 항목에 접근하는 방법을 제공합니다.

제가 생각하는 반복자 패턴은…

> 객체의 집합체를 어떠한 컬렉션과 상관없이 모든 객체에 접근할 수 있도록 하는 것이 반복자 패턴이라고 생각합니다.

<br>

# 반복자 패턴은 언제 사용되는가

반복자 패턴은 List, Map, 배열과 상관없이 모든 객체에 접근할려고 할 때 사용합니다.

<br>



## 개선전

예를 들어 우리 회사는 음악을 내부 또는 외부로부터 음악을 받아 재생하는 역할로 외부로 받은 음악 파일을 우리의 파일과 같이 재생해야 합니다.

```java
public class Music {
  String name;
  public Music(String name) {
    this.name = name;
  }

  public String getName() {
    return name;
  }
}
```

먼저 음원 클래스는 위와 같습니다.

<br>

```java
public class WeCompany {
  List<Music> musicList = new ArrayList<>();;

  public void add(Music music) {
    musicList.add(music);
  }
}

public class OtherCompany {
  Music[] musicArr;
  int index = 0;

  public OtherCompany(int count) {
    musicArr = new Music[count];
  }

  public void add(Music music) {
    if (index >= musicArr.length) {
      System.out.println("이미 가질수 있는 음원이 꽉 찼습니다.");
      return;
    }
    musicArr[index++] = music;
  }
}
```

위처럼 다른 회사에서는 배열을 주고 있고, 우리 회사는 List 로 음악을 사용하고 있습니다. 

<br>

```java
public class MusicPlayer {
  WeCompany weCompany;
  OtherCompany otherCompany;

  public MusicPlayer(WeCompany weCompany, OtherCompany otherCompany) {
    this.weCompany = weCompany;
    this.otherCompany = otherCompany;
  }

  public void play() {
    for (Music music : weCompany.musicList) {
      System.out.println(music.getName() + " 듣는중");
    }
    
    for (Music music : otherCompany.musicArr) {
      if (music == null) {
        continue;
      }
      System.out.println(music.getName() + " 듣는중");
    }
  }
}
```

위는 음원을 실행시켜주는 앱의 코드입니다.

<br>

```java
class Main {
  public static void main(String[] args) {
    WeCompany weCompany = new WeCompany();
    weCompany.add(new Music("좋은날"));
    weCompany.add(new Music("마음"));

    OtherCompany otherCompany = new OtherCompany(5);
    otherCompany.add(new Music("hype boy"));
    otherCompany.add(new Music("Ditto"));
    
    MusicPlayer musicPlayer = new MusicPlayer(weCompany, otherCompany);
		musicPlayer.play();
  }
}
```

위처럼 실행시키게 되면 다음과 같습니다.

![result1](/assets/images/posts_img/cs-iteratorPattern/result1.png)

위의 코드를 보시면 **for 문을 두번돌려야 하는 번거로움이 존재**합니다. 만약 다른 업체에서는 Map 을 이용한다면 for 문을 3번돌려야 할 것입니다.

이때 반복자 패턴을 이용하면 불필요한 중복 코드를 없앨 수 있습니다.

<br>



## 개선후

자바에서는 반복자 패턴을 이미 사용할 수 있도록 준비해두었습니다.

바로 **Iterator 를 사용하는 것**입니다.

```java
public class WeCompany {
  ...

  // 추가된 코드
  public Iterator<Music> getIterator() {
    return musicList.listIterator();
  }
}
```

자바에서 모든 컬렉션에서는 Iterator 가 이미 구현되어 있기 때문에 List 의 경우 위의 코드처럼만 하면 됩니다. 

<br>

```java
public class OtherCompanyIterator implements Iterator<Music> {
  Music[] musicArr;
  int index;

  public OtherCompanyIterator(Music[] musicArr) {
    this.musicArr = musicArr;
    index = 0;
  }

  @Override
  public boolean hasNext() {
    return index < musicArr.length && musicArr[index] != null;
  }

  @Override
  public Music next() {
    return musicArr[index++];
  }
}

public class OtherCompany {
  ...

  // 추가된 코드
  public Iterator<Music> getIterator() {
    return new OtherCompanyIterator(musicArr);
  }
}
```

이후 otherCompanyIterator 를 새롭게 구현하고 OtherCompany 에서 사용할 수 있도록 합니다.

 <br>

```java
public class MusicPlayer {
  ...

  public void play() {
    Iterator<Music> weCompanyIterator = weCompany.getIterator();
    Iterator<Music> otherCompanyIterator = otherCompany.getIterator();
    playWithIterator(weCompanyIterator);
    playWithIterator(otherCompanyIterator);
  }

  public void playWithIterator(Iterator<Music> iterator) {
    while (iterator.hasNext()) {
      Music music = iterator.next();
      System.out.println(music.getName() + " 듣는중 with iterator");
    }
  }
}
```

이터레이터를 이용하면 위처럼 공통된 함수를 만들어 반복적인 일을 하지 않아도 됩니다.

이렇게 되면서 **반복자를 다른 객체로 빼내면서 결합도를 줄일수 있었습니다.**

```java
class Main {
  public static void main(String[] args) {
    WeCompany weCompany = new WeCompany();
    weCompany.add(new Music("좋은날"));
    weCompany.add(new Music("마음"));

    OtherCompany otherCompany = new OtherCompany(5);
    otherCompany.add(new Music("hype boy"));
    otherCompany.add(new Music("Ditto"));

    MusicPlayer musicPlayer = new MusicPlayer(weCompany, otherCompany);
    musicPlayer.play();
  }
}
```

![result2](/assets/images/posts_img/cs-iteratorPattern/result2.png)

**위의 코드에서는 간단하게 보여주기 위해 생성자를 직접받아 구현하였지만 만약 인터페이스를 정의하고 그룹으로 받는다면 OCP 를 지킬 수 있습니다.**

<br>

# 참조

헤드퍼스트 디자인패턴 책