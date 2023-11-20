---
title: "Design pattern 개념"
excerpt: "Design pattern 개념"

categories:
  - Design Pattern
tags:
  - [design pattern]

permalink: /Design-Pattern/basic/

toc: true
toc_sticky: true

date: 2023-05-06
last_modified_at: 2023-05-06
---

## Design Pattern 이란

디자인 패턴이란 
- 소프트웨어를 설계를 할 때 이미 겪었던 문제들을 패턴으로 만들어 놓은 것을 의미합니다.
- 이미 검증된 디자인 패턴을 사용하면 유지보수성, 확장성 및 가독성이 올라갑니다.
- 디자인 패턴은 로버트 마틴의 SOLID를 지키고 있습니다.
<br>
<br>

## GoF (Gang of Four) 디자인 패턴
- 소프트웨어 개발 영역에서 디자인 패턴을 구체화하고 체계화한 사람들입니다.
  - 에리히 감마(Erich Gamma), 리차드 헬름(Richard Helm), 랄프 존슨(Ralph Johnson), 존 블리시디스(John Vissides)
- 23가지 디자인 패턴을 정리하고 디자인 패턴은 생성(Creational), 구조(Structural), 행위(Behavioral) 3가지로 분류하였습니다.

### 생성 패턴 (Creatinal)
- 객체 생성에 관련된 패턴입니다.
- 객체의 생성과 조합 부분을 캡슐화해 코드가 변경이 되어도 구현 코드에 영향이 가지않도록 해주는 패턴입니다.
- 종류
  - 추상 팩토리 (Abstract Factory)
  - 빌더 (Builder)
  - 팩토리 (Factory)
  - 프로토타입 (Prototype)
  - 싱글톤 (Singleton)
<br>

### 구조 패턴 (Structural)
- 클래스나 객체를 조합해 더 큰 구조를 만드는 패턴입니다.
- 예) 서로다른 인터페이스를 지닌 2개의 객체를 묶어 단일 인터페이스를 제공하거나 객체들을 서로 묶어 새로운 기능을 제공하는 패턴입니다.
- 종류
  - 어뎁터 (Adapter)
  - 브릿지 (Bridge)
  - 컴퍼지트 (Composite)
  - 데커레이터 (Decorator)
  - 퍼사드 (Facade)
  - 플라이웨이트 (Flyweight)
  - 프록시 (Proxy)

<br>

### 행위 패턴 (Behavioral)
- 객체나 클래스 사이의 알고리즘이나 <strong>책임 분배</strong>에 관련된 패턴입니다.
- 한 객체가 혼자 수행할 수 없는 작업을 여러개의 객체로 어떻게 분배하는지, 그리고 결합도를 최소화하는 패턴입니다.
- 종류
  - 책임 연쇄 (Chain of Responsibility)
  - 커맨드 (Command)
  - 인터프린터 (Interpreter)
  - 이터레이터 (Iterator)
  - 미디에이터 (Mediator)
  - 메멘토 (Memento)
  - 옵저버 (Observer)
  - 스테이트 (State)
  - 스트레티지 (Strategy)
  - 템플릿 메서드 (Template Method)
  - 비지터 (Visitor)


## Reference
https://gmlwjd9405.github.io/2018/07/06/design-pattern.html
https://www.hanbit.co.kr/channel/category/category_view.html?cms_code=CMS8616098823








