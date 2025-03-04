# JVM 이란 무엇인가

> Java JVM (Virtual Machine) 은 Java 프로그램을 실행하는 플랫폼 컴포넌트입니다.

런타임 시 VM은 Java 컴파일러가 컴파일한 Java 바이트 코드를 해석합니다. VM은 언어와 기본 운영체제 및 기본 운영 체제 및 하드웨어 간의 **번역기**로 동작합니다.

**A Java virtual machine (JVM) is a virtula machine that enables a computer to run Java programs as well as programs written in other languages that are also compiled to Java bytecode.**

**JVM(Java Virtual Machine) runs Java Applications as a run-time engine. JVM is part of JRE(Java Runtime Environment).**

![Jvm Spec - wiki](https://github.com/user-attachments/assets/5db509ea-82c0-4bbc-a2e7-7bdb6be1ba23)
![Jvm - geeks](https://github.com/user-attachments/assets/7f8d4793-279a-44ed-b91d-1aef08bd5355)

## JVM 의 스펙
- Class Loader
- JVM Memory
  - Method Area
  - Heap 
  - JVM Language Stacks
  - PC Registers
  - Native Method Stacks
- Excution Engine 
  - Native Method Interface
  - Navtive Method Libraies

## Class Loader
JVM 클래스 로더는 클래스 파일을 실행시킨다. 클래스 파일이란 Java bytecode를 포함한 파일로 실행 가능한 파일이다.

클래스 파일은 Java Compile을 통해서 .java 파일에서 .class 파일로 생성된다. 클래스 로더 구현체는 자바 클래스 파일 형식에 맞는 파일을 인식하고 로드합니다. 이 때 클래스 로더는 세가지의 엄격한 형식에 맞게 수행됩니다.

### Class Loader SubSystem
1. Loading : 이진 데이터 타입에 맞게 찾고 import 합니다.
  - 클래스 로더가 ".class" 파일을 읽고 상응하는 이진 데이터를 **메소드 영역**에 저장합니다.
2. Linking : Verification, Preparation, Resolution
  - Verification : 가져온 타입의 정확성을 보장합니다.
  - Preparation : 클래스 변수에 메모리를 할당하고 메모리를 기본값으로 할당합니다.
  - Resolution : 
3. Initialization : 클래스 변수를 적절한 시작 변수로 초기화하는 자바 코드를 호출합니다.

세가지 타입의 클래스 로더
- Bootstrap Class Loader : 모든 JVM 구현에는 클래스를 로드할 수 있는 부트스트랩 로더가 있어야 합니다. 이는 'JAVA_HOME/lib' 디렉토리에 있는 핵심 Java API 클래스를 로드합니다.
- 클래스 로더를 상속한 System Class Loader
- Application Class Loader

![Class Loaders](https://github.com/user-attachments/assets/45f91f1f-7468-4389-898b-9579058ab0a6)

## JVM Memory Area
![Runtime Data Area](https://github.com/user-attachments/assets/a2c8f17c-7eee-4c38-90c3-d287b84ac6b4)

### Method area

Method Area 는 클래스와 인터페이스 정의를 저장하는 JVM의 **공유 데이터 영역입니다.** 
JVM이 시작될 때 생성되고 JVM이 종료될 때만 파괴됩니다.

메소드 영역에는 정적 변수를 포함하여 클래스 이름, 부모 클래스 이름, 메소드 및 변수 정보 등 모든 
클래스 수준 정보가 저장됩니다. JVM 당 메서드 영역은 하나뿐이며 공유 리소스입니다.

JVM이 시작될 때 생성되는 공간으로 바이트 코드(.class)를 처음 메모리 공간에 올릴 때 초기화되는
대상을 저장하기 위한 메모리 공간입니다. JVM이 동작하고 클래스가 로드될 때 적재되서 **_프로그램이 종료될
때까지 저장_** 됩니다.

> 메소드 영역은 Class Area 나 Static Area 라고도 불리운다.

구체적으로, 클래스 로더는 클래스의 바이트코드를 로드하여 JVM에 전달합니다. 그런 다음 JVM은 클래스의
내부 표현을 생성하여 런타임에 객체를 생성하고 메서드를 호출하는 데 사용합니다.

Runtime Constant Pool : Method Area 영역에 포함되지만 독자적 중요성을 띈다


### 출처
- [IBM - JVM(Java Virtual Machine)](https://www.ibm.com/docs/ko/sdk-java-technology/8?topic=introduction-java-virtual-machine)

- [Wikipedia - Java virtual machine](https://en.wikipedia.org/wiki/Java_virtual_machine)

- [Wikipedia - JvmSpec7.png](https://en.wikipedia.org/wiki/File:JvmSpec7.png)

- [How Jvm Works - JVM  Architecture](https://www.geeksforgeeks.org/jvm-works-jvm-architecture/)

- [baeldung - JVM Runtime Data Area](https://www.baeldung.com/java-jvm-run-time-data-areas)

- [Inpa Dev Blog](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8#%EB%A9%94%EC%84%9C%EB%93%9C_%EC%98%81%EC%97%AD_method_area)