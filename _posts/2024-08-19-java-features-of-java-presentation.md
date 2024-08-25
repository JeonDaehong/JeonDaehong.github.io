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

2024년 8월 회사에서 가벼운 **컨퍼런스**가 있었다.

그리고 필자도 주니어 개발자로서, Java에 대한 발표를 해야했다.

동일 동등에 대한 발표, String에 대한 발표, JVM에 대한 발표 등등 여러가지가 있을 수 있지만,
필자는 자바의 기본이자 기둥이 되는, 객체지향과 SOLID 에 대해서 발표하기로 하였다.

그 이유는 **객체지향 프로그래밍은 자바의 뿌리이자, 근간이 되는 자바의 핵심 요소**이기 때문이다.

그러나 코드 구현을 먼저 배워온 개발자들에게는 왜 이렇게 개발해야 되는지도 모른 채 그저 학교나 학원에서 배운대로 객체지향 언어를 사용하고,

객체지향적인 방법으로 구현만 할 줄 아는 상태로 연차만 쌓여가는 경우들이 종종 있었다.

**지피지기면 백전백승**이라는 말이 있듯, 개발자들이 보다 자바를 깊게 이해하고, 좋은 설계를 하고, 좋은 개발을 하기 위해서는 객체지향이 무엇인지를 알아야 된다고 생각했고,

그만큼 이 부분이 자바의 가장 중요한 요소라고 생각했기 때문에 선택하게 되었다.

그것이 내가 발표 주제로 **객체지향**을 선택한 이유이다.

그래서 먼저 자바의 등장 배경으로 시작해서 객체지향 프로그래밍이란 무엇인지, 그리고 **객체지향의 특징 4가지**를 설명하고,

마지막으로 **객체지향 프로그래밍을 보다 견고하게 설계하기 위한 이정표가 되어주는 SOLID**까지 알아보도록 하겠다.

<br>

### ✏️ 자바의 등장 배경
---

**먼저 자바의 등장 배경을 간단히 알아보겠다.**

자바는 C와 C++의 문제점을 개선하기 위해 제임스 고슬링(James Gosling)이라는 사람이 패트릭(Patrick), 노튼(Naughton)과 함께 개발하였다.

여기서 중점적으로 해결하려 하였던 C, C++의 문제점은 **메모리를 개발자가 직접 제어해야 한다는 문제, 절차지향형 코드이기에 복잡성이 높아 개발자가 오류를 만들어내기 쉽다는 문제, 그리고 플랫폼에 따라 다른 결과를 출력할 수 있다는 문제**가 있다.

그래서 제임스 고슬링은 자바라는 프로그래밍 언어를 개발할 때, **GC를 통한 자동 메모리 관리와, JVM을 통한 독립적인 플랫폼 사용, 그리고 객체지향 프로그래밍 방법을 통한 복잡성 감소**에 중점을 두었다.

<br>

### ✏️ 절차지향 vs 객체지향
---

