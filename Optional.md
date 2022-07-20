# Optional 바르게 사용하기

- ```Optional``` 공식 API 문서의 ```API Note```
  ```
  API Note:
  Optional is primarily intended for use as a method return type where there is a clear need to represent "no result," 
  and where using null is likely to cause errors. A variable whose type is Optional should never itself be null; 
  it should always point to an Optional instance.
  ```
  
  - Optional은 주로 "결과 없음" 을 나타내거나 null 사용 시 오류가 발생할 가능성이 있는 경우 메서드 반환 타입으로 사용하기 위한 것이다.
  - Optional 을 타입으로 가지는 변수는 null 이 되어서는 안되며 항상 Optional 인스턴스를 가리키고 있어야 한다.

<br>

### Optional 을 사용하는 26가지 방법 -> 겹치는 내용이 많아 생략하고 중요한 내용만 작성한다.

<br>

#### 1. Optional 변수에는 null을 사용하지 않는다.

```java
// AVOID
Optional<Cart> emptyCart = null;

// PREFER
Optional<Cart> emptyCart = Optional.empty();
```
  
<br>  
  
#### 2. Optional.get()을 사용하기 전에 값이 들어있는지 확인한다.
  
  ```java
  // AVOID
  Optional<Cart> cart = ...; // 비어있는 cart
  
  //만약 cart가 비어있으면 NoSuchElementException이 발생
  Cart myCart = cart.get();
  
  // PREFER
  if(cart.isPresent()) Cart myCart = cart.get();
  ```
  
  - isPresent() 와 get() 을 사용하는거 자체가 좋은 방법이 아니다.. get() 쓰지말고 orElse() 나 orElseGet()을 쓰자
 
<br>
 
#### 3. 아무런 값이 없을 때는, Optional.Else() 메서드를 통해 미리 생성한 Object를 반환하자.
   
```java
// AVOID
public static final String USER_STATUS = "UNKNOWN";
...
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는 Optionial을 반환
    if (status.isPresent()) {
        return status.get();
    } else {
        return USER_STATUS;
    }
}

// PREFER
public static final String USER_STATUS = "UNKNOWN";
...
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는 Optional을 반환한다.
    return status.orElse(USER_STATUS);
}
```

- orElse() 메서드를 사용하는게 isPresent()-get()을 사용하는 것보다 훨씬 낫다.

- 하지만 orElse()의 경우, Optional이 값을 가지고 있더라도 orElse()가 가지고있는 파라미터를 확인한다.
  - 사용하지도 않을 값을 확인하는 것은 성능적으로 좋지 않음
  - orElse() 파라미터로 데이터를 가져오는 함수를 사용하고자한다면 4번을 참고하자

<br>

#### 4. 아무런 값이 없을 때는, Optional.orElseGet() 메서드를 통해 존재하지 않는 Default Object를 반환하자.

```java
// AVOID
public String computeStatus() {
    ... // status를 설정하는 코드
}
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는 Optional을 반환한다.
    // status가 비어있지 않아도  computeStatus()를 호출한다.
    return status.orElse(computeStatus()); 
}

// PREFER
public String computeStatus() {
    ... // status를 설정하는 코드
}
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는  Optional을 반환한다
    
    // status가 비어있는 경우에만 computeStatus()를 호출한다
    return status.orElseGet(this::computeStatus);
}
```

- orElseGet() 은 파라미터로 함수형 인터페이스(Supplier 인터페이스)를 받아서 수행한다.
- Supplier는 Optional에 값이 없을때만 실행된다.
- 따라서 Optional에 값이 없을 때만 새 객체를 생성하거나 새 연산을 수행하므로 불필요한 오버헤드가 없음

<br>

#### 5. 예외 발생 필요 시 (값이 없는 경우) orElseThrow()를 사용하자.

```java
// 자바 10 이상일 경우
// Optional에 값이 없을 때 그냥 단순히 NoSuchElementException만 발생시킨다.
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는 Optional을 반환한다.
    return status.orElseThrow();
}

// Java 8, 9
public String findUserStatus(long id) {
    Optional<String> status = ... ; // 비어있는 Optional을 반환한다.
    return status.orElseThrow(IllegalStateException::new);
    
    //이렇게도 쓸 수 있다.
    //return status.orElseThrow(() -> new NoSuchElementException());
}
```

<br>

#### 6. Optional이 비어있지 않고 들어 있는 값을 바로 사용하고자 한다면 ifPresent()를 사용

```java
// AVOID
Optional<String> status = ... ;
...
if (status.isPresent()) {
    System.out.println("Status: " + status.get());
}

// PREFER
Optional<String> status ... ;
...
status.ifPresent(System.out::println);
```

- Optional이 비어있다면 아무 작업도 하지 않고, 비어있지 않은 Option의 값을 바로 사용하고자하는 경우 사용한다.

<br>

#### 7. Java 9부터 - Optional 값이 있는지 없는지에 따라 다른 코드를 실행하고자 한다면 ifPresentOrElse()를 사용한다.

```java
// AVOID
Optional<String> status = ... ;
if(status.isPresent()) {
    System.out.println("Status: " + status.get());
} else {
    System.out.println("Status not found");
}

// PREFER
Optional<String> status = ... ;
status.ifPresentOrElse(
    System.out::println, 
    () -> System.out.println("Status not found")
);
```

- 자바 9부터 지원된다.

<br>

#### 8. Optional 타입의 필드를 선언하지 말자.

```java
// AVOID
public class Member {

    private Long id;
    private String name;
    private Optional<String> email = Optional.empty();
}

// PREFER
public class Member {

    private Long id;
    private String name;
    private String email;
}
```
 
- Optional은 필드에 사용할 목적으로 만들어지지 않았으며 Serializable 을 구현하지 않는다.
- 따라서 필드로 사용하지말자.

<br>

#### 9. Optional 을 생성자나 메서드 인자로 사용하지 말자.

- Optional은 대상 객체를 한 번 감싸는 방식으로 생성된다. 
  만약 생성자에서 인자로 Optional을 받게 된다면 매번 Optional을 생성해 인자로 전달을 해줘야 하는 상황이 생긴다.
  
- 마찬가지로 Setter에서도 Optional을 사용하지말자.

- Getter에서 무조건적으로 Optional을 반환하는 것 역시 좋은 방법은 아니다.

<br>

#### 10. Optional을 비어있는 Collections나 배열을 반환하는 용도로 사용하지 않는다.

```java
// AVOID
List<Member> members = team.getMembers();
return Optional.ofNullable(members);

// PREFER
List<Member> members = team.getMembers();
return members != null ? members : Collections.emptyList();
```

- Optional은 비싸다. 그리고 컬렉션은 null이 아니라 비어있는 컬렉션을 반환하는 것이 좋을 때가 많다. 
- 따라서 컬렉션은 Optional로 감싸서 반환하지 말고 비어있는 컬렉션을 반환하자.

- 마찬가지 이유로 Spring Data JPA Repository 메서드 선언 시 다음과 같이 컬렉션을 Optional로 감싸서 반환하는 것은 좋지 않다. 
  컬렉션을 반환하는 Spring Data JPA Repository 메서드는 null을 반환하지 않고 비어있는 컬렉션을 반환해준다.
  - 따라서 Optional로 감싸서 반환할 필요가 없다.
  ```java
  // AVOID
  public interface MemberRepository<Member, Long> extends JpaRepository {
      Optional<List<Member>> findAllByNameContaining(String part);
  }

  // PREFER
  public interface MemberRepository<Member, Long> extends JpaRepository {
      List<Member> findAllByNameContaining(String part);  // null이 반환되지 않으므로 Optional 불필요
  }
  ```

<br>

#### 11. Optional을 컬렉션의 원소로 사용 금지

```java
// AVOID
Map<String, Optional<String>> sports = new HashMap<>();
sports.put("100", Optional.of("BasketBall"));
sports.put("101", Optional.ofNullable(someOtherSports));
String basketBall = sports.get("100").orElse("BasketBall");
String unknown = sports.get("101").orElse("");

// PREFER
Map<String, String> sports = new HashMap<>();
sports.put("100", "BasketBall");
sports.put("101", null);
String basketBall = sports.getOrDefault("100", "BasketBall");
String unknown = sports.computeIfAbsent("101", k -> "");
```
- 컬렉션에는 많은 원소가 들어갈 수 있다. 
- 따라서 비싼 Optional을 원소로 사용하지 말고 원소를 꺼낼 때나 사용할 때 null 체크하는 것이 좋다. 
- 특히 Map은 getOrDefault(), putIfAbsent(), computeIfAbsent(), computeIfPresent() 처럼 null 체크가 포함된 메서드를 제공하므로, 
  Map의 원소로 Optional을 사용하지 말고 Map이 제공하는 메서드를 활용하는 것이 좋다.

- 그리고 Optional은 객체이다. 이 역시도 메모리를 잡아먹고 GC의 대상이다. 성능적으로도 절대 추천하지 못할 방법이다.

<br>

#### 12. of(), ofNullable() 혼동 주의

```java
// AVOID
return Optional.of(member.getEmail());  // member의 email이 null이면 NPE 발생

// PREFER
return Optional.ofNullable(member.getEmail());


// AVOID
return Optional.ofNullable("READY");

// PREFER
return Optional.of("READY");
```

- of(data)은 data가 null이 아님이 확실할 때만 사용해야 하며, data가 null이면 NullPointerException 이 발생한다.

- ofNullable(data)은 data가 null일 수도 있을 때만 사용해야 하며, data가 null이 아님이 확실하면 of(data)를 사용해야 한다.

<br>

#### 13. Optional\<T\> 대신 OptionalInt, OptionalLong, OptionalDouble

```java
// AVOID
Optional<Integer> price = Optional.of(50);
Optional<Long> price = Optional.of(50L);
Optional<Double> price = Optional.of(50.43d);

// PREFER
OptionalInt price = OptionalInt.of(50);           // unwrap via getAsInt()
OptionalLong price = OptionalLong.of(50L);        // unwrap via getAsLong()
OptionalDouble price = OptionalDouble.of(50.43d); // unwrap via getAsDouble()
```

```java
// AVOID
Optional<Integer> count = Optional.of(38);  // boxing 발생
for (int i = 0 ; i < count.get() ; i++) { ... }  // unboxing 발생

// PREFER
OptionalInt count = OptionalInt.of(38);  // boxing 발생 안 함
for (int i = 0 ; i < count.getAsInt() ; i++) { ... }  // unboxing 발생 안 함  
```

- Optional에 담길 값이 int, long, double이라면 Boxing/Unboxing이 발생하는 Optional<Integer>, Optional<Long>, Optional<Double>을 사용하지 말고, 
  OptionalInt, OptionalLong, OptionalDouble을 사용하자.
  
- 무조건 Optioanl<T>를 사용하지 말라는 것은 아님.

- 객체를 감싸고 다시 푸는것은 비용이 들어간다. 
  - 그러므로 int, long, double같은 기본타입의 값을 사용할 때는 OptionalInt, OptionalLong , OptionalDouble 등을 사용하자.
  
<br>
  
#### 14. assertEquals를 사용할 때 Optional을 그대로 사용

```java
// AVOID
Optional<String> actualItem = Optional.of("Shoes");
Optional<String> expectedItem = Optional.of("Shoes");        
assertEquals(expectedItem.get(), actualItem.get());

// PREFER
Optional<String> actualItem = Optional.of("Shoes");
Optional<String> expectedItem = Optional.of("Shoes");        
assertEquals(expectedItem, actualItem);  
```
  
- Optional값을 비교할 때 굳이 get등을 사용해 값을 가져올 필요가 없다.
- Optional equals() 메서드는 Optional을 비교하는것이 아닌 Optional로 감싸진 값을 비교하기 때문이다.

<br>
  
#### 15. 값을 처리할 때 Optional 이 지원하는 메서드을 활용하자.

- Map(). flatMap(), filter() 등

<br>
  
#### 16. 자바 11부터 Optional.isEmpty() 사용 가능

- Optional 값이 비어있는지의 유무를 boolean으로 반환한다.
  

<br>

- 내용 참고) 
  - https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/
  - https://dzone.com/articles/using-optional-correctly-is-not-optional
  - https://docs.oracle.com/javase/9/docs/api/java/util/Optional.html
  - https://irerin07.tistory.com/108
