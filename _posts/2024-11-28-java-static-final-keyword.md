---
layout: post
title:  "[#1] Understanding Java static and final"
author: daehong
categories: [ Java ]
image: assets/images/java-static-final-keyword.jpg
featured: true
rating: 5
---

자바에서 `static`과 `final` 키워드의 조합은 주로 **상수(constant)**를 정의할 때 사용됩니다. `static` 키워드는 클래스 수준에서 메모리를 관리하도록 하며, `final` 키워드는 변수가 한 번 초기화되면 그 값을 변경할 수 없게 만듭니다. 이를 통해 프로그램 내에서 전역적으로 공유되면서도 불변성을 가지는 값을 표현할 수 있습니다.

`static final` 변수는 클래스가 로드되는 시점에 메모리에 할당되며, 프로그램 종료 시까지 해당 메모리에 존재합니다. 이 변수는 객체 생성과 관계없이 클래스명.변수명 형태로 접근할 수 있습니다. 또한, `final`의 특성으로 인해 초기화된 이후에는 값을 수정할 수 없으므로 상수로 사용하기에 적합합니다.

예를 들어, 파이 값을 저장하는 `Math.PI`처럼 항상 일정한 값을 가지는 데이터를 `static final`로 정의하면, 모든 객체에서 동일한 값을 공유하면서도 실수로 변경될 위험을 원천적으로 방지할 수 있습니다. 이는 코드의 안정성과 가독성을 높이는 데 기여합니다.

`static final` 키워드는 변수뿐 아니라 클래스 로드 시점의 초기화 로직과도 밀접하게 연관이 있습니다. 예를 들어, 초기값이 복잡한 계산을 필요로 한다면, 이를 `static` 블록 내에서 초기화하여 실행 시점을 명확히 할 수 있습니다. 다만, 너무 많은 `static final` 변수를 사용하는 것은 객체 지향 설계의 유연성을 떨어뜨릴 수 있으므로, 적절히 사용하는 것이 중요합니다.

이 글에서는 `static final` 키워드를 활용하여 상수를 정의하고, 이를 통해 보다 명확하고 안정적인 코드를 작성하는 방법을 살펴보겠습니다.

<br>

## ✏️ static 키워드
---

자바에서 `static` 키워드는 클래스 수준의 멤버(변수와 메서드)를 정의할 때 사용되며, 객체 지향 프로그래밍에서 매우 중요한 역할을 합니다. 일반적으로 변수와 메서드는 특정 객체에 속하지만, `static` 키워드를 사용하면 이들이 객체가 아닌 클래스에 속하게 됩니다.

`static` 멤버는 클래스가 처음 로드될 때 메모리에 단 한 번 할당되며, 모든 인스턴스가 이를 공유합니다. 이는 데이터의 공유와 메모리 효율성을 높이고, 클래스 수준에서 직접 접근 가능하다는 장점이 있습니다. 하지만 잘못 사용하면 데이터 변경으로 인한 예기치 않은 부작용이 발생할 수 있으므로 신중히 사용해야 합니다.

주요 특징에 대하여 가볍에 살펴보면 다음과 같습니다.

1. 클래스 수준에서 공유
static 변수와 메서드는 객체가 아니라 클래스 자체에 속하며, 모든 인스턴스에서 공유됩니다. 따라서 클래스명으로 직접 접근할 수 있습니다.

2. 객체 생성 없이 접근 가능
static 멤버는 클래스 로드 시 메모리에 할당되므로, 객체를 생성하지 않아도 바로 접근할 수 있습니다.

3. 한 번만 초기화
static 변수는 클래스가 메모리에 로드될 때 단 한 번 초기화됩니다. 프로그램 종료 시까지 메모리에 유지됩니다.

4. 메모리 효율성
동일한 데이터를 여러 객체가 공유하므로 메모리 사용량을 줄일 수 있습니다. 그러나 GC의 대상이 되지 않으므로, 너무 남발하여 사용 할 시 메모리에 부담이 될 수 있습니다.

<br>

자바에서 `static` 키워드는 굉장히 유용하면서도, 잘못 사용하면 문제가 될 수 있는 양날의 검 같은 존재입니다.
프로그램을 작성하다 보면, 데이터를 여러 객체에서 공유하거나, 객체와 관계없이 동작하는 기능을 구현해야 할 때가 있는데, 이때 static 키워드가 큰 역할을 합니다. 하지만 동시성 문제나 메모리 누수 같은 단점도 있기 때문에 신중하게 사용해야 합니다.

`static` 변수는 클래스가 처음 메모리에 로드될 때 한 번만 생성되고, 모든 객체가 이를 공유합니다. 그래서 같은 데이터를 여러 객체가 따로따로 저장할 필요가 없죠. 예를 들어, 게임의 전역 설정값이나 공통된 리소스를 `static` 변수로 관리하면 메모리를 절약할 수 있습니다.

`static` 메서드는 객체를 생성하지 않고도 호출할 수 있어서 유틸리티 기능을 구현할 때 자주 사용됩니다. 예를 들어, `Math.sqrt()`처럼 간단한 계산 로직은 객체와 무관하게 동작하니 `static` 메서드로 정의하면 좋습니다.

그러나, `static`은 클래스에 속하다 보니 객체 지향의 기본 원칙인 캡슐화와 다형성을 저해할 수 있습니다. 객체마다 다른 동작이 필요한 경우에도 `static`을 사용하면 유연성이 떨어지고, 코드가 딱딱해질 수 있습니다.
예를 들어, 어떤 데이터를 객체별로 관리해야 하는데 실수로 `static`으로 선언하면, 모든 객체가 같은 데이터를 공유해버리죠.

또한 멀티스레드 환경에서 `static` 변수는 공유 메모리를 사용하기 때문에, 동시에 여러 스레드가 값을 수정하려고 하면 **데이터 레이스(Data Race)**가 발생할 수 있습니다. 이로 인해 값이 의도와 다르게 처리될 가능성이 높아집니다.

예시를 들어보면, 

```
class Counter {
    static int count = 0;

    static void increment() {
        count++;
    }
}
```

이러한 코드에 동시에 여러 Thread 가 접근한다면 **count**가 제대로 증가하지 않을 수 있습니다. 이를 해결하려면 `synchronized` 키워드를 사용하거나,` AtomicInteger` 같은 동시성-safe 클래스를 사용하는 것이 좋습니다.

이 부분은 추후 동시성 이슈에 대한 포스팅을 진행 할 때, 자세하게 다루도록 하겠습니다.

또 메모리 누수 문제도 있습니다.

`static` 변수는 클래스가 언로드되지 않는 한 메모리에 계속 남아 있습니다. 그래서 필요 없는 객체를 `static` 변수로 참조하고 있다면, **Garbage Collector**가 이를 회수하지 못해 메모리 누수가 발생할 수 있습니다.

마지막으로 초기화 순서 문제가 발생 할 수 있습니다.

`static` 변수나 블록의 초기화 순서를 제대로 관리하지 않으면, 예상치 못한 동작이 발생할 수 있습니다. 예를 들어, 두 static 변수가 서로를 참조할 때 초기화 순서가 꼬이면 런타임 에러가 날 수도 있습니다.

<br>

### ▶ static 변수

static 변수는 클래스 로드 시 Method Area에 저장되며, 객체가 아닌 클래스에 속합니다.
static 변수는 Method Area에 저장되므로, 모든 객체에서 동일한 값을 참조합니다.
즉, 모든 객체가 해당 변수를 공유하며, 프로그램 종료 시까지 메모리에 유지되며 이는 객체 생성 수에 관계없이 하나의 메모리만 차지합니다.

- 공유 메모리: 클래스의 모든 인스턴스가 동일한 값을 공유합니다.

- 클래스 로드 시 초기화: 클래스가 JVM에 로드될 때 한 번만 초기화됩니다.

- 클래스명으로 접근 가능: 객체 생성 없이 클래스명.변수명으로 접근 가능합니다.

예시를 들면, 게임 내에서 공유 자원을 관리할 때 공유하는 변수로 사용 할 수 있습니다.

```java
class ResourceManager {
    static int totalMinerals = 500; // 모든 객체가 공유하는 자원 (미네랄)
    static int totalGas = 200; // 모든 객체가 공유하는 자원 (가스)

    // 자원 추가
    static void gatherMinerals(int amount) {
        totalMinerals += amount;
    }

    static void gatherGas(int amount) {
        totalGas += amount;
    }
}

public class Main {
    public static void main(String[] args) {
        // ResourceManager.totalMinerals와 totalGas는 공유됨
        ResourceManager.gatherMinerals(50);
        ResourceManager.gatherGas(20);

        System.out.println("Total Minerals: " + ResourceManager.totalMinerals); // 550
        System.out.println("Total Gas: " + ResourceManager.totalGas); // 220
    }
}
```

### ▶ static 메서드

static 메서드는 객체 상태와 무관하며, 클래스에 종속된 동작을 정의합니다.

- 객체 없이 호출 가능: 클래스명.메서드명으로 호출할 수 있습니다.

- static 변수만 접근 가능: 인스턴스 변수는 접근할 수 없습니다.

static 메서드도 Method Area에 저장되며, 클래스 로드 시 함께 메모리에 올라갑니다.
이 메서드는 객체를 생성하지 않고 호출되므로, 전역적인 동작(예: 유틸리티 메서드)을 수행할 때 적합합니다.

예시를 들면, 게임 내에서 두 지점 사이의 거리를 계산하는 메서드 등, 공유 메서드로 사용 할 수 있습니다.

```java
class MathUtils {
    // 두 점 사이의 거리 계산
    static double calculateDistance(int x1, int y1, int x2, int y2) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
    }
}

public class Main {
    public static void main(String[] args) {
        // static 메서드는 객체 생성 없이 호출 가능
        double distance = MathUtils.calculateDistance(0, 0, 10, 10);
        System.out.println("Distance: " + distance); // 출력: Distance: 14.142135623730951
    }
}
```

### ▶ static 블록

static 블록은 클래스 로드 시 한 번만 실행되는 코드 블록입니다.
주로 static 변수의 초기화나 설정 작업에 사용됩니다.

 - 클래스 로드 시 자동 실행: 객체 생성과 관계없이 한 번 실행됩니다.

 - 여러 개 사용 가능: 클래스 내에서 여러 static 블록을 정의할 수 있으며, 작성 순서대로 실행됩니다.
 
static 블록의 코드는 클래스가 Method Area에 로드될 때 실행됩니다. 이 블록은 static 변수 초기화와 관련된 로직을 처리합니다.

예시를 들면, 게임 설정을 초기화 하는 것처럼 최초 로드 시 한 번만 실행 되는 부분으로 사용 할 수 있습니다.

```java
class GameSettings {
    static String mapName;
    static int maxPlayers;

    // static 블록: 클래스 로드 시 한 번 실행
    static {
        mapName = "Lost Temple";
        maxPlayers = 8;
        System.out.println("Game settings initialized.");
    }
}

public class Main {
    public static void main(String[] args) {
        // static 블록은 클래스 로드 시 실행되므로 출력이 이미 나타남
        System.out.println("Map: " + GameSettings.mapName); // 출력: Map: Lost Temple
        System.out.println("Max Players: " + GameSettings.maxPlayers); // 출력: Max Players: 8
    }
}
```

