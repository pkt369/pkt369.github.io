---
title: "프록시 패턴"
excerpt: "프록시 패턴에 대하여"

categories:
  - CS
tags:
  - [design pattern, proxy]

permalink: /CS/design-pattern/proxy

toc: true
toc_sticky: true
published: true

date: 2023-12-28
last_modified_at: 2023-12-28

---

# 프록시 패턴

책에서 말하는 프록시 패턴은…

> 특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공합니다.

제가 생각하는 프록시 패턴은…

> 진짜 객체를 대변할 수 있는 가짜 객체라고 생각합니다.

<br>



# 프록시 패턴은 언제 사용할까

**모두 실제 객체에 접근하기 전 프록시 객체에 접근하도록 하여 보안이나 메모리 이점등을 가져갈 수 있습니다.**

<br>

프록시 패턴은 여러가지 형태로 사용할 수 있습니다.

- 방화벽 프록시 (Firewall Proxy)
- 스마트 레퍼런스 프록시 (Smart Reference Proxy)
- 캐싱 프록시 (Caching Proxy)
- 동기화 프록시 (Synchronization Proxy)
- 복잡도 숨김 프록시 (Complexity Hiding proxy)
- 지연 복사 프록시(Copy-On-Write Proxy)

등등 있습니다.

<br>



## 프록시 서버

프록시 서버에서 방화벽과 캐싱역할을 담당할 수 있습니다. 

방화벽의 경우에는 **우리가 원하는 IP 만 접근을 가능하게 하거나 시간안에 제한된 요청만 가능하게 한다던지 여러 보안적인 기능을 담당**하게 할 수 있습니다.

<br>

캐시의 경우 **자주 요청되는 것들을 캐싱해두었다가 같은 요청이 오면 본 서버에 가지않고 프록시 서버에서 리턴**해줄 수 있습니다.

![proxyServer](/assets/images/posts_img/cs-proxyPattern/proxyServer.png)

예시로 우리가 멀리있는 미국 유튜브 서버에서 어떻게 빨리 영상을 가져올까요?

정답은 한국에 프록시 서버를 두었기 때문입니다.

![youtube](/assets/images/posts_img/cs-proxyPattern/youtube.png)

한국에서 비디오를 재생했을때 프록시 서버는 캐싱되어 있지 않은 영상의 경우 미국의 서버에서 가져와서 프록시 캐싱 서버에 저장하고 클라이언트에게 요청을 줍니다.

이후 다른 사용자가 같은 영상을 재생했을 경우 본 서버까지 가지않고 프록시 서버에서 데이터를 리턴해줍니다.

<br>



## 스프링 JPA의 지연 로딩

스프링의 JPA 에서는 즉시 로딩과 지연 로딩이 존재합니다. 이것은 지연 복사 프록시와 비슷합니다.

즉시 로딩의 경우에는 말그대로 즉시 가져오는 것이고, 지연 로딩은 필요한 시점에서 가져오는 것입니다.

![jpaProxy](/assets/images/posts_img/cs-proxyPattern/jpaProxy.png)

Proxy 와 Entity 가 가진 함수는 같으며 실제로 접근하기까지 프록시 객체가 실제 객체를 대신해서 있습니다.

하지만 객체에 접근하게 되면 프록시에서 실제 객체를 생성해서 메모리와 시간적인 이점을 가져갈 수 있습니다.

![hibernateProxy](/assets/images/posts_img/cs-proxyPattern/hibernateProxy.png)

실제 jpa 를 실행시켜보면 위처럼 hibernate 에서 프록시 객체로 만들어 관리하게 됩니다.

<br>



# 참조

헤드퍼스트 디자인패턴 책