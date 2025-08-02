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

로드된 바이트 코드(.class) 들을 엮어서 JVM의 메모리 영역인 Runtime Data Areas에 배치합니다. 클래스를 메모리에 올리는
로딩 기능은 한번에 메모리에 올리지 않고, 어플리케이션에서 필요한 경우 동적으로 메모리에 적재하게 됩니다.

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

## 자바 가상 머신(JVM) 의 동작 방식
1. 자바 프로그램을 실행하면 JVM은 OS로부터 메모리를 할당받는다.
2. 자바 컴파일러(javac)가 자바 소스코드 (.java)를 자바 바이트 코드(.class)로 컴파일한다.
3. **Class Loader**는 동적 로딩을 통해 필요한 클래스들을 로딩 및 링크 하여 **Runtime Data Area**(실질적인 메모리를 할당 받아 관리하는 영역)에 올린다.
4. Runtime Data Area에 로딩 된 바이트 코드는 **Execution Engine**을 통해 해석된다.
5. 이 과정에서 Execution Engine에 의해 Garbage Collector의 작동과 Thread 동기화가 이루어진다.

![JVM](https://github.com/user-attachments/assets/cb5c8e4b-98bb-4d2f-8b11-28a0e3ed5e8d)

**구성요소**

- 클래스 로더 (Class Loader)
- 실행 엔진 (Execution Engine)
  - 인터프리터(Interpreter)
  - JIT 컴파일러(Just-in-Time)
  - 가비지 컬렉터(Garbage collector)
- 런타임 데이터 영역 (Runtime Data Area)
  - 메소드 영역
  - 힙 영역
  - PC Register
  - 스택 영역
  - 네이티브 메소드
- JNI - 네이티브 메소드 인터페이스
- 네이티브 메소드 라이브러리

## 실행 엔진 (Execution Engine)
클래스 로더를 통해 런타임 데이터 영역에 배치된 바이트 코드를 명령어 단위로 읽어서 실행한다.

자바 바이트 코드는 기계가 바로 수행할 수 있는 언어보다는 가상 머신이 이해할 수 있는 중간 레벨로
컴파일 된 코드이다. 그래서 JVM Execution Engine 은 JVM 내부에서 기계가 실행할 수 있는 형태로 변경
해준다.

이 수행 과정에서 실행 엔진은 인터프리터와 JIT 컴파일러 두 가지 방식을 혼합하여 바이트 코드를 실행한다.

### 인터프리터
바이트 코드 명령어를 하나씩 읽어서 해석하고 바로 실행한다. JVM 안에서 바이트코드는 기본적으로 인터프리터
방식으로 동작한다. 다만 같은 메소드라도 여러 번 호출된다면 매번 해석하고 수행해야 되서 전체적인 속도는 느리다.

### JIT 컴파일러 (Just-In-Time Compiler)
인터프리터의 단점을 보완하기 위해 도입된 방식으로 반복되는 코드를 발견하여 바이트 코드 전체를 컴파일하여
Native Code로 변경하고 해당 메소드를 더 이상 인터프리팅 하지 않고 캐싱해 두었다가 네이티브 코드로 직접
실행하는 방식이다.

하나씩 인터프리팅하여 실행하는 것이 아니라, 컴파일된 네이티브 코드를 실행하는 것이기 때문에
전체적인 속도는 인터프리팅 방식보다 빠르다.

하지만 바이트 코드를 Native Code로 변환하는 데에도 비용이 소요되므로, JVM은 모든 코드를 JIT 컴파일러
방식으로 실행하지 않고 인터프리터 방식을 사용하다 일정 기준이 넘어가면 JIT 컴파일 방식으로 명령어를
실행하는 식으로 진행한다.

Native Code?
JVM 환경에서 실행되는 코드 중 네이티브 환경에서 직접 실행되는 코드를 의미합니다. 즉, Java 코드가 아니라,
특정 운영 체제(OS)나 하드웨어에서 실행될 수 있는 기계어 코드 또는 플랫폼 종속적인 코드를 말합니다.
Java에서 부모가 되는 C 언어나, C++, 어셈블리어로 구성된 코드를 의미한다.

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