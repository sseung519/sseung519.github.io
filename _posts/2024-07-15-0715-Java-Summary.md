---
title: "자바 패키지 구조와 다양한 유형의 내부 클래스"
date: 2024-07-15 17:40:00 +0900
categories: [Java]
tags: [Java]
---
# Java 패키지 및 내부 클래스

## 추상 클래스

추상 클래스는 클래스의 일부 구현을 제공하면서도, 하위 클래스가 반드시 구현해야 할 메서드를 정의하는데 사용된다.

### 예제 코드
```java
public abstract class Mammal {
    public abstract void giveBirth();
}

public interface Birds {
    void fly();
}

public class Bat extends Mammal implements Birds {
    @Override
    public void giveBirth() {
        System.out.println("새끼를 낳다");
    }

    @Override
    public void fly() {
        System.out.println("하늘을 날다");
    }

    public static void main(String[] args) {
        Bat bat = new Bat();
        bat.fly();
        bat.giveBirth();
    }
}
```

### 설명
- `Mammal`은 추상 클래스이다. 추상 메서드 `giveBirth`를 가지고 있다.
- `Bat` 클래스는 `Mammal` 추상 클래스를 상속받고 `Birds` 인터페이스를 구현한다.
- `Bat` 클래스는 추상 메서드 `giveBirth`와 인터페이스 메서드 `fly`를 구현한다.

## 패키지와 임포트

- **패키지**는 자바에서 클래스를 그룹화하는 방법. 패키지는 고유의 네임스페이스를 제공.
- **패키지 선언**: `package` 키워드를 사용하여 클래스 파일의 첫 줄에 패키지를 선언.
  ```java
  package com.example.libs;
  ```
- **임포트 문**: 다른 패키지에 있는 클래스를 사용하기 위해 `import` 문을 사용.
  ```java
  import com.example.libs.Car;
  import kr.seoul.sesac.util.libs.*;
  import com.example.libs.*;
  ```

### 접근 제한자

- **public**: 모든 클래스에서 접근 가능.
- **protected**: 동일 패키지와 상속받은 클래스에서 접근 가능.
- **default** (접근 제한자를 명시하지 않음): 동일 패키지 내에서만 접근 가능.
- **private**: 동일 클래스 내에서만 접근 가능.

### 내부 클래스 (Inner Class)

내부 클래스는 클래스 내부에 또 다른 클래스를 정의할 수 있다. 이러한 클래스를 "내부 클래스"라고 합니다. 내부 클래스는 세 가지 유형으로 나뉩니다: 멤버 내부 클래스, 정적 내부 클래스, 로컬 내부 클래스. 각 유형에 대한 예제와 설명을 아래에 정리했습니다.

### 1. 멤버 내부 클래스 (Member Inner Class)

멤버 내부 클래스는 외부 클래스의 멤버로서 정의된다. 외부 클래스의 인스턴스와 연관되어 있고, 외부 클래스의 인스턴스 변수와 메서드에 접근할 수 있다.

#### 예제 코드
```java
public class OuterClass {
    public static void main(String[] args) {
        OuterClass oc = new OuterClass();
        OuterClass.InnerClass ic = oc.new InnerClass();
        System.out.println(ic.c);
        System.out.println(InnerClass.d); // static
    }
    private int a = 5; // outer class's member variable
    private static int b = 10; // outer class's static variable
    private class InnerClass { // member inner class
        private int c =  100; // inner class's member variable
        private static int d =  500; // inner class's static variable
        public void display () { // inner class's member method
            System.out.println(a + ", " + b + ", " + c + ", " + d); // member는 static에 접근이 가능하다
        }
        public static void print() { // inner class's static method
            System.out.println();
        }
    }
}
```

#### 설명
- `InnerClass`는 `OuterClass`의 멤버 내부 클래스이다.
- `InnerClass`의 인스턴스를 만들기 위해 `OuterClass`의 인스턴스를 먼저 생성해야 한다.
- 멤버 내부 클래스는 외부 클래스의 멤버 변수와 메서드에 접근할 수 있다.
- `InnerClass`의 정적 변수와 메서드도 정의할 수 있지만, 자바의 버전이 제한을 둘 수 있다.

### 2. 정적 내부 클래스 (Static Inner Class)

정적 내부 클래스는 외부 클래스의 정적 멤버로 정의된다. 외부 클래스의 인스턴스와 독립적으로 존재할 수 있으며, 외부 클래스의 인스턴스 변수에는 접근할 수 없지만 정적 변수에는 접근할 수 있다.

#### 예제 코드
```java
public class OuterClass1 {
    public static void main(String[] args) {
        OuterClass1.InnerClass1 ic = new OuterClass1.InnerClass1();        
        ic.display();
        ic.print();
    }
    private int a = 5; // outer class's member variable
    private static int b = 10; // outer class's static variable
    
    private static class InnerClass1 { // static inner class
        private int c = 100; // static inner class's member variable
        private static int d = 500; // static inner class's static variable
        
        public void display() { // static inner class's member method
            System.out.println(b + ", " + c + ", " + d);
        }
        
        public static void print() { // static inner class's static method
            System.out.println(b + ", " + d);
        }
    }
}
```

#### 설명
- `InnerClass1`는 `OuterClass1`의 정적 내부 클래스이다.
- 정적 내부 클래스의 인스턴스는 외부 클래스의 인스턴스 없이도 생성할 수 있다.
- 정적 내부 클래스는 외부 클래스의 정적 변수와 메서드에 접근할 수 있지만, 인스턴스 변수에는 접근할 수 없다.

### 3. 로컬 내부 클래스 (Local Inner Class)

로컬 내부 클래스는 메서드 내부에 정의되며, 해당 메서드 내에서만 사용될 수 있다. 로컬 내부 클래스는 외부 클래스의 멤버 변수와 상수뿐만 아니라 메서드의 지역 변수에도 접근할 수 있다. 단, 지역 변수는 final 이어야 한다.

#### 예제 코드
```java
public class OuterClass2 {
    public static void main(String[] args) {
        OuterClass2 oc = new OuterClass2();
        oc.display();
    }

    void display() {
        int a = 5; // local variables
        class InnerClass2 { // local class
            int b = 100; // local inner class's member variable
            static int c = 500; // local inner class's static variable

            void print() {
                System.out.println(a + ", " + b + ", " + c);
            }
        }
        InnerClass2 ic = new InnerClass2();
        System.out.println(ic.b); // member variable 이기 때문에 주소로 접근
        System.out.println(InnerClass2.c); // static이기 때문에 접근 가능
    }
}
```

#### 설명
- `InnerClass2`는 `display` 메서드 내에 정의된 로컬 내부 클래스이다.
- 로컬 내부 클래스는 메서드 내에서만 사용될 수 있다.
- 로컬 내부 클래스는 외부 클래스의 멤버 변수와 메서드의 지역 변수에 접근할 수 있다.
- 로컬 내부 클래스는 정적 변수와 정적 메서드를 가질 수 없지만, 자바 16 이후로는 정적 변수를 가질 수 있다.

### 요약

- **패키지와 임포트**: 패키지를 사용하여 코드를 조직화하고, `import` 문을 통해 다른 패키지의 클래스를 사용할 수 있다.
- **접근 제한자**: `public`, `protected`, `default`, `private` 접근 제한자는 클래스 멤버에 대한 접근 권한을 제어한다.
- **내부 클래스**:
  - **멤버 내부 클래스**: 외부 클래스의 인스턴스 변수와 메서드에 접근할 수 있다.
  - **정적 내부 클래스**: 외부 클래스의 정적 변수와 메서드에 접근할 수 있다.
  - **로컬 내부 클래스**: 메서드 내에서만 사용될 수 있으며, 외부 클래스의 멤버 변수와 메서드의 지역 변수에 접근할 수 있다.

이와 같은 내부 클래스는 특정 상황에서 코드를 더 간결하고 논리적으로 구성하는 데 유용하다.
