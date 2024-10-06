---
title: #1 Java Final Keyword
author: daehong
date: 2024-05-22 20:55:00 +0800
categories: [Personal IT Study, Java]
tags: [Study, Java, Final]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/java-final.jpg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: What is a final keyword in Java?
---

Java에서 **final** 키워드는 상수 선언, 상속 방지, 불변성을 보장하기 위해 사용됩니다.

이로 인하여, **개발자들의 의도치 않은 코드 변경을 막을 수 있으며**, 바뀌지 않는 값이 들어온다는 것을 명시적으로 알려줄 수 있기에 **가독성**에도 좋습니다.

Java 에서 final 은 **변수, 메서드, 클래스**에 모두 사용 할 수 있습니다.

<br>

## Final Variable

 - final로 선언된 변수는 초기화된 후 **값을 변경할 수 없습니다.** 변수는 선언 시 또는 생성자에서 초기화되어야 합니다.

 - 바꿀 수 없는 변수이므로 **Thread-Safe** 합니다.
 
 ```java
 final int MAX_VALUE = 100;
 ```

<br>
 
## Final Method

 - final로 선언된 메서드는 하위 클래스에서 **오버라이드할 수 없습니다.**
 
 - 이는 하위 클래스가 메서드의 구현을 변경하지 못하게 하고 싶을 때 유용합니다.
 
 ```java
 public final void display() {
    // 메서드 구현
 }
 ```

<br>

## Final Class

 - final로 선언된 클래스는 **상속될 수 없습니다.**
 
 - 이는 보안 이유로 상속을 방지하거나 클래스의 동작을 변경하지 않도록 보장하고 싶을 때 유용합니다.
 
 ```java
 public final class Constants {
    // 클래스 구현
 }
 ```

