---
title: Java OOP, SOLID Deep Dive Presentation
author: daehong
date: 2024-08-19 22:00:00 +0800
categories: [IT Presentation, Company]
tags: [Java, Presentation, Features of Java, Company, Conference]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/oop-java-presentation.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Features of Java Deep Dive Presentation
---

2024년 8월 회사에서 가벼운 컨퍼런스가 있었습니다.
그리고 저도 주니어 개발자로서, Java에 대한 발표를 해야했습니다.
동일 동등에 대한 발표, String에 대한 발표, JVM에 대한 발표 등등
여러가지가 있을 수 있지만,
저는 자바의 기본이자 기둥이 되는, 객체지향과 SOLID 에 대해서 발표하기로 하였습니다.

<br>

### 자바의 등장 배경

먼저 자바의 등장 배경을 간단히 알아보겠습니다.
자바는 C 와 C++ 의 문제점을 개선하기 위해 제임스 고슬링( James Gosling )이라는 사람이 패트릭( Patrick ), 노튼 ( Nauhton ) 과 함께 개발하였습니다.
여기서 중점적으로 해결하려 하였던 C, C++ 의 문제점은 메모리를 개발자가 직접 제어해야 한다는 문제, 절차지향형 코드이기에 복잡성이 높아 개발자가 오류를 만들어 내기 쉽다는 문제, 그리고 플랫폼에 따라 다른 결과를 출력할 수 있다는 문제가 있습니다.
그래서 제임스 고슬링은 자바라는 프로그래밍 언어를 개발 할 때, GC 를 통한 자동 메모리 관리와, JVM 을 통한 독립적인 플랫폼 사용, 그리고 객체지향 프로그래밍 방법을 통한 복잡성 감소에 중점을 두었습니다.


