<details>
<summary>JVM</summary>
<div markdown="1">

* 바이트 코드로 변환된 자바 프로그램을 실행하는 가상 머신
* JVM 자체는 지키도록 권장되는 specification, 실제로는 구현하기 나름
* JDK(Java Development Kit), JRE(Java Runtime Environment)와 한 세트, 별도로 설치하거나 그러지 않음
    * JDK: 개발을 위한 tool, 개발자가 사용
    * JRE: 자바 프로그램 실행을 위한 환경 제공
* 자바가 어느 환경에서든 실행될 수 있게 하는 핵심 기술(WORA - write once run anywhere)
    * C++의 경우, 실행되는 환경에 따라 컴파일되기 때문에 윈도우에서 컴파일한 결과물은 Linux에서 실행되지 않을 수 있음
    * 자바의 경우, 컴파일 시 바로 CPU가 실행할 수 있는 기계어로 변환되지 않고 우선적으로 JVM에 의해 바이트코드로 변환됨
    * JVM은 바이트코드를 CPU가 실행할 수 있는 기계어로 변환함
    * 이 과정을 통해 자바코드(바이트코드)는 JVM이 설치되어 있는 모든 환경에서 실행될 수 있음 - platform independent
    * 자바는 platform independent 하지만 JVM은 당연히 운영체제에 따라 여러가지 버전이 존재해야 함
* JVM은 메모리에서 돌아가고, 하나의 자바 프로세스 당 하나의 JVM이 존재
* JVM은 non-daemon thread가 모두 종료되면 메모리에서 사라짐
    * JVM daemon thread는 백그라운드에서 돌아가는 우선 순위가 낮은 스레드로, 사용자의 애플리케이션을 보조하는 역할을 수행
    * 대표적으로 GC
    * 일반 스레드가 모두 종료되면 JVM이 할당 해제되는 것과 함께 daemon thread는 강제로 종료됨

</div>
</details>

<details>
<summary>JVM 구성</summary>
<div markdown="1">

* Class Loader
    * 3가지 작업 수행 - Loading, Linking, Initialization
        * Loading - 컴파일한 바이트 코드(.class)를 읽고 아래(binary) 데이터를 생성해 Runtime Data Area의 method area에 저장함
            * load한 클래스와 근접 부모 클래스의 FQCN(Fully Qualified Class Name - 패키지 경로를 포함한 클래스 이름)
            * load한 클래스가 클래스, 인터페이스, enum 중 어떤 것인지에 대한 정보
            * 제어자, 변수, 메소드에 대한 정보
            * 3가지 class loader
                * Boostrap class loader
                    * 모든 class loader의 부모
                    * jre/lib/rt.jar(runtime java archive)에서 JVM을 구동하는데 필수적인 JDK 클래스 파일을 로딩함
                    * 네이티브 언어로 구현됨
                * Extension class loader
                    * jre/lib/ext의 클래스 파일을 로딩(TODO: 자세히 알아보기)
                * Application(System) class loader
                    * application classpath의 클래스 파일을 로딩함
                    * 간단히 말해서 개발자가 작성한 자바 코드의 클래스 파일을 로딩함
                * 4가지 Principle
                    * Delegate Hierarchy Principle
                        * 클래스 A를 로딩할 때, Application class loader에서 시작해 상위 class loader로 위임(Class -> Extension -> Bootstrap)
                        * 최상위 class loader부터 클래스를 찾음 -> BootStrap에서 못 찾으면 Extension으로, 없으면 Class로, 최하위에서도 못 찾으면 ClassNotFoundException
                        * Visibility, Uniquenessprinciple을 만족시키기 위함
                    * Visibility Principle - 상위 class loader는 하위 class loader가 load한 클래스를 볼 수 없음, 반대는 가능
                    * Uniqueness Principle - 상위 class loader가 load한 클래스를 하위 class loader가 중복으로 load 하지 말아야 함
                    * No Unloading Principle - class loader는 load한 클래스를 unload할 수 없음, 대신 현재 class loader를 없애고 새롭게 생성은 가능
    * Linking
        * 3가지 단계
            * Verification
                * .class 파일의 유효성을 확인함
                    * 코드가 Java Specification대로 작성되었는지?
                    * JVM Specification에 맞는 컴파일러에 의해 생성되었는지?
                * class load process 중 제일 많은 시간 소요
            * Preparation
                * static 변수를 위한 메모리 공간을 할당하고 기본 값으로 초기화함
                    * '기본 값'으로 초기화 함 -> static int a = 5;일때 5가 아니라 int의 기본 값인 0으로 초기화
            * Resolution
                * Symbolic reference를 method area에 있는 실제 주소로 변경
                    * Symbolic reference - .class 파일에서 참조하고 있는 클래스의 이름만을 지칭하는 것, JVM에 올라가면 단순히 이름만 지칭하고 있는 참조에서 참조하고 있는 객체의 주소값으로 변경
    * Initialization
        * static 변수가 코드에서 정의한 값으로 초기화되고, static{} 블록 안의 코드가 실행됨

* Runtime Data Area
    * Method Area
        * Runtime Constant Pool
            * string constant, numeric constant, class reference...
            * 런타임에 생성되는 static 상수 저장소
        * 메소드 정보(이름, 리턴 타입 등) + 코드
        * 멤버(필드) 변수
        * static 변수
        * JVM 안에서 공유됨
    * Heap
        * 런타임에 new 키워드로 생성된 객체 및 배열 저장
        * GC의 대상
        * JVM 안에서 공유됨
    * Stack
        * 각 메소드 호출 시 하나의 stack frame 할당됨
        * 메소드 안에서 선언되는 로컬 변수 저장
        * 메소드가 값을 리턴하거나(끝나거나) 예외가 발생할 때 stack frame 제거됨
        * 스레드 당 하나의 stack 할당
    * PC Register
        * context switch 발생 시, 어디까지 실행됐는지 process count를 저장
        * 스레드 당 하나 할당
    * Native Method Stack
        * native method 호출 시 사용되는 스택
        * native method
            * JVM이 동작하는 아키텍쳐에서 사용되는 언어로 작성된 메소드, 주로 C, C++

* Execution Engine
    * Interpreter
        * 바이트코드를 한 줄씩 읽고 기계어로 변환
        * 바이트코드 한 줄을 읽고 실행하는 건 빠르지만 전체를 읽고 실행하는 건 느림
        * 한 메소드가 여러번 호출될 경우, 매번 다시 읽고 실행하는 과정을 거침 -> JIT 컴파일러를 이용해 단점 보완
    * JIT Compiler
        * 특정 메소드가 반복적으로 호출되는 경우, 바이트코드를 컴파일해 native code로 변환함
        * 이후 반복적 호출에 native code를 실행시킴 -> interpreter가 바이트코드를 한 줄씩 읽는 것 보다 좋은 성능
        * 하지만 JIT Compiler가 바이트코드를 컴파일 하는 것보다 interpreter가 읽고 실행하는 경우가 빠른 경우가 있음 + native code는 cache에 저장됨(고비용)
        * 이런 경우, JIT Compiler는 메소드 호출 빈도를 확인하고, 일정 횟수 이상인 경우, 위 방법 사용 -> adaptive compiling
        * 4가지 구성
            * Intermediate Code Generator - intermediate code(바이트코드와 native code 사이?) 생성
            * Code Optimizer - 위에서 생성한 intermediate code optimize
            * Target Code Generator - intermediate code에서 native code 생성
            * Profiler - hotspot을 찾는 역할 수행(ex. 메소드가 여러번 호출되는 인스턴스 찾기)
    * GC
        * heap 영역에서 더 이상 사용하지 않는 객체를 메모리에서 할당 해제하는 역할 수행
        * mark and sweep 방식
            * stack 등에서 heap 영역에 생성된 객체의 레퍼런스 값을 가지고 있는데, 이를 이용해 레퍼런스 값이 가르키고 있는 객체를 마킹함
            * 마킹되지 않은 객체(unreachable)를 모두 heap 영역에서 제거
        * minor gc, major gc
            * minor gc
                * heap 공간은 eden, survivor0, survivor1, old generation으로 나뉨
                * 객체가 처음 생성될 때 age bit가 0으로 초기화되어 eden에 할당됨
                * eden 공간이 채워지면 minor gc가 동작하고, 살아남은 객체는 age bit가 1 더해져 survivor0으로 옮겨짐
                * 다시 eden 공간이 채워지면 minor gc가 동작, 살아남은 객체는 age bit가 1 더해져 survivor1로 옮겨짐 -> 0에서 1, 1에서 0, 즉 survivor 영역 중 하나는 반드시 비워진 상태
                * age bit가 특정 정도보다 커지면 해당 객체를 old generation으로 이동, old generation은 minor gc의 대상이 아님, 즉 상시 사용되는 객체를 저장함
            * major gc
                * old generation 영역이 채워지면 major gc 동작
                * old generation의 모든 객체를 대상으로 실행됨
                * 매우 비용이 큰 동작, 동작 시 모든 스레드가 정지함 -> stop the world
                * JVM을 튜닝할 때, major gc의 동작 빈도를 줄이고 minor gc가 자주 동작하도록 하는 경우가 많음
        * System.gc()로 직접 gc를 호출할 수 있지만 금기시 됨 -> 호출 시점에서 프로세스의 상태를 알 수 없음, 어떤 스레드에서 어떤 동작을 하는지는 매 순간 다르므로 직접 호출하는 건 위험함
</div>
</details>

<details>
<summary>자료구조</summary>
<div markdown="1">

* **Hash**
  *  HashTable & HashMap
  *  자주 사용되는 건 HashMap
  *  **HashMap**
     *  동기화를 지원하지 않음 -> 병렬 실행시 문제 발생 -> ConcurrentHashMap 사용
     *  보조 해시 함수를 사용해 성능 상의 이점이 있음 -> 충돌 빈도 ↓
     *  내부적으로 배열로 구성되어 있음 -> hash bucket
     *  key 값을 ```hashcode()```라는 메소드를 통해 int형의 hash 값으로 변환 후 bucket의 크기로 modulo 연산을 한 값을 인덱스로 사용함 -> ```bucket[hashcode() % M]```
     *  이때 key 값이 달라도 1/M의 확률로 같은 인덱스 값이 나올 수 있음 -> hash collision
     *  M을 키우면 확률은 낮아지지만 2^32를 벗어날 수는 없음(int 범위)
   
  *  **Hash Collision**
     *  크게 2가지 해결 방법
     *  Open Addressing - 다른 비어있는 버킷을 찾아 삽입
        *  Linear Probing - 순차적으로 비어있는 버킷 탐색
        *  Quadratic Probing - 2차함수(?)를 이용해 버킷 탐색
     *  Seperate Chaining - 중복되는 버킷을 기준으로 LinkedList or Red-Black Tree 사용(Java 8)
        *  각 버킷을 head로 지정하고 중복 발생시 add 하는 방법(자료구조만 다름)
     *  Java는 Seperate Chaining 사용
        * Open Addressing은 연속된 공간(버킷 배열?)에 저장되므로 캐시 hit ratio가 좋음
        * 하지만 이 장점도 크기가 커질수록 사라짐 -> 배열이 커질수록 hit ratio ↓
        * remove가 빈번하게 호출되는 상황에서 Seperate Chaining가 유리(삭제에 용이한 자료구조)
        * Java 8부터는 요소의 개수에 따라 LinkedList와 Tree 구조를 오감
      
  *  **버킷 동적 확장**
     *  버킷 개수가 적은 경우, 빈번한 충돌로 인해 성능 이슈가 발생할 수 있음
     *  따라서 HashMap은 일정 개수에 도달하면 버킷의 크기를 2배로 늘림(기본값 16)
     *  하지만 확장마다 모든 데이터를 읽어 Seperate Chaining을 다시 구성해야함
     *  따라서 저장될 데이터의 개수를 미리 예측할 수 있다면 생성자의 인자로 미리 버킷 개수를 지정해 불필요한 Seperate Chaining 재구성을 방지할 수 있음
     *   버킷 개수를 2배씩 확장시킬 때, X % M와 같은 계산식을 사용함(X == hashCode(), M -> 2 ^ a)
     *   이때 X가 아무리 32bit에 넓게 분배되었다고 해도 계산에는 하위 a bit만 사용되게 되어 충돌이 빈번하게 일어날 수 있음
     *   이를 위해 **보조 해시 함수**를 사용함
        *  보조 해시 함수는 키의 해시값(key.hashCode(), 즉 X % M의 X)을 변형함  
</div>
</details>
