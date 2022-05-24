# Java 8, 9, 10 Study
- References) Modern Java in Action (by RAOUL-GABRIEL URMA, MARIO FUSCO, ALAN MYCROFT) 을 읽고 정리
- 참고한 책과 내용이 다를 수 있음
- 소스코드 참고 : http://www.hanbit.co.kr/src/10202

</br></br>

### 컬렉션 API 개선

</br>

#### 컬렉션 팩토리

- ```ArrayList``` 에서 ```List```를 ```Arrays.asList``` 함수를 사용해서 정의한 다음, 
  요소 추가나 삭제를 하려면 ```UnsupportedOperationException``` 이 발생한다.
-	내부적으로 고정된 크기의 변환할 수 있는 배열로 구현되었기 때문에 이와같은 일이 일어난다.

-	그럼 집합은 어떨까?
    ```java
    Set<String> friends = new HashSet<>(Arrays.asList(“Raphael”, “Oli”, “Thibaut”));
    ```
	- 또는 
    ```java
    Set<String> friends = Stream.of(“Ra”, “Teemo”, “Oli”).collect(Collectors.toSet());
    ```
    - 두 방법 모드 매끄럽지 못하며 내부적으로 불필요한 객체 할당을 필요로한다. 그리고 결과는 변환할 수 있는 집합이라는 사실도 주목하자.
    
-	자바 9 에서 작은 리스트, 집합, 맵을 쉽게 만들 수 있도록 팩토리 메소드를 제공한다.
    - ```List.of``` 팩토리 메소드를 이용하여 간단하게 리스트를 만들 수 있다.
        ```java
        List<String> friends = List.of(“Ra”, “Ti”, “Oli”);
        ```
        - 위 코드에 요소를 추가하려고해도 Exception이 발생한다. set() 으로 아이템을 바꾸려해도 비슷한 예외가 발생한다.

- 참고) 오버로딩 vs 가변인자
    - 가변 인자 버전은 추가 비용이 든다. 고정된 숫자의 요소를 API로 정의하여 오버로딩 하므로 이런 비용을 제거할 수 있다.

- ```Collectors.toList()``` 컬렉터로 스트림을 리스트로 변환 할 수 있다. 
  데이터 처리 형식을 설정하거나 데이터를 변환할 필요가 없다면 사용하기 간편한 팩토리 메소드(.of)를 이용할 것을 권장한다.
  

- 리스트 팩토리 : ```List.of()``` 로 간단하게 바꿀수 없는 리스트 생성 가능

- 집합 팩토리 : ```Set.of()```

- 맵 팩토리 
    - ```Map.of();```
        ```java
        Map<String, Integer> ageOfFriends = Map.of(“ra”, 30, “oli’, 25);
        ```
    
    - 값이 많은 맵에서는 ```Map.ofEntries``` 팩토리 메서드를 이용하는 것이 좋음
        ```java
        import static java.util.Map.entry;
        
        Map<String, Integer> aOf = Map.ofEntries(entry(“Ra”, 30),
                            entry(“Oli”, 25),
                            etnry(“Thi”. 26));
        ```
        - ```Map.entry``` 는 ```Map.Entry``` 객체를 만드는 새로운 팩토리 메소드다.
        
</br>

#### 리스트와 집합 처리

- 자바 8 에서는 ```List```, ```Set``` 인터페이스에 다음과 같은 메소드를 추가했다.
    - ```removeIf``` : 프레디케이트를 만족하는 요소를 제거
    - ```replaceAll``` : 리스트에서 이용할 수 있는 기능으로, ```UnaryOperator``` 함수를 이용해 요소를 바꾼다
    - ```sort``` : List 인터페이스에서 제공하는 기능으로 리스트를 정렬한다.

- ```removeIf``` 메소드
    - 삭제할 요소를 가르키는프레디케이트를 인수로 받는다.
    ```java
    transactions.removeIf(transaction -> Character.isDigit(transaction.getReferenceCode().charAt(0)));
    ```
    
- ```replaceAll``` 메소드
    - List 인터페이스의 ```replaceAll``` 메서드를 이용해 리스트의 각 요소를 새로운 요소로 바꿀 수 있다.
    ```java
    referenceCodes.replaceAll( code -> Character.toUpperCase(code.charAt(0)) + code.substring(1));
    ```

</br>

#### 맵 처리

- 자바 8 에서는 Map 인터페이스에 몇 가지 디폴트 메소드를 추가했다.

- ```forEach``` 메소드
    - 기존 Map 의 반복 작업 예시
        ```java
        for( Map.Entry<String, Integer> entry : aOf.entrySet() ) {
            String friend = entry.getKey();
            Integer age = entry.getValue();
        }
        ```
    
    - 자바 8 부터 Map 인터페이스는 ```BiConsumer``` 를 인수로 받는 ```forEach``` 메소드를 지원하므로 코드를 조금 더 간단하게 구현 할 수 있다
        ```java
        aOf.forEach( (friend, age) -> System.out.println(friend + “ is “ + age + “ years old”) );
        ```

- 정렬 메서드
    - 다음 두 개의 새로운 유틸리티를 이용하면 맵의 항목을 값 또는 키를 기준으로 정렬할 수 있다.
    - ```Entry.comparingByValue```
    - ```Etnry.comparingByKey```
    
        ```java
        Map<String, String> movies = Map.ofEntries(entry(“Ra”, “Star Wars”),
							entry(“Cri”, “Matrix”));
        movies.entrySet()
            .stream()
            .sorted(Entry.comparingByKey())
            .forEachOrdered(System.out::println); // 사람의 이름을 알파벳 순으로 스트림 요소 처리
        ```
	        - 다음과 같은 결과 출력 :
                Cri=Matrix
                Ra=Star Wars

- ```getOrDefault``` 메소드
    - 키로 찾으려는 결과가 null 인경우 기본 값을 반환한다. (해당 함수의 두번째 인수)
        ```java
        moives.getOrDefault(“Oli”, “matrix”); // "Oli" 라는 Key의 값이 null이면, "matrix" 값 반환
        ```

- 계산 패턴
    - ```computeIfAbsent``` : 제공된 키에 해당하는 값이 없으면, 키를 이용해 새 값을 계산하고 맵에 추가한다.
    - ```computeIfPresent``` : 제공된 키가 존재하면 새 값을 계산하고 맵에 추가한다.
    - ```compute``` : 제공된 키로 새 값을 계산하고 맵에 저장한다.
    - 정보를 캐시할때 ```computeIfAbsent```를 활용할 수 있다.

- 삭제 패턴
    - 제공된 키에 해당하는 맵 항목을 제거하는 remove 메소드는 이미 알고 있다. 
    - 자바 8에서는 키가 특정한 값과 연관되었을 때만 항목을 제거하는 오버로드 버전 메서드를 제공한다.
        ```java
        movies.remove(key, value);
        ```

- 교체 패턴
    - 맵의 항목을 바구는데 사용할 수 있는 두 개의 메소드가 맵에 추가 되었다.
    - ```replaceAll``` : ```BiFunction```을 적용한 결과로 각 항목의 값을 교체함
    - ```replace``` : 값이 존재하면 맵의 값을 바꾼다. 키가 특정 값으로 매핑되었을 때만 값을 교체하는 오버라이드 버전도 있다.
   
   - 다음과 같은 방법으로 맵의 모든 값의 형식을 바꿀 수 있다,
        ```java
        movies.replaceAll((friend, movie) -> movie.toUpperCase());
        ```

- 합침

    - 두 개의 맵을 합친다고 가정하자. 다음 처럼 ```putAll```을 사용 할 수 있다.
        - family, friends 의 맵을 정의..

        ```java
        Map<String, String> everyone = new HashMap<>(family);

        everyone.putAll(friends); // friends 의 모든 항목을 everyone으로 복사
        ```

    - 중복된 키가 없다면 위의 코드는 잘 동작한다.
    
    - 값을 좀더 유연하게 합쳐야 한다면 새로운 ```merge``` 메소드를 이용할 수 있다. 
      이 메소드는 중복된 키를 어떻게 합칠지 결정하는 ```BiFunction``` 을 인수로 받는다. 
        - family 와 friends 두 맵 모두에 “Cri”가 다른 영화 값으로 존재한다고 가정하자.
      
        - ```forEach``` 와 ```merge``` 메소드를 이용해 충돌을 해결할 수 있다.
            ```java
            Map<String, String> everyone = new HashMap<>(family);
            
            //중복된 키가 있으면 두 값을 연결
            friends.forEach((k, v) -> everyone.merge(k, v, (movie1, movie2) -> movie1 + “ & “ + movie2));  
            ```        
    
        - merge를 이용해 초기화 검사를 구현할 수 있다. 영화를 몇 회 시청했는지 기록하는 맵이 있다고 가정하자. 
            해당 값을 증가시키기 전에 관련 영화가 이미 맵에 존재하는지 확인해야 한다.
            ```java
            moviesToCount.merge(movieName, 1L, (key, count) -> count + 1L);
            ```
            - 키의 값이 null 이면 처음에는 1이 사용된다.

- 참고) map 의 특정 값 프레디케이트로 remove 하는 함수 : ```removeIf```
    ```java
	movies.entrySet().removeIf( entry -> entry.getValue < 10);
    ```

</br>


#### 개선 ConcurrentHashMap

- ```ConcurrentHashMap``` 클래스는 동시성 친화적이며 최신 기술을 반영한 HashMap 버전이다.
	- 내부 자료구조의 특정 부분만 잠궈 동시 추가, 갱신 작업을 허용한다. 
	- 따라서 동기화된 Hashtable 버전에 의해 읽기 쓰기 연산 성능이 월등하다. (참고로 표준 HashMap은 비동기로 동작함)

- 리듀스와 검색
	- ```ConcurrentHashMap``` 은 스트림에서 봤던 것과 비슷한 종류의 세 가지 새로운 연산을 지원한다.
	- ```forEach``` : 각 (키, 값) 쌍에 주어진 액션을 실행
	- ```reduce``` : 모든 쌍을 제공된 리듀스 함수를 이용해 결과로 합침
	- ```search``` : 널이 아닌 값을 반환할 때까지 각 쌍에 함수를 적용

	- 다음 처럼 키에 함수받기, 값, Map.Entry, (키, 값) 인수를 이용한 네 가지 연산 형태를 지원한다.
		- 키, 값으로 연산(forEach, reduce, search)
		- 키로 연산(forEachKey, reduceKeys, searchKeys)
		- 값으로 연산(forEachValue, reduceValues, searchValues)
		- Map.Entry 객체로 연산(forEachEntry, reduceEntries, searchEntries)

		- 이들 연산은 ```ConcurrentHashMap``` 의 상태를 잠그지 않고 연산을 수행한다는 점을 주목하자. 
			- 따라서 이들 연산에 제공한 함수는 계산이 진행되는 동안 바뀔 수 있는 객체, 값, 순서등에 의존하지 않아야한다.

		- 또한 이들 연산에 병렬성 기준 값(threshold)을 지정해야 한다. 
			- 맵의 크기가 주어진 기준 값 보다 작으면 순차적으로 연산을 실행한다. 
			- 기준 값을 1로 지정하면 공통 스레드 풀을 이용해 병렬성을 극대화한다. 
			- Long.MAX_VALUE 를 기준 값으로 설정하면 한 개의 스레드로 연산을 실행한다. 기준 값 규칙을 따르는 것이 좋다.

			- ```reduceValues``` 메소드를 이용해 맵의 최대 값을 찾는 예제
				```java
				ConcurrentHashMap<String, Long> map = new ConcurrentHashMap<>();

				long parallelismThreshold = 1;

				Optional<Integer> maxValue = Option.ofNullable(map.reduceValues(parallelismThreshold, Long::max));
				```
	
	- int, long, double 등의 기본 값에는 전용 each reduce 연산이 제공되므로 ```reduceValuesToInt```, ```reduceKeysToLong``` 등을 이용하면 
  	박싱 작업을 할 필요가 없고 효율적으로 작업을 처리 할 수 있다.


- 계수
	- ```ConcurrentHashMap``` 클래스는 맵의 매핑 개수를 반환하는 ```mappingCount``` 메소드를 제공한다. 
	- 기존의 size 메소드대신 새 코드에서는 int를 반환하는 ```mappingCount``` 메소드를 사용하는 것이 좋다. 
	  그래야 매핑의 개수가 int의 범위를 넘어서는 이후의 상황을 대처할 수 있기 때문이다.

- 집합뷰
	- ```ConcurrentHashMap``` 클래스는 ```ConcurrentHashMap``` 을 집합뷰로 반환하는 ```keySet```이라는 새메서드를 제공한다. 
		- 맵을 바꾸면 집합도 바뀌고 반대로 집합을 바꾸면 맵도 영향을 받는다. 
		- ```newKeySet``` 이라는 새 메서드를 이용해 ```ConcurrentHashMap``` 으로 유지되는 집합을 만들 수 도 있다.

</br>

#### 정리
- 자바 9는 적의 원소를 포함하여 바꿀 수 없는 리스트, 집합, 맵을 쉽게 만들 수 있도록 
  ```List.of```, ```Set.of```, ```Map.of```, ```Map.ofEntries``` 등의 컬렉션 팩토리를 지원한다.
	- 이들 컬렉션 팩토리가 반환한 객체는 만들어진 다음 바꿀 수 없다.

- List 인터페이스는 ```removeIf```, ```replaceAll```, ```sort``` 세가지 디폴트 메소드를 지원한다.

- Set 인터페이스는 ```removeIf``` 디폴트 메소드를 지원한다.

- Map 인터페이스는 자주 사용하는 패턴과 버지를 방지할수 있도록 다양한 디폴트 메소드를 지원한다.

- ConcurrentHashMap 은 Map 에서 상속받은 새 디폴트 메소드를 지원함과 동시에 스레드 안정성도 제공한다.
