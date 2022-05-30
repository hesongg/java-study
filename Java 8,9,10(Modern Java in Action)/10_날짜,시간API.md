# Java 8, 9, 10 Study
- References) Modern Java in Action (by RAOUL-GABRIEL URMA, MARIO FUSCO, ALAN MYCROFT) 을 읽고 정리
- 참고한 책과 내용이 다를 수 있음
- 소스코드 참고 : http://www.hanbit.co.kr/src/10202

</br></br>

# 새로운 날짜와 시간 API

</br>

### LocalDate, LocalTime, Instant, Duration, Period 클래스

</br>

#### LocalDate와 LocalTime 사용

- ```LocalDate``` 인스턴스는 시간을 제외한 날짜를 표현하는 불변 객체다.

	- 정적 팩토리 메소드 of 로 LocalDate 인스턴스를 만들 수 있다.

	- 다음 코드에서 보여주는 것처럼 ```LocalDate``` 인스턴스는 연도, 달, 요일 등을 반환하는 메소드를 제공한다.
		```java
		LocalDate date = LocalDate.of(2014, 3, 18);	// 2014-03-18
		int year = date.getYear(); // 2014
		Month month = date.getMonth(); // MARCH
		int day = date.getDayOfMonth(); // 18
		DayOfWeek dow = date.getDayOfWeek(); // TUESDAY
		int len = date.lengthOfMonth(); // 31 (3월의 길이)
		boolean leap = date.isLeapYear(); // false (윤년이 아님)
		```
		
	- 팩토리 메소드 now는 시스템 시계의 정보를 이용해서 현재 날짜 정보를 얻는다.
		```java
		LocalDate today = LocalDate.now();
		```
		
	- get 메소드에 ```TemporalField```를 전달해서 정보를 얻는 방법도 있다.
		- ```TemporalField```는 시간 관련 객체에서 어떤 필드의 값에 접근할지 정의하는 인터페이스다.
		- 열거자 ```ChronoField``` 는 ```TemporalField``` 인터페이스를 정의하므로 다음 코드에서 보여주는 것 처럼 ```ChronoField``` 의 열거자 요소를 이용해서 원하는 정보를 쉽게 얻을 수 있다.
			```java
			int year = date.get(ChronoField.YEAR);
			int month = date.get(ChronoField.MONTH_OF_YEAR);
			int day = date.get(ChronoField.DAY_OF_MONTH);
			```
		
	- 다음 처럼 내장 메소드 ```getYear()```, ```getMonthValue```, ```getDayOfMonth``` 등을 이용해 가독성을 높일 수 있다.
		```java
		int year = date.getYear();
		int month = date.getMonthValue();
		int day = date.getDayOfMonth();
		```

</br>
	
- 마찬가지로 13:45:20 같은 시간을 ```LocalTime``` 클래스로 표현할 수 있다.
	- 오버로드 버전의 두 가지 정적메서드 ```of```로 LocalTime 인스턴스를 만들 수 있다.
	
	- 시간과 분을 인수로 받는 of 메서드와 시간과 분, 초를 인수로 받는 of 메서드가 있다.
	
	- LocalDate 클래스처럼 LocalTime 클래스는 다음과 같은 게터 메소드를 제공한다.
		```java
		LocalTime time = LocalTime.of(13, 45, 20); // 13:45:20
		int hour = time.getHour(); // 13
		int minute = time.getMinute(); // 45
		int second = time.getSecond(); // 20
		```
	
	- 날짜와 시간 문자열로 LocalDate와 LocalTime의 인스턴스를 만드는 방법도 있다. 
		- 다음처럼 ```parse``` 정적 메소드를 사용할 수 있다.
			```java
			LocalDate date = LocalDate.parse("2017-09-21");
			LocalTime time = LocalTime.parse("13:45:20");
			```
	- ```parse``` 메소드에 ```DateTimeFormatter```를 전달할 수도 있다.
		- 문자열을 LocalDate나 LocalTime으로 파싱할 수 없을 때 parse 메소드는 ```DateTimeParseException```을 일으킨다.
		
</br>

#### 날짜와 시간 조합

- ```LocalDateTime```은 LocalDate와 LocalTime을 쌍으로 갖는 복합 클래스다.

- LocalDateTime을 직접 만드는 방법과 날짜와 시간을 조합하는 방법
	```java
	LocalDateTime dt1 = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45, 20); // 2014-03-18T13:45
    LocalDateTime dt2 = LocalDateTime.of(date, time);
    LocalDateTime dt3 = date.atTime(13, 45, 20);
    LocalDateTime dt4 = date.atTime(time);
    LocalDateTime dt5 = time.atDate(date);
	```
	
- LocalDateTime의 ```toLocalDate``` 나 ```toLocalTime``` 메소드로 LocalDate나 LocalTime 인스턴스를 추출할 수 있다.
	```java
	LocalDate date1 = dt1.toLocalDate();	// 2014-03-18
    LocalTime time1 = dt1.toLocalTime();	// 13:45:20
	```

</br>

#### Instant 클래스 : 기계의 날짜와 시간

- 기계적인 관점에서 시간을 표현

- Instant 클래스는 유직스 에포크 시간(1970년 1월 1일 0 시 0분 0초 UTC)을 기준으로 특정 지점까지의 시간을 초로 표현한다.

- 팩토리 메소드 ```ofEpochSecond```에 초를 넘겨줘서 Instant 클래스 인스턴스를 만들 수 있다.

- ```Instant``` 클래스는 나노초(10억분의 1초)의 정밀도를 제공한다. 

- 또한 오버로드된 ```ofEpochSecond``` 메서드 버전에서는 두 번째 인수를 이용해서 나노초 단위로 시간을 보정 할 수 있다.
	- 두 번째 인수에는 0에서 999,999,999 사이의 값을 지정할 수 있다.

- 다음 네 가지 ```ofEpochSecond``` 호출 코드는 같은 Instant를 반환한다.
	```java
	Instant.ofEpochSecond(3);
	Instant.ofEpochSecond(3, 0);
	Instant.ofEpochSecond(2, 1_000_000_000);	// 2초 이후의 1억 나노초(1초)
	Instant.ofEpochSecond(4, -1_000_000_000);	// 4초 이전의 1억 나노초(1초)
	```
	
- ```Instant``` 클래스도 사람이 확인할 수 있도록 시간을 표시해주는 정적 팩토리 메소드 ```now```를 제공한다.
	- 하지만 Instant 는 기계전용의 유틸리티이고, 초와 나노초 정보를 포함한다.
	- 따라서 Instant는 사람이 읽을 수 있는 시간 정보는 제공하지 않는다.
		```java
		int day = Instant.now().get(ChronoField.DAY_OF_MONTH);
		```
		- 위코드는 다음과 같은 예외를 일으킨다.
		```java.time.temporal.UnsupportedTemporalTypeException: Unsupported field: DayOfMonth```
		
</br>

#### Duration과 Period 정의

- ```Duration``` 클래스의 정적 팩토리 메소드 ```between``` 으로 두 시간 객체 사이의 지속시간을 만들 수 있다.
	
	- 다음 코드와 같이 두 개의 LocalTime, 두개의 LocalDateTime, 또는 두 개의 Instant로 Duration을 만들 수 있다.
		```java
		Duration d1 = Duration.between(time1, time2);
		Duration d1 = Duration.between(dateTime1, dateTime2);
		Duration d2 = Duration.between(instant1, instant2);
		```
		
	- LocalDateTime은 사람이 사용하도록, Instant는 기계가 사용하도록 만들어진 클래스로 두 인스턴스는 서로 혼합할 수 없다.

	- Duration 클래스는 초와 나노초로 시간 단위를 표현하므로 between 메소드에 LocalDate를 전달할 수 없다.

- 년, 월, 일로 시간을 표현할 때는 ```Period``` 클래스를 사용한다.

	- Period 클래스의 팩토리 메서드 ```between```을 이용하면 두 LocalDate의 차이를 확인할 수 있다.
		```java
		Period tenDays = Period.between(LocalDate.of(2017, 9, 11),
										LocalDate.of(2017, 9, 17));
		```
		
- Duration과 Period 클래스는 자신의 인스턴스를 만들 수 있도록 다양한 팩토리 메소드를 제공한다.
	```java
	Duration threeMinutes = Duration.ofMinutes(3);
	Duration threeMinutes = Duration.of(3, ChronoUnit.MINUTES);
	
	Period tenDays = Period.ofDays(10);
	Period threeWeeks = Period.ofWeeks(3);
	Period twoYearsSixMonthsOneDay = Period.of(2, 6, 1);
	```
	
- Duration과 Period 클래스가 공통으로 제공하는 메소드
	|메소드|정적|설명|
	|----|---|---|
	|between|Y|두 시간 사이의 간격을 생성함|
	|from|Y|시간 단위로 간격을 생성함|
	|of|Y|주어진 구성 요소에서 간격 인스턴스를 생성함|
	|parse|Y|문자열을 파싱해서 간격 인스턴스를 생성함|
	|addTo|N|현재값의 복사본을 생성한 다음에 지정된 Temporal 객체에 추가함|
	|get|N|현재 간격 정보 값을 읽음|
	|isNegative|N|간격이 음수인지 확인|
	|isZero|N|간격이 0인지 확인|
	|minus|N|현재 값에서 주어진 시간을 뺀 복사본을 생성|
	|multipliedBy|N|현재값에 주어진 값을 곱한 복사본을 생성|
	|negated|N|주어진 값의 부호를 반전한 복사본을 생성함|
	|plus|N|현재 값에 주어진 시간을 더한 복사본을 생성|
	|subtractFrom|N|지정된 Temporal 객체에서 간격을 뺌|

</br>
	
### 날짜 조정, 파싱, 포매팅

- ```withAttribute``` 메소드로 기존의 LocalDate를 바꾼 버전을 직접 간단하게 만들 수 있다.
	- 다음 코드에서의 모든 메소드는 기존 객체를 바꾸지 않는다
	- 절대적인 방식으로 LocalDate의 속성 변경
		```java
		LocalDate date1 = LocalDate.of(2017, 9, 21);	// 2017-09-21
		LocalDate date2 = date1.withYear(2022);	// 2022-09-21
		LocalDate date3 = date2.withDayOfMonth(30);	// 2022-09-30
		LocalDate date4 = date3.with(ChronoField.MONTH_OF_YEAR, 5);	// 2022-05-30
		```
	
- 선언형으로 LocalDate를 사용하는 방법
	- 상대적인 방식으로 LocalDate 속성 변경
		```java
		LocalDate date1 = LocalDate.of(2017, 9, 21);	// 2017-09-21
		LocalDate date2 = date1.plusWeeks(1);	// 2017-09-28
		LocalDate date3 = date2.minusYears(6);	// 2011-09-28
		LocalDate date4 = date3.plus(6, ChronoField.MONTH);	// 2012-03-38
		```
		
- ```LocalDate```, ```LocalTime```, ```LocalDateTime```, ```Instant``` 등 날짜와 시간을 표현하는 모든 클래스는 비슷한 메소드를 제공한다.
	|메소드|정적|설명|
	|----|---|---|
	|from|Y|주어진 Temporal 객체를 이용해서 클래스의 인스턴스를 생성함|
	|now|Y|시스템 시계로 Temporal 객체를 생성함|
	|of|Y|주어진 구성 요소에서 Temporal 객체의 인스턴스를 생성함|
	|parse|Y|문자열을 파싱해서 Temporal 객체를 생성함|
	|atOffset|N|시간대 오프셋과 Temporal 객체를 합침|
	|atZone|N|시간대 오프셋과 Temporal 객체를 합침|
	|format|N|지정된 포매터를 이용해서 Temporal 객체를 문자열로 변환함</br>(Instant는 지원하지 않음)|
	|get|N|Temporal 객체의 상태를 읽음|
	|minus|N|특정 시간을 뺀 Temporal 객체의 복사본 생성|
	|plus|N|특정 시간을 더한 Temporal 객체의 복사본 생성|
	|with|N|일부 상태를 바꾼 Temporal 객체의 복사본 생성|
	
</br>

#### TemporalAdjusters 사용하기

- 날짜와 시간 API는 다양한 상황에서 사용할 수 있도록 다양한 ```TemporalAdjuster```를 제공한다.

- 미리 정의된 TemporalAdjusters 사용하기
	```java
	import static java.time.temporal.TemporalAdjusters.*;
	
	LocalDate date1 = LocalDate.of(2014, 3, 18)	// 2014-03-18
	LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY));	// 2014-03-23
	LocalDate date3 = date2.with(lastDayOfMonth());	// 2014-03-31
	```
	
- TemporalAdjusters 클래스의 팩토리 메소드 
	(표는 길어서 생략.. API 문서 참고 : http://goo.gl/e1krg1)
	
- 커스텀 ```TemporalAdjuster``` 구현
	- TemporalAdjuster 인터페이스 (함수형 인터페이스)
		```java
		@FunctionalInterface
		public interface TemporalAdjuster {
			Temporal adjustInto(Temporal temporal);
		}
		```
	
	- TemporalAdjuster 인터페이스 구현은 Temporal 객체를 어떻게 다른 Temporal 객체로 변환할지 정의한다.
		- 결국 TemporalAdjuster 인터페이스를 ```UnaryOperator<Temporal>``` 과 같은 형식으로 간주할 수 있다.
	
	- 구현 예시
	```java
	// 이 클래스는 날짜를 하루씩 다음날로 바꾸는데 토요일과 일요일은 건너뛴다. 
	date = date.with(new NextWorkingDay());
	
	public class NextWorkingDay implements TemporalAdjuster{
		@Override
		public Temporal adjustInto(Temporal temporal){
			DayOfWeek dow = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK));	// 현재 날짜 읽기
			
			int dayToAdd = 1;	// 보통은 하루 추가
			if(dow == DayOfWeek.FRIDAY) dayToAdd = 3;	// 금요일이면 3일 추가
			else if (dow == DayOfWeek.SATURDAY) dayToAdd = 2;	// 토요일이면 2일 추가
			return temporal.plus(dayToAdd, ChronoUnit.DAYS);	// 적정한 날 수만큼 추가된 날짜를 반환
		}
	}
	```
	
	- 람다표현식으로 정의하고 싶다면 ```UnaryOperator<LocalDate>```를 인수로 받는 TemporalAdjusters 클래스의
	  정적 팩토리 메소드 ```ofDateAdjuster```를 사용하는 것이 좋다.
		```java
		TemporalAdjuster nextWorkingDay = TemporalAdjusters.ofDateAdjuster(
			temporal -> {
				DayOfWeek dow = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK));
				int dayToAdd = 1;
				if(dow == DayOfWeek.FRIDAY) dayToAdd = 3;	// 금요일이면 3일 추가
				else if (dow == DayOfWeek.SATURDAY) dayToAdd = 2;	// 토요일이면 2일 추가
				return temporal.plus(dayToAdd, ChronoUnit.DAYS);	// 적정한 날 수만큼 추가된 날짜를 반환
			});
			
		date = date.with(nextWorkingDay);	
		```

</br>
	
#### 날짜와 시간 객체 출력과 파싱

- 서로 다른 두개의 포매터로 문자열을 만드는 예제
	```java
	LocalDate date = LocalDate.of(2014, 3, 18);
	String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE);	// 20140318
	String s1 = date.format(DateTimeFormatter.ISO_LOCAL_DATE);	// 2014-03-18
	```
	
- 반대로 날짜와 시간을 표현하는 문자열을 파싱해서 날짜 객체를 만들 수도 있다.
	```java
	LocalDate date1 = LocalDate.parse("20140318", DateTimeFormatter.BASIC_ISO_DATE);
	LocalDate date1 = LocalDate.parse("2014-03-18", DateTimeFormatter.ISO_LOCAL_DATE);
	```
	
- 기존의 ```java.util.DateFormat``` 클래스와 달리 모든 ```DateTimeFormatter```는 스레드에서 안전하게 사용할 수 있는 클래스이다.

- 특정 패턴으로 포매터 만들 수 있는 정적 팩토리 메소드도 제공한다.
	```java
	DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
	
	LocalDate date = LocalDate.of(2014, 3, 18);
	String formattedDate = date1.format(formatter);
	LocalDate date2 = LocalDate.parse(formattedDate, formatter);
	```
	
- 지역화된 DateTimeFormatter 만들기
	```java
	DateTimeFormatter italianFormatter = DateTimeFormatter.ofPattern("d. MMMM yyyy", Local.ITALIAN);
	
	LocalDate date1 = LocalDate.of(2014, 3, 18);
	String formattedDate = date.format(italianFormatter // 18. marzo 2014
	LocalDate date2 = LocalDate.parse(formattedDate, italianFormatter);
	```
	
- 세부적으로 포매터 제어
	```java
	DateTimeFormatter complexFormatter = new DateTimeFormatterBuilder()
        .appendText(ChronoField.DAY_OF_MONTH)
        .appendLiteral(". ")
        .appendText(ChronoField.MONTH_OF_YEAR)
        .appendLiteral(" ")
        .appendText(ChronoField.YEAR)
        .parseCaseInsensitive()
        .toFormatter(Locale.ITALIAN);
	```
	
</br>

### 다양한 시간대와 캘린더 활용 방법

</br>

#### 시간대 사용하기

- 표준 시간이 같은 지역을 묶어서 시간대 규칙 집합을 정의한다.

- ```ZoneId``` 의 ```getRules()``` 를 이용해서 해당 시간대의 규정을 획득할 수 있다.
	- 다음처럼 지역 id로 특정 ZoneId를 구분한다.
		```java
		ZoneId romeZone = ZoneId.of("Europe/Rome");
		```

- ZoneId의 새로운 메소드인 ```toZoneId```로 기존의 ```TimeZone``` 객체를 ```ZoneId``` 객체로 변환할 수 있다.
	```java
	ZoneId zoneId = TimeZone.getDefault().toZoneId();
	```
	
- 특정 시점에 시간대 적용
	```java
	LocalDate date = LocalDate.of(2014, Month.MARCH, 18);
	ZonedDateTime zdt1 = date.atStartOfDay(romeZone);
	
	LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45);
	ZonedDateTime zdt2 = dateTime.atZone(romeZone);
	
	Instant instant = Instant.now();
	ZonedDateTime zdt3 = instant.atZone(romeZone);
	```

- ```ZonedDateTime```의 개념
	```2014-05-14T15:33:05.941+01:00[Europe/London]

	ZonedDateTime = LocalDate + LocalTime + ZoneId
				  = LocalDateTime + ZoneId
	```
	
</br>

#### 정리

- 새로운 날짜와 시간 API 에서 날짜와 시간 객체는 모두 불변이다.

- 새로운 API는 각각 사람과 기계가 편리하게 날짜와 시간 정보를 관리할 수 있도록 두 가지 표현 방식 제공

- 날짜와 시간 객체를 절대적인 방법과 상대적인 방법으로 처리할 수 있으며 기존 인스턴스를 변환하지 않도록 처리 결과로 새로운 인스턴스가 생성

- TemporalAdjuster로 복잡한 동작 수행가능, 커스텀 날짜 변환 기능 정의 가능

- 날짜와 시간 객체를 특정 포맷으로 출력하고 파싱하는 포매터 정의 가능. 패턴을 이용하거나 프로그램으로 포매터 만들 수 있으며 
  포매터는 스레드 안정성 보장

- 특정 지역/장소에 상대적인 시간대 또는 UTC/GMT 기준의 오프셋을 이용해서 시간대 정의 가능.
  이 시간대를 날짜와 시간 객체에 적용해서 지역화 가능
  
- ISO-8601 표준 시스템을 준수하지 않는 캘린더 시스템도 사용 가능
