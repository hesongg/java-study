# Java 8, 9, 10 Study
- References) Modern Java in Action (by RAOUL-GABRIEL URMA, MARIO FUSCO, ALAN MYCROFT) 을 읽고 정리
- 참고한 책과 내용이 다를 수 있음

### 람다와 함수형 인터페이스

- 동작 파라미터화
  - 아직 어떻게 실행할지 정해지지 않은 코드 블록을 의미한다.
  - 이를 이용하여 변화되는 요구 사항에 효과적으로 대응 가능

- 익명 클래스
  - 이름이 없는 클래스
  - 익명 클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 할 수 있다.

- 동작 파라미터화와 익명 클래스를 사용한 예시
  ```java
  List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple){
      return RED.equals(apple.getColor());
    }
  });

  public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
      List<Apple> result = new ArrayList<>();
      for (Apple apple : inventory) {
        if (p.test(apple)){
          result.add(apple);
        }
      }
      return result;
    }
  ```
  
- 람다 표현식을 사용하면 위와 같은 코드를 간결하게 줄일 수 있음
  ```java
  List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));

  public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
      List<Apple> result = new ArrayList<>();
      for (Apple apple : inventory) {
        if (p.test(apple)){
          result.add(apple);
        }
      }
      return result;
    }
  ```

- 람다
  - 익명, 함수(특정 클래스에 종속되지 않음)
  - 전달(람다 표현식을 메소드 인수로 전달 가능)
  - 간결성
  - 람다 표현식 ```(Apple a1, Apple 2) -> a.getWeight()...``` 와 같이 파라미터, 화살표, 바디로 이루어짐 ```(parameters) -> expression```
  - 람다는 함수형 인터페이스 문맥에서 사용 가능
    - 전체 표현식을 함수형 인터페이스의 인터페이스로 취급 가능
  - 시그니처 란?
    - () -> {} 의 시그니처 ```() -> void```
    - Callable\<String\> ```() -> String```
    - () -> “test” ```() -> String```

- 함수형 인터페이스(Functional Interface) : 추상 메서드가 오직 하나인 인터페이스를 의미

- ```@FunctionalInterface``` 어노테이션 : 함수형 인터페이스를 가르킴, 함수형 인터페이스가 아니면 컴파일 에러남

- 람다 ex) 실행 어라운드 패턴에서 사용될 수 있다. ```try-with-resources``` 구문 참고

- 함수 디스크립터 : 함수형 인터페이스의 추상 메소드 시그니처

- 함수형 인터페이스 예시 (책의 105p)

T : 제네릭 형식
|인터페이스|추상메소드|
|-------|-------|
|Predicate\<T\>|boolean test(T t);|
|Consumer\<T\>|void accept(T t);|
|Function\<T,R\>|R apply(T t);|

- 자바의 형식
  - 기본형 (primitive type) : boolean, char, byte, short, int, long, float, double
    - 실제 연산에 사용되는 것은 모두 기본형 이다.
  - 참조형 (reference type) : Byte, Integer, Object, List ...
    - 기본형 8가지를 제외한 나머지 타입

- 기본형 -> 참조형 변환 기능 : 박싱(Boxing)
- 참조형 -> 기본형 변환 기능 : 언박싱(Unboxing)
- 박싱은 비용이 소모된다.

- 각 함수형 인터페이스마다 기본형 특화 인터페이스명이 따로 있다.
  - ex) ```Predicate<T>``` 의 함수형 디스크립터 ```T -> boolean```, 기본형 특화 인터페이스 ```IntPredicate```
 
- 람다 사용 시에
  - 타입 캐스팅으로 어떤 인터페이스를 호출하는지 명시 가능

  - 형식 추론 : 자바 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 파라미터 생략 가능
    ```java
    List<Apple> greenApples = filter(inventory, apple -> GREEN.equals(apple.getColor()));
 
    // 형식을 추론하고있지않음
    Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
    // type을 생략하여 형식을 추론
    Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
    ```


















