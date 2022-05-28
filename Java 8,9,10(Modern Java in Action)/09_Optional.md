# Java 8, 9, 10 Study
- References) Modern Java in Action (by RAOUL-GABRIEL URMA, MARIO FUSCO, ALAN MYCROFT) 을 읽고 정리
- 참고한 책과 내용이 다를 수 있음
- 소스코드 참고 : http://www.hanbit.co.kr/src/10202

</br></br>

# Optional 클래스

</br>

### null 때문에 발생하는 문제

- 에러의 근원이다 : NullPointerException은 자바에서 가장 흔히 발생하는 에러이다.

- 코드를 어지럽힌다 : 때로는 중첩된 null 확인 코드를 추가해야 하므로 null 때문에 코드 가독성이 떨어진다.

- 아무 의미가 없다 : null은 아무 의미도 표현하지 않는다. 특히 정적 형식 언어에서 값이 없음을 표현하는 방법으로는 적절하지 않다.

- 자바 철학에 위배된다 : 자바는 개발자로부터 모든 포인터를 숨겼다. 하지만 예외가 있는데 그것이 바로 null 포인터다.

- 형식 시스템에 구멍을 만든다 : null은 무형식이며 정보를 포함하고 있지않으므로 모든 참조 형식에 nul을 할당할 수 있다.
	이런 식으로 null이 할당되기 시작하면서 시스템의 다른 부분으로 null이 퍼졌을 때 애초에 null이 어떤 의미로 사용되었는지 알 수 없다.

</br>

### Optional 클래스 소개

- 자바 8은 하스켈과 스칼라의 영향을 받아서 ```java.util.Optional<T>``` 라는 새로운 클래스를 제공한다.

- Optional은 선택형값을 캡슐화하는 클래스이다.
	- 예를 들어 어떤 사람이 차를 소유하고 있지 않다면 Person 클래스의 car 변수는 null을 가져야한다.
	- 하지만 Optional을 이용하면 null을 할당하는 것이 아니라 변수형을 Optional<Car>로 설정할 수 있다.
	
- 값이 있으면 Optional 클래스는 값을 감싼다. 반면 값이 없으면 ```Optional.empty``` 메서드로 Optional을 반환한다.

- ```Optional.empty```는 Optional의 특별한 싱글턴 인스턴스를 반환하는 정적 팩토리 메소드이다.

- null을 참조하려하면 NullPointerException이 발생하지만 ```Optional.empty()``` 는 Optional 객체이므로 다양한 방식으로 활용 가능

- Optional 사용한 데이터 모델 Person/Car/Insurance
	```java
	public class Person {

	  private Optional<Car> car;

	  public Optional<Car> getCar() {
		return car;
	  }
	}
	
	public class Car {

	  private Optional<Insurance> insurance;

	  public Optional<Insurance> getInsurance() {
		return insurance;
	  }

	}
	
	public class Insurance {

	  private String name;

	  public String getName() {
		return name;
	  }

	}
	```
	
	- Optional 클래스를 사용하면서 모델의 의미가 더 명확해졌음을 확인 가능
		- 사람은 Optional<Car> 를 참조하며 자동차는 Optional<Insurance>를 참조
			- 이는 사람이 자동차를 소유했을수도, 아닐수도 있으며 / 자동차는 보험에 가입되어있을수도, 아닐수도 있음을 명확하게 설명한다.
		- 또한 보험회사 이름은 Optional<String> 이 아니라 String으로 선언되어있는데, 이는 보험회사는 반드시 이름을 가져야 함을 보여준다.
			- 따라서 보험회사 이름을 참조할 때 NullPointerException이 발생할 수도 있다는 정보를 확인할 수 있다.
			- 하지만 보험회사 이름이 null인지 확인하는 코드를 추가할 필요는 없고 보험회사 이름이 없는 이유가 무엇인지 밝혀서 문제를 해결해야 한다.
			
- Optional을 이용하면 값이 없는 상황이 우리 데이터에 문제가 있는 것인지 아니면 알고리즘의 버그인지 명확하게 구분할 수 있다.

- 모든 null 참조를 Optional로 바꾸는것은 바람직하지 않다. 

- Optional의 역할은 더 이해하기 쉬운 API를 설계하도록 돕는 것이다.

- 메서드의 시그니처만 보고도 선택형 값인지 여부를 구별할 수 있다.

- Optional이 등장하면 이를 언랩해서 값이 없을 수 있는 상황에 적절하게 대응하도록 강제하는 효과가 있다.

</br>

### Optional 적용 패턴

</br>

#### Optional 객체 만들기

- 빈 Optional
	- 정적 팩토리 메서드 ```Optional.empty``` 로 빈 Optional 객체를 얻을 수 있다.
		```java
		Optional<Car> optCar = Optional.empty
		```
		
- null이 아닌 값으로 Optional 만들기
	- 정적 팩토리 메서드 ```Optional.of``` 로 null 이 아닌 값을 포함하는 Optional을 만들 수 있다.
		```java
		Optional<Car> optCar = Optional.of(car);
		```
		- 이제 car가 null이라면 즉시 NullpointerException이 발생한다.
			- Optional을 사용하지 않았다면 car의 프로퍼티에 접근하려 할 때 에러가 발생했을 것이다.
			
- null 값으로 Optional 만들기
	- 정적 팩토리 메서드 ```Optional.ofNullable```로 null 값을 저장할 수 있는 Optional을 만들 수 있다.
		```java
		Optional<Car> optCar = Optional.ofNuallable(car);
		```
		- car 가 null이면 빈 Optional 객체가 반환된다.
		
#### 맵으로 Optional의 값을 추출하고 변환하기

- 객체의 정보를 추출할 때 예를 들어 보험회사의 이름을 추출한다고 가정하자.
	- 다음 코드처럼 이름 정보에 접근하기 전에 insurance가 null인지 확인해야 한다.
		```java
		String name = null;
		
		if(insurance != null){
			name = insurance.getName();
		}
		```
	
	- 이런 유형의 패턴에 사용할 수 있도록 Optional은 map 메소드를 지원한다.
		```java
		Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
		
		Optional<String> name = optInsurance.map(Insurance::getName);
		```
	
- Optional의 map 메서드는 스트림의 map 메서드와 개념적으로 비슷하다.

#### flatMap으로 Optional 객체 연결

- 다음과 같은 코드가 있다고 가정
	```java
	public String getCarInsuranceName(Person person){
		return person.getCar().getInsurance.getName();
	}
	```
	
	- 다음 처럼 map 을 이용하여 코드를 재구현할 수 있다.
		```java
		Optional<Person> optPerson = Optional.of(person);
		
		Optional<String> name = 
			optPerson.map(Person::getCar)
					.map(Car::getInsurance)
					.map(Insurance::getName)
		```
	
		- 위 코드는 컴파일 되지않는다.
			- optPeople의 형식은 ```Optional<People>``` 이므로 map 메소드를 호출할 수 있다.
			- 하지만 getCar는 ```Optional<Car>``` 형식의 객체를 반환한다.
			- 즉 map 연산의 결과는 ```Optional<Optional<Car>>``` 형식의 객체다.
			- 이런 중첩 구조로 인해 getInsurance 메소드를 지원하지 않는다.
			
	- ```flatMap``` 으로 수정
		```java
		public String getCarInsuranceName(Optional<Person> person){
			return person.flatMap(Person::getCar)
						.flatMap(Car::getInsurance)
						.map(Insurance::getName)
						.orEsle("Unknown"); // 결과 Optional이 비어있으면 기본 값 사용
		}
		```

- 참고) Optional 클래스는 필드 형식으로 사용할 것을 가정하지 않으므로 Serializable 인터페이스를 구현하지 않는다.
	- 따라서 도메인 모델에 Optional을 사용한다면 직렬화 모델을 사용하는 도구나 프레임워크에서 문제가 생길 수 있다.
	- 직렬화 모델이 필요하다면 다음과 같이 Optional로 값을 반환받을 수 있는 메소드를 추가하는 방식을 권장
		```java
		public class Person {
			private Car car;
			public Optional<Car> getCarAsOptional() {
				return Optional.ofNullable(car);
			}
		}
		```

#### Optional 스트림 조작

- 자바 9에서는 Optional을 포함하는 스트림을 쉽게 처리할 수 있도록 Optional에 stream() 메소드를 추가했다.

- 예시) 사람 목록을 이용해 가입한 보험 회사 이름 찾기
	```java
	public Set<String> getCarInsuranceNames(List<Person> persons) {
		return persons.stream()
					.map(Person::getCar) // 사람 목록을 각 사람이 보유한 자동차의 Optional<Car> 스트림으로 변환
					.map(optCar -> optCar.flatMap(Car::getInsurance)) // FlatMap 연산을 이용해 Optional<Car> 을 해당 Optional<Insurance> 로 변환
					.map(optIns -> optIns.map(Insurance::getName)) // Optional<Insurance>를 해당 이름의 Optional<String> 으로 매핑
					.flatMap(Optional::stream) // Stream<Optional<String>> 을 현재 이름을 포함하는 Stream<String> 으로 변환
					.collect(toSet()); // 결과 문자열을 중복되지 않은 값을 갖도록 집합으로 수집
					
	}
	```
		- 3번의 map 메소드로 변환 과정을 거친결과 Stream<Optional<String>> 을 얻는데, 마지막 결과를 얻으려면 빈 Optional을 제거하고 값을 언랩해야한다.
			- 다음 코드 처럼 filter, map을 순차적으로 이용해 결과를 얻을 수 있다.
				```java
				Stream<Optional<String>> stream = ...
				Set<String> result = stream.filter(Optional::isPresent)
										.map(Optional::get)
										.collect(toSet());
				```
	
	- 하지만 위 코드에서 확인되듯이 Optional 클래스의 ```stream()``` 메소드를 이용하면 한번의 연산으로 같은 결과를 얻을 수 있다.
			- 이 메소드는 각 Optioanl이 비어있는지 아닌지에 따라 Optional을 0개 이상의 항목을 포함하는 스트림으로 변환한다.
			- 따라서 이를 원래 스트림에 호출하는 flatMap 메소드로 전달할 수 있다. ```.flatMap(Optional::stream)``` 부분
			- ```.flatMap(Optional::stream)``` 이 기법을 이용하면 한 단계의 연산으로 값을 포함하는 Optional을 언랩하고 비어있는 Optional은 건너뛸 수 있다.
			

#### 디폴트 액션과 Optional 언랩

- 위에서는 빈 Optional인 상황에서 기본 값을 반환하도록 ```orElse```로 Optional을 읽었다.

- Optioanl 클래스는 이외에도 Optional 인스턴스에 포함된 값을 읽는 다양한 방법을 제공한다.

- ```get()```
	- 값을 읽는 가장 간단한 메서드, 동시에 가장 안전하지 않은 메서드
	- get은 래핑된 값이 있으면 해당 값을 반환하고, 없으면 ```NosuchElementException``` 을 발생시킨다.
	- 따라서 Optional에 값이 반드시 있다고 가정할 수 있는 상황이 아니면 get 메소드를 사용하지 않는 것이 바람직하다.
	
- ```orElse(T other)```
	- orElse 메소드를 이용하면 Optional이 값을 포함하지 않을 때 기본 값을 제공할 수 있다.
	
- ```orElseGet(Supplier<? extends T> other)``` 
	- orElse 메서드에 대응하는 게으른 버전의 메소드다.
	- Optional에 값이 없을 때만 Supplier가 실행되기 때문이다.
	- 디폴트 메소드를 만드는데 시간이 걸리거나 Optional이 비어있을 때만 기본 값을 생성하고싶다면 해당 메소드를 사용해야한다.

- ```orElseThrow(Supplier<? extends X> exceptionSupplier)```
	- Optional이 비어있을 때 예외를 발생시킨다는 점에서 get 메소드와 비슷하다.
	- 하지만 이 메소드는 발생시킬 예외의 종류를 선택할 수 있음
	
- ```ifPresent(Consumer<? super T> consumer)```
	- 값이 존재할 때 인수로 넘겨준 동작을 실행할 수 있다.
	- 값이 없으면 아무 일도 일어나지않는다.
	
- 자바 9에서는 다음의 인스턴스 메소드가 추가 되었다.
	- ```ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)```
		- Optional이 비었을 때 실행할 수 있는 Runnable을 인수로 받는다는 점만 ifPresent와 다르다.
		

#### 두 Optional 합치기

- Person과 Car 정보를 이용해서 가장 저렴한 보험료를 제공하는 보험회사를 찾는 복잡한 비즈니스 로직을 구현한 외부 서비스가 있다고 가정하자
	```java
	public Insurance findCheapestInsurance(Person person, Car car){
		//다양한 보험 회사가 제공하는 서비스 조회
		//모든 결과 데이터 비교
		return cheapestCompany;
	}
	```
	
- Optional 언랩하지않고 두 Optional 합치기
	- 다음 코드처럼 어떤 조건문도 사용하지 않고 한줄의 코드로 메소드를 구현할 수 있다.
		```java
		public Optional<Insurance> nullSafeFindCheapestInsurance(Optional<Person> person, Optional<Car> car){
			return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
		}
		```
		
- 필터로 특정값 거르기
	- 종종 객체의 메서드를 호출해서 어떤 프로퍼티인지 확인해야 할 때가 있다.
	
	- 예를 들어 보험회사 이름이 'CambridgeInsurance' 인지 확인해야한다고 가정하자.
		- 이 작업을 안전하게 수행하려면 Insurance 객체가 null인지 여부를 확인한 다음에 getName 메소드를 호출해야 한다.
		- Optional 객체에 filter 메소드를 이용해서 다음과 같이 코드를 구현할 수 있다.
			```java
			Optional<Insurance> optInsurance = ...;
			
			optInsurance.filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
						.ifPresent(x -> System.out.println("ok"));
			```
		- filter 메소드는 프레디케이트를 인수로 받는데
			- Optional 객체가 값을 가지고, 프레디케이트와 일치하면 filter메소드는 그 값을 반환한다.
			- 그렇지 않으면 빈 Optional 객체를 반환한다.
		
	- Optional은 최대 한 개의 요소를 포함할 수 있는 스트림과 같으므로 이 사실을 적용하면 filter 연산의 결과를 쉽게 이해할 수 있다.
	
	- 인수 person이 minAge 이상의 나이일 때만 보험회사 이름을 반환하는 예제
		```java
		public String getCarInsuranceName(Optional<Person> person, int minAge){
			return person.filter(p -> p.getAge() >= minAge)
						.flatMap(Person::getCar)
						.flatMap(Car::getInsurance)
						.map(Insurance::getName)
						.orElse("Unknown");
		}
		```

#### 표) Optional 클래스의 메소드

|메소드|설명|
|----|---|
|empty|빈 Optional 인스턴스 반환|
|filter|값이 존재하며 프레디케이트와 일치하면 값을 포함하는 Optional을 반환하고, </br> 값이 없거나 프레디케이트와 일치하지 않으면 빈 Optional을 반환함|
|flatMap|값이 존재하면 인수로 제공된 함수를 적용한 결과 Optional을 반환하고, </br> 값이 없으면 빈 Optional을 반환|
|get|값이 존재하면 Optional이 감싸고 있는 값을 반환하고, </br> 값이 없으면 NoSuchElementException이 발생함|
|ifPresent|값이 존재하면 지정된 Consumer를 실행하고, </br> 값이 없으면 아무일도 일어나지 않음|
|ifPresentOrElse|값이 존재하면 지정된 Consumer를 실행하고, </br> 값이 없으면 아무일도 일어나지 않음|
|isPresent|값이 존재하면 true, 없으면 false 반환|
|map|값이 존재하면 제공된 매핑 함수 적용|
|of|값이 존재하면 값을 감싸는 Optional을 반환하고, </br> 값이 null이면 NullPointerException 을 발생함|
|ofNullable|값이 존재하면 값을 감싸는 Optional을 반환하고, </br> 값이 null이면 빈 Optional을 반환함|
|or|값이 존재하면 같은 Optional을 반환하고, </br> 값이 없으면 Supplier에서 만든 Optional을 반환|
|orElse|값이 존재하면 값을 반환하고, </br> 값이 없으면 기본값을 반환함|
|orElseGet|값이 존재하면 값을 반환하고, </br> 값이 없으면 Supplier에서 제공하는 값을 반환함|
|orElseThrow|값이 존재하면 값을 반환하고, </br> 값이 없으면 Supplier에서 생성한 예외를 발생함|
|stream|값이 존재하면 존재하는 값만 포함하는 스트림을 반환하고, </br> 값이 없으면 빈 스트림을 반환|

</br>

### Optional을 사용한 실용 예제

</br>

#### 잠재적으로 null이 될수있는 대상을 Optional로 감싸기

```java
Object value = map.get("key");
```
	- 문자열 'key'에 해당하는 겂이 없으면 null이 반환될 것이다.

```java
Optional<Obejct> value = Optional.ofNullable(map.get("key"));
```
	- 이와 같은 코드를 이용해서 null일 수 있는 값을 Optional로 안전하게 변환할 수 있다.

#### 예외와 Optional 클래스

- 예외를 발생시키는 메서드에서는 try/catch 블록을 사용해야 한다.

- ```Integer.parseInt``` 메소드 사용 시 정수로 변환할 수 없는 문자열 문제를 빈 Optional로 해결할 수 있다.
	```java
	public static Optional<Integer> stringToInt(String s){
		try{
			return Optional.of(Integer.parseInt(s)); // 문자열을 정수로 변환할 수 있으면 정수로 변환된 값을 포함하는 Optional 반환
		}catch (NumberForamtException e){
			return Optional.empty(); // 그렇지 않으면 빈 Optional 반환
		}
	}
	```
	
	- parseInt를 감싸는 작은 유틸리티 메서드를 구현해서 Optional을 반환한다.
	
- 위와 같이 Optional 유틸리티 메소드를 만들어 놓으면 기존처럼 거추장스러운 try/catch 로직을 사용할 필요가 없다.

#### 기본형 Optional을 사용하지 말아야 하는 이유

- 스트림 처럼 Optional도 기본형으로 특화된 ```OptionalInt```, ```OptionalLong```, ```OptionalDouble``` 등의 클래스를 제공한다.

- 위의 stringToInt 예제에서 Optional<Integer> 대신 OptionalInt를 반환할 수 있다.

- Optional은 스트림과 다르게 최대 요소 수가 한개이므로 기본형 특화클래스로 성능을 개선할 수 없다.

- 기본형 특화 Optional은 map, flatMap, filter 등을 지원하지 않으므로 기본형 특화 Optional을 사용할 것을 권장하지 않는다.

- 게다가 스트림과 마찬가지로 기본형 특화 Optional로 생성한 결과는 다른 일반 Optional 과 혼용할 수 없다.

#### 응용

- 프로퍼티에서 지속 시간을 읽는 명령형 코드가 있다고 가정하자.
	```java
	public int readDuration(Properties props, String name){
		String value = props.getProperty(name);
		
		if(value != null){ // 요청한 이름에 해당하는 프로퍼티 존재하는지 확인
			try{
				int i = Integer.parseInt(value); // 문자열 프로퍼티를 숫자로 변환하기 위해 시도한다.
			}catch(NumberFor){}
		}
		return 0; // 하나의 조건이라도 실패하면 0을 반환한다.
	}
	```
	
	- if문과 try/catch 블록이 주업되면서 구현 코드가 복잡해졌고 가독성도 나빠졌다.
	
	- 간단하게 구현한 코드 (위에서 구현한 유틸리티 클래스 사용)
		```java
		public int readDuration(Properties props, String name){
			return Optional.ofNullable(props.getProperty(name))
						.flatMap(OptionalUtility::stringToInt)
						.filter(i -> i > 0)
						.orElse(0);
		}
		```
		
- 정리
	- 자바 8에서는 값이 있거나 없음을 표현할 수 있는 클래스 ```java.util.Optional<T>``` 를 제공한다.
	- 팩토리 메서드 ```Optional.empty```, ```Optional.of```, ```Optional.ofNullable``` 등을 이용해서 Optional 객체를 만들 수 있다.
	- Optional 클래스는 스트림과 비슷한 연산을 수행하는 map, flatMap, filter 등의 메서드를 제공한다.
	- Optional로 값이 없는 상황을 적절하게 처리하도록 강제할 수 잇다. 즉, Optional로 예상치 못한 null 예외를 방지할 수 있다.
	- Optional을 활용하면 더 좋은 API를 설계할 수 있다. 즉, 사용자는 메서드의 시그니처만 보고도 Optional 값이 사용되거나 반환되는지 예측할 수 있다.
