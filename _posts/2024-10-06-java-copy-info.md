---
title: Java Shallow copy, Deep copy
author: daehong
date: 2024-10-06 20:55:00 +0800
categories: [Personal IT Study, Java]
tags: [Study, Java, Shallow Copy, Deep copy]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/java-copy.jpg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: What is a copy in Java?
---

2022년 정도였을까.. 첫 직장에서 이커머스 플래폼을 개발 할 때 있었던 일이다.

분명히, 객체를 복사한 후 값을 바꿨는데, 원본 객체의 값도 바뀐 것이다.

처음에는 어떤 오류인지 조차 찾지 못하였는데, 디버그를 찍어보니 이러한 오류가 있었던 것이었다.

그 때 처음 알게 된 것이 바로 얕은 복사와 깊은 복사이다.

<br>

## ✏️ Primitive Type Copy
---

얕은 복사와 깊은 복사를 알기 전에, 먼저 일반적인 원시 타입 변수를 복사할 때와, 객체 타입을 복사 할 때의 차이를 알아야 한다.

먼저 원시 타입의 복사는 다음과 같은 결과가 나온다.

```java
int a = 100;
int b = a;

a = 1;

System.out.println(a) // 1
System.out.println(b) // 100
```
	
원시타입은 Stack 메모리에 값이 직접 저장된다.
	
<br>

## ✏️ Reference Type Copy
---

참조 타입 복사는 다음과 같은 결과가 나온다.

```java
int[] a = {1, 2, 3, 4, 5};
int[] b = a;

a[0] = 100;

System.out.println(Arrays.toString(a)) // [100, 2, 3, 4, 5]
System.out.println(Arrays.toString(b)) // [100, 2, 3, 4, 5]
```
	
참조타입은 주소만 Stack 에 저장되고, 실제 값은 Heap 에 저장된다.

그래서 위 코드처럼 복사를 하면, 주소값을 복사하는거기 때문에 같은 Heap 의 값을 바라보게 된다.

그리고 그걸 얕은 복사 라고 이야기한다.

<br>

![image](https://github.com/user-attachments/assets/888b6641-63c2-42eb-8c4d-8445edc6403d)

## ✏️ Shallow Copy
---

얕은 복사

객체를 그냥 = 으로 복사하는 개념으로, 동등이 아닌 동일하게 복사를 한다. ( 객체 주소 자체를 복사해서 옮김 )

```java
public class Main {
    public static void main(String[] args) {
        // 원본 배열
        String[] arr1 = {"Apple", "Banana", "Cherry"};

        // 얕은 복사: arr2는 arr1을 가리키는 참조만 복사 (원본과 같은 주소)
        String[] arr2 = arr1;

        

        System.out.println("=== 초기 상태 ===");
        System.out.println("원본 배열 arr1: " + Arrays.toString(arr1));
        System.out.println("얕은 복사본 arr2: " + Arrays.toString(arr2));

        // 원본 배열의 값 변경
        arr1[0] = "Orange";

        System.out.println("\n=== 원본 배열 수정 후 ===");
        System.out.println("원본 배열 arr1: " + Arrays.toString(arr1));
        System.out.println("얕은 복사본 arr2: " + Arrays.toString(arr2)); // 얕은 복사본도 변경됨
    }
}
```

```java
class ArrayContainer implements Cloneable {
    int[] data;

    // 생성자
    ArrayContainer(int[] data) {
        this.data = data;
    }

    // 얕은 복사 (Cloneable 사용)
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // 배열 내용을 쉽게 확인하기 위한 toString 메서드
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("[");
        for (int i = 0; i < data.length; i++) {
            sb.append(data[i]);
            if (i < data.length - 1) sb.append(", ");
        }
        sb.append("]");
        return sb.toString();
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        // 원본 배열과 객체 생성
        int[] originalData = {1, 2, 3, 4, 5};
        ArrayContainer original = new ArrayContainer(originalData);

        // 얕은 복사 (Cloneable 사용)
        ArrayContainer shallowCopy = (ArrayContainer) original.clone();

        System.out.println("=== 초기 상태 ===");
        System.out.println("원본: " + original);
        System.out.println("얕은 복사본: " + shallowCopy);

        // 원본 배열의 값을 변경
        original.data[0] = 100;

        System.out.println("\n=== 원본 배열 변경 후 ===");
        System.out.println("원본: " + original);        // 원본 배열이 100으로 변경됨
        System.out.println("얕은 복사본: " + shallowCopy); // 얕은 복사본도 100으로 변경됨
    }
}
```
	
이렇게 할 시, a == b를 하면 true가 나온다.

그렇기 때문에 b 의 값을 변경하면, a 를 통해 조회하였을 때에도 변경된 값을 조회하게 된다.

같은 주소를 바라보기 때문이다.
	
재정의 하지 않은 clone();을 사용하거나, = 을 통하여 복사한 경우 이렇게 얕은 복사가 일어난다.

<br>

## ✏️ Deep Copy
---

깊은 복사

```java
public class Main {
    public static void main(String[] args) {
        // 원본 배열
        String[] arr1 = {"Apple", "Banana", "Cherry"};

        // 얕은 복사: arr2는 arr1을 가리키는 참조만 복사 (원본과 같은 주소)
        String[] arr2 = arr1;

        // 깊은 복사: 새로운 배열을 생성하고 각 값을 복사
        String[] arr3 = Arrays.copyOf(arr1, arr1.length);

        System.out.println("=== 초기 상태 ===");
        System.out.println("원본 배열 arr1: " + Arrays.toString(arr1));
        System.out.println("얕은 복사본 arr2: " + Arrays.toString(arr2));
        System.out.println("깊은 복사본 arr3: " + Arrays.toString(arr3));

        // 원본 배열의 값 변경
        arr1[0] = "Orange";

        System.out.println("\n=== 원본 배열 수정 후 ===");
        System.out.println("원본 배열 arr1: " + Arrays.toString(arr1));
        System.out.println("얕은 복사본 arr2: " + Arrays.toString(arr2)); // 얕은 복사본도 변경됨
        System.out.println("깊은 복사본 arr3: " + Arrays.toString(arr3)); // 깊은 복사본은 변경되지 않음
    }
}
```

```java
class ArrayContainer implements Cloneable {
    int[] data;

    // 생성자
    ArrayContainer(int[] data) {
        this.data = data;
    }

    // 얕은 복사 (Cloneable 사용)
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // 깊은 복사 (직접 배열 복사)
    protected ArrayContainer deepCopy() {
        // 새로운 배열을 생성하고 각 원소를 개별적으로 복사
        int[] newData = new int[data.length];
        for (int i = 0; i < data.length; i++) {
            newData[i] = data[i];
        }
        return new ArrayContainer(newData);
    }

    // 배열 내용을 쉽게 확인하기 위한 toString 메서드
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder("[");
        for (int i = 0; i < data.length; i++) {
            sb.append(data[i]);
            if (i < data.length - 1) sb.append(", ");
        }
        sb.append("]");
        return sb.toString();
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        // 원본 배열과 객체 생성
        int[] originalData = {1, 2, 3, 4, 5};
        ArrayContainer original = new ArrayContainer(originalData);

        // 얕은 복사 (Cloneable 사용)
        ArrayContainer shallowCopy = (ArrayContainer) original.clone();

        // 깊은 복사 (직접 배열 복사)
        ArrayContainer deepCopy = original.deepCopy();

        System.out.println("=== 초기 상태 ===");
        System.out.println("원본: " + original);
        System.out.println("얕은 복사본: " + shallowCopy);
        System.out.println("깊은 복사본: " + deepCopy);

        // 원본 배열의 값을 변경
        original.data[0] = 100;

        System.out.println("\n=== 원본 배열 변경 후 ===");
        System.out.println("원본: " + original);        // 원본 배열이 100으로 변경됨
        System.out.println("얕은 복사본: " + shallowCopy); // 얕은 복사본도 100으로 변경됨
        System.out.println("깊은 복사본: " + deepCopy);   // 깊은 복사본은 변경되지 않음
    }
}

```

Cloneable 인터페이스를 implement 한 후, clone( )을 재정의하거나

새 메모리를 생성하여 값들을 하나하나 옮기거나 직렬화 역직렬화를 통해 Deep Copy를 수행할 수 있다.

혹은 값을 하나하나 복사하는 메서드를 만들어 사용할 수도 있다.

아무튼 깊은 복사는 객체 주소는 복사하지 않되, 내용은 그대로 복사하는 방법을 말한다.

