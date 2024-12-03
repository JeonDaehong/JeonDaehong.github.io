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

### ▶ static 변수

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