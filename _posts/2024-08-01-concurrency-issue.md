---
title: How to Solve Concurrency Issue ?!
author: daehong
date: 2024-07-28 20:55:00 +0800
categories: [Develop Issue, Concurrency Issue]
tags: [Develop, Develop Issue, Concurrency, Concurrency Issue, Java, Spring, Issue]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/concurrency-issue.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: How to Solve Concurrency Issue ?!
---

## ✏️ 개발하며, 마주한 이슈
---
2022년 신입 개발자 시절로 잠시 되돌아가 보자.
당시 외주를 받아 쇼핑몰 애플리케이션 개발 프로젝트에 투입하게 되었고, 많은 것들을 배우고 경험하며 개발을 하고 있었다.
꽤 장기 프로젝트였기 때문에, 1차 ~ 3차 오픈으로 나뉘어져 있었는데, 문제는 1차 오픈을 하고난 직후에 생기게 되었다.

1차 오픈 이벤트로 판매하던 물건이었는데, 재고가 하나밖에 남지 않은 물건임에도 주문이 2명에게 들어간 것이다.

결과적으로, 나는 **syncronized 를 사용하여 문제를 우선 해결**하였고 ( 고객사측에서 빠른 대응을 원했기 때문에 ),
이후에 서버가 증설 될 것을 대비하여 **DB Level 에서나, 아니면 Redis 를 활용한 Lock 을 사용해야 한다고 건의** 하였다.

<br>

## ✏️ 동시성 이슈가 생기는 원인
---

<br>

## ✏️ 프로세스와 스레드
---
그럼 프로세스와 스레드에 대해서 알아보자.

<br>

## ✏️ 멀티스레드와 톰캣
---

<br>

## ✏️ 가시성, 동시성, 원자성
---

<br>

## ✏️ 여러가지 동시성 문제
---

<br>

## ✏️ 코드 Level에서 해결하는 방법
---

<br>

## ✏️ DB Level 에서 해결하는 방법
---

<br>

## ✏️ Redis 를 사용하여 해결하는 방법
---

<br>

## ✏️ 결론
---


<br>

<br>

![image](https://github.com/user-attachments/assets/85c6eb1b-ac65-4906-a278-6f36349c261a)