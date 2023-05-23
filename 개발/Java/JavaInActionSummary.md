<details>
<summary>chapter 1 ~ 3</summary>
<div markdown="1">

* **Java 8에서의 변화**
  * 함수형 프로그래밍 도입
  * 멀티코어 하드웨어 등장에 따른 병렬성 지원
  * 더욱 간결한 코드
  * **스트림 처리**
    * 파이프라인을 이용해 병렬적으로 데이터를 처리할 수 있음
    * 스레드라는 복잡한 작업을 사용하지 않으면서 공짜로 병렬성을 얻을 수 있음
    * 내부 반복으로 가독성과 간소화 지원
  * **코드 전달 기법**
    * 동작 파라미터화
    * 람다와 메소드를 일급 객체로 취급함
  * **인터페이스의 default 메소드**
    * 최근들어 외부에서 만들어진 컴포넌트를 이용해 시스템을 구축하는 경향이 있음
    * 예를 들어, 오픈소스를 끌어다 입맛에 맞게 수정해 사용하는 경우
    * 하지만 이런 경우, 상위 인터페이스에 메소드를 추가하기 위해서는 그 인터페이스를 구현하는 모든 클래스에 메소드를 오버라이딩 해야 함
    * default 메소드를 도입함으로써 이런 현상을 방지할 수 있음
    * 하지만 "다이아몬드 상속" 이슈가 발생할 수 있음
* **동작 파라미터화**
  * 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미함
  * 잦은 변화에 유연한 대처가 가능함
  * 요약하자면 인터페이스를 파라미터로 넘기는 것 - 3가지 방법
    * 파라미터에 익명 클래스를 이용 - 코드의 불필요한 장황함이 발생
    * **람다 함수 이용**
    * **메소드 참조 이용**
* **람다**
  * 함수형 인터페이스가 파라미터인 경우, 람다 함수로 대체 가능
  `Predicate<Integer> predicate = (Integer i) -> i % 2 == 0;`
  * 함수형 인터페이스는 default로 구현되지 않은 단 하나의 추상 메서드를 가지는 인터페이스
  * 활용 예제 - 초기화 코드와 마무리 코드가 작업 코드를 감싸고 있는 **실행 어라운드 패턴**에 람다 적용
    ```
      public String processFile() throws IOException {
       try (BufferedReader br = 
              new BufferedReader(new FileReader("data.txt"))) {
                 return br.readLine();
              }
       }
    ```
    * 이 경우, 파일에서 단 한 줄만 읽을 수 있고, 두 줄을 읽어야 한다면 함수 자체를 수정해야 함
    * 다음과 같이 람다를 이용해 문제를 해결할 수 있음
    ```
       public interface BufferedReaderProcessor {
           String process(BufferedReader br) throws IOException;
       }
       
       public String processFile(BufferedReaderProcessor p) throws IOException {
           try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
                return p.process(br);
           }
       }
       
       String oneLine = processFile((BufferedReader br) -> br.readLine());
       String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
    ```
    * 이런 식으로 람다를 사용해 유지보수에 용이한 코드를 작성할 수 있음
    * 이 때 람다는 예외를 던지는 것을 허용하지 않음 -> 예외를 던지게 하려면 예외를 던지는 함수형 인터페이스를 정의하거나 try/catch 블럭으로 감싸야 함
  * 람다의 형식 검사는 파라미터와 리턴 타입으로 이루어짐 -> 함수형 인터페이스의 추상 메소드와 일치해야 함
    * 하지만 다음 예와 같이 함수 디스크립터로 구분할 수 없는 경우가 있을 수 있다
    ```
    public void execute(Runnable runnable) { runnable.run(); }
    public void execute(Action<T> action) { action.act(); }
    
    @FunctionalInterfacee interface Action { void act(); }
    ```
    * 이 경우, `execute((Action) ()->System.out.println("Action"));`과 같은 방법으로 어떤 메소드를 호출할 지 명확히 할 수 있다
    * 함수 디스크립터에서 리턴 타입이 void인 경우, 람다 바디에 일반 표현식이 있다면 리턴 타입과 무관하게 호환된다
      * `Consumer<String> consumer = (String str) -> list.add(str);`에서 list.add는 boolean을 리턴하지만 람다 바디에 일반 표현식이 있으므로 리턴 타입이 void인 Consumer과 호환된다
  * 람다에서 파라미터로 넘겨진 변수 외에도 지역 변수도 사용 가능, 다만 해당 변수가 명시적으로 final이거나 final 처럼 사용되어야 한다 -> read만 가능
    * **why? - todo**
* **메소드 참조**
  * 특정 메소드만을 호출하는 람다의 축약형
  * 람다를 대체해 함수형 인터페이스에 대입 가능
  * **how? - 아직 헷갈림, todo**
</div>
</details>

<details>
<summary>chapter 4 ~ 6</summary>
<div markdown="1">

* **Stream**
  * Java 8에 추가된 기능
  * 선언형으로 컬렉션을 처리할 수 있음
    * Java 8 이전에는 명령형으로 컬렉션을 처리해야 했음 
    * 뭔가를 하려면 처음부터 끝까지 모든 작업을 일일히 선언해야 했음
    ```
    List<Dish> lowCaloricDishes = new ArrayList<>();
    for(Dish dish : dishes) {
      if(dish.getCalorie() < 400) {
        lowCaloricDishes.adD(dish);
      }
    }
    ```
    * Stream을 이용하면 명령형과 달리 how가 아니라 what에 집중할 수 있음
    ```
    List<Dish> lowCaloricDishes = dishes.stream()
                                        .filter(dish -> dish.getCalorie() < 400)
                                        .collect(toList());
    ```
    * 이로 인한 소프트웨어공학적 이득을 취할 수 있음
  * 멀티스레드 코드를 직접 구현하지 않고 병렬적으로 처리할 수 있음
  * 2가지 중요 특성
    * 파이프라이닝
      * 스트림 연산은 스트림 연산끼리 연결해 파이프라인을 구성할 수 있도록 스트림 자신을 반환함
      * 빌더 패턴과 유사한 형식으로 중간 연산을 이용해 데이터를 가공하고 최종 연산으로 결과물을 반환하는 형식
      * 이로 인해 laziness, short-circuiting과 같은 최적화도 얻을 수 있음
      * laziness: 이론상 요구할 때만 값을 계산함, 모든 값을 메모리에 올리는 컬렉션과의 차이
      * short-circuit: 모든 stream을 처리하지 않아도 결과를 반환하는 것, allMatch, findFirst 등의 중간 연산으로 가능 
    * 내부 반복
      * 명시적으로 반복자를 사용하는 컬렉션과 달리 알아서 반복을 처리하고 스트림 값을 저장해주는 내부 반복을 사용함
      * 즉, 추상화
      ```
      for(Integer i : Integers) { ... } // 외부 반복
      Integers.stream().map(i -> i + 2).collect(toList()) // 내부 반복
      ```
  * stream의 parallelStream을 이용하는 것으로 간단히 기존 stream에 병렬성을 더할 수 있음
    * 이 때, 각 연산의 내부적인 상태를 고려해야 함
    * map, filter와 같은 연산의 경우, 입력 스트림에서 각 요소를 받아 0 또는 결과를 출력 스트림으로 보냄
    * 일반적으로 이런 연산은 내부적인 상태를 가지지 않는 stateless 연산임
    * 반면 reduce, sum, max와 같은 연산은 중간 연산의 값을 기록할 내부 상태가 필요함
    * 외에도 sorted, distinct와 같은 연산은 작업을 위해 모든 요소를 기록해야 하고, 요소가 버퍼에 추가돼 있어야 함
    * 이런 연산은 stateful 연산



</div>
</details>
