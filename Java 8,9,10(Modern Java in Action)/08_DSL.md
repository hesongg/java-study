# Java 8, 9, 10 Study
- References) Modern Java in Action (by RAOUL-GABRIEL URMA, MARIO FUSCO, ALAN MYCROFT) 을 읽고 정리
- 참고한 책과 내용이 다를 수 있음
- 소스코드 참고 : http://www.hanbit.co.kr/src/10202

</br></br>

# Chapter 10. 람다를 이용한 도메인 전용 언어

### 도메인 전용 언어(Domain-Specific Languages, DSL)

- DSL은 특정 비즈니스 도메인의 문제를 해결하려고 만든 언어
- 자바에서는 도메인을 표현할 수 있는 클래스와 메소드 집합이 필요하다.
	DSL 이란 특정 비즈니스 도메인을 인터페이스로 만든 API라고 생각 할 수 있다.
- 다음 두 가지 필요성을 생각하면서 DSL을 개발해야 함
	- 의사 소통의 왕 : 우리 코드의 의도가 명확히 전달되어야 하고, 프로그래머가 아닌 사람도 이해할 수 있어야한다.
	- 한 번 코드를 구현하지만 여러 번 읽는다 : 다른 사람이 쉽게 이해할 수 있도록 코드를 구현해야 한다.

- DSL의 장점과 단점
	- 장점
		- 간결함 : API는 비즈니스 로직을 간편하게 캡슐화하므로 반복을 피할 수 있고, 코드를 간결하게 만들 수 있다
		- 가독성 : 도메인 영역의 용어를 사용하므로 비 도메인 전문가도 코드를 쉽게 이해할 수 있다.
		- 유지보수성 향상
		- 높은 수준의 추상화 : DSL은 도메인과 같은 추상화 수준에서 동작하므로 도메인의 문제와 직접적으로 관련되지않은 세부사항을 숨긴다.
		- 집중 : 비즈니스 도메인의 규칙을 표현할 목적으로 설계된 언어이므로 프로그래머가 특정 코드에 집중할 수 있다.
		- 관심사분리 : 지정된 언어로 비즈니스 로직을 표현함으로 애플리케이션의 인프라구조와 관련된 문제와 
			독립적으로 구성된 비즈니스 관련된 코드를 사용함으로써 집중하기가 용이하다.
	- 단점
		- DSL 설계의 어려움 : 간결하게 제한적인 언어에 도메인 지식을 담는 것이 쉬운 작업은 아니다.
		- 개발 비용 : 코드에 DSL을 추가하는 작업은 많은 비용과 시간이 소모되는 작업이다
		- 추가 우회 계층 : DSL은 추가적인 계층으로 도메인 모델을 감싸며 이 때 계층을 최대한 작게 만들어 성능 문제를 회피한다.
		- 새로 배워야 하는 언어
		- 호스팅 언어 한계 : 일부 자바 같은 범용 프로그래밍 언어는 장황하고 엄격한 문법을 가지고 있다.
			이런 언어로는 사용자 친화적인 DSL을 만들기 힘들다. 자바 8의 람다는 이 문제를 해결할 강력한 새 도구다.
		
- 내부 DSL
	- 이 책에서 내부 DSL이란 자바로 구현한 DSL을 의미한다.
	- 람다를 이용하면 익명 내부 클래스를 사용해 DSL을 구현하는 것보다 장황함을 크게 줄일 수 있다.
	- 순사 자바로 DSL을 구현하여 얻을 수 있는 이점
		- 외부 DSL에 비해 새로운 패턴과 기술을 배워 DSL을 구현하는 노력이 현저히 줄어든다
		- 나머지 코드와 함께 DSL을 컴파일 할 수 있다. 외부 DSL을 만드는 도구를 사용할 필요가 없으므로 추가로 비용이 들지 않는다.
		- 복잡한 외부 도구를 배울 필요가 없다.
		- 추가로 DSL을 개발해야하는 상황에서의 유지보수성
		
- 다중 DSL
	- 자바와 호환되는 다른 언어를 함께 사용하여 더욱 친화적으로 만든 DSL
	- 두 개 이상의 언어가 혼재하므로 빌드 과정을 개선해야 한다.
	- 완벽하지 않은 호환성 때문에 성능이 손실될 때도 있다
	
- 외부 DSL
	- 새 언어를 사용하여 DSL 구현하는 것
	- 외부 DSL을 개발하는 가장 큰 장점은 외부 DSL이 제공하는 무한한 유연성이다.

</br>
	
### 최신 자바 API의 작은 DSL
 
- 자바의 새로운 기능의 장점을 적용한 첫 API는 네이티브 자바 API 자신이다. 
 
- Comparator 인터페이스 예를 통해 람다가 어떻게 네이티브 자바 API의 재사용성과 메소드 결합도를 높였는지 확인해보자
	- 사람(Persons)을 가리키는 객체 목록을 나이를 기준으로 정렬한다고 가정하자. 
		- 람다가 없으면 내부 클래스로 Comparator 인터페이스를 구현해야 한다.
			```java
			Collections.sort(persons, new Comparator<Person>() {
				public int compare(Person p1, Person p2) {
					return p1.getAge() - p2.getAge();
				}
			}
			```
		- 내부 클래스를 간단한 람다 표현식으로 바꿀 수 있다.
			```java
			Collections.sort(people, (p1, p2) -> p1.getAge() - p2.getAge());
			```
		- ```Comparator.comparing``` 메소드를 임포트 했을 때
			```java
			Collections.sort(persons, comparing(p -> p.getAge()));
			```
		- 람다를 메소드 참조를 대신해 코드 개선
			```java
			Collections.sort(persons, comparing(Person::getAge));
			```
		- 자바 8에서 추가된 reverse 메소드 사용
			```java
			Collections.sort(persons, comparing(Person::getAge).reverse());
			```
		- 다음처럼 이름으로 비교를 수행하는 Comparator를 구현해 같은 나이의 사람들을 알파벳순으로 정렬할 수도 있다.
			```java
			Collections.sort(persons, comparing(Person::getAge)
										.thenComparing(Person::getName));
			```
		- 이 작은 API는 컬렉션 도메인의 최소 DSL이다.
		- 작은 영역에 국한된 예제지만 이미 람다와 메소드 참조를 이용한 DSL이 코드의 가독성, 재사용성, 결합성을 높일 수 있는지 보여준다.
		
</br>

#### 스트림 API는 컬렉션을 조작하는 DSL

- Stream 인터페이스는 네이티브 자바 API에 작은 내부 DSL을 적용한 좋은 예다.

- Stream은 컬렉션의 항목을 필터, 정렬, 변환, 그룹화, 조작하는 작지만 강력한 DSL로 볼 수 있다.

- 로그 파일을 읽어서 "ERROR" 라는 단어로 시작하는 파일의 첫 40행을 수집하는 작업을 수행한다고 가정하자.
	- 반복 형식으로 이 작업을 처리 했을 때 (에러 처리 코드 생략)
		```java
		List<String> errors = new ArrayList<>();
		int errorCount = 0;
		BufferedReader br = new BufferedReader(new FileReader(fileName));
		
		String line = br.readLine();
		while(errorCount < 40 && line != null){
			if(line.startsWith("ERROR")) {
				erros.add(line);
				errorCount++;
			}
			line = br.readLine();
		}
		```
		- 같은 의무를 지닌 코드가 여러 행에 분산되어 있다.
			- FileReader 가 만들어짐
			- 파일이 종료되었는지 확인하는 while 루프의 두 번째 조건
			- 파일의 다음 행을 읽는 while 루프의 마지막 행
		- 마찬가지로 첫 40행을 수집하는 코드도 세 부분으로 흩어져있다.
			- errorCount 변수를 초기화하는 코드
			- while 루프의 첫 번째 조건
			- "Error"를 로그에서 발견하면 카운터를 증가시키는 행
	- Stream 인터페이스를 이용하여 함수형으로 코드를 구현하면 더 쉽고 간결하게 코드를 구현할 수 있다.
		```java
		List<String> errors = Files.lines(Paths.get(fileName)) // 파일을 열어서 문자열 스트림을 만듦
									.filter(line -> line.startsWith("ERROR")) 
									.limit(40)
									.collect(toList());
		```
		- Files.lines 는 정적 유틸리티 메소드로 Stream<String> 을 반환한다.

</br>

#### 데이터를 수집하는 DSL인 Collectors

- Stream 인터페이스는 데이터 리스트를 조작하는 DSL로 간주할 수 있음을 확인했다.
 마찬가지로 Collector 인터페이스는 데이터 수집을 수행하는 DSL로 간주 할 수 있다.

- Comparator 인터페이스는 다중 필드 정렬을 지원하도록 합쳐질 수 있다.

- Collectors 는 다중 수준 그룹화를 달성할 수 있도록 합쳐질 수 잇다.
	- 예를 들어 다음 예제처럼 자동차를 브랜드별 그리고 색상 별로 그룹화 가능
		```java
		Map<String, Map<Color, List<Car>>> carsByBrandAndColor =
			cars.stream().collect(groupingBy(Car::getBrand,
									groupingBy(Car::getColor)));
		```
			
- 네이티브 자바 API를 좀 더 자세히 살펴보고 내부 설계 결정 이유를 생각해보면 가독성 있는 DSL을 구현하는
	유용한 패턴과 기법을 배울 수 있다.

</br>

#### 자바로 DSL을 만드는 패턴과 기법

- DSL은 특정 도메인 모델에 적용할 친화적이고 가독성 높은 API를 제공한다.

- 먼저 간단한 도메인 모델을 정의해보자
	- 예제 도메인 모델을 세 가지로 구성된다.
	- 첫 번째는 주어진 시장에 주식 가격을 모델링하는 순수 자바 빈즈다.
		```java
		public class Stock {

		  private String symbol;
		  private String market;

		  public String getSymbol() {
			return symbol;
		  }

		  public void setSymbol( String symbol ) {
			this.symbol = symbol;
		  }

		  public String getMarket() {
			return market;
		  }

		  public void setMarket( String market ) {
			this.market = market;
		  }

		  @Override
		  public String toString() {
			return String.format("Stock[symbol=%s, market=%s]", symbol, market);
		  }

		}
		```
	
	- 두 번째는 주어진 가격에서 주어진 양의 주식을 사거나 파는 거래다.
		```java
		public class Trade {

			public enum Type {
				BUY,
				SELL
			}

			private Type type;
			private Stock stock;
			private int quantity;
			private double price;

			public Type getType() {
				return type;
			}

			public void setType(Type type) {
				this.type = type;
			}

			public int getQuantity() {
				return quantity;
			}

			public void setQuantity(int quantity) {
				this.quantity = quantity;
			}

			public double getPrice() {
				return price;
			}

			public void setPrice(double price) {
				this.price = price;
			}

			public Stock getStock() {
				return stock;
			}

			public void setStock(Stock stock) {
				this.stock = stock;
			}

			public double getValue() {
				return quantity * price;
			}

			@Override
			public String toString() {
				return String.format("Trade[type=%s, stock=%s, quantity=%d, price=%.2f]", type, stock, quantity, price);
			}

		}
		```
	
	- 마지막으로 고객이 요청한 한 개 이상의 거래의 주문이다.
		```java
		public class Order {

		  private String customer;
		  private List<Trade> trades = new ArrayList<>();

		  public void addTrade( Trade trade ) {
			trades.add( trade );
		  }

		  public String getCustomer() {
			return customer;
		  }

		  public void setCustomer( String customer ) {
			this.customer = customer;
		  }

		  public double getValue() {
			return trades.stream().mapToDouble( Trade::getValue ).sum();
		  }

		  @Override
		  public String toString() {
			String strTrades = trades.stream().map(t -> "  " + t).collect(Collectors.joining("\n", "[\n", "\n]"));
			return String.format("Order[customer=%s, trades=%s]", customer, strTrades);
		  }

		}
		```
	
	- 해당 도메인 모델로 BigBank 라는 고객이 요청한 두 거래를 포함하는 주문을 만들어보자.
		```java
		Order order = new Order();
		order.setCustomer("BigBank");

		Trade trade1 = new Trade();
		trade1.setType(Trade.Type.BUY);

		Stock stock1 = new Stock();
		stock1.setSymbol("IBM");
		stock1.setMarket("NYSE");

		trade1.setStock(stock1);
		trade1.setPrice(125.00);
		trade1.setQuantity(80);
		order.addTrade(trade1);

		Trade trade2 = new Trade();
		trade2.setType(Trade.Type.BUY);

		Stock stock2 = new Stock();
		stock2.setSymbol("GOOGLE");
		stock2.setMarket("NASDAQ");

		trade2.setStock(stock2);
		trade2.setPrice(375.00);
		trade2.setQuantity(50);
		order.addTrade(trade2);
		```
	- 위 코드는 장황하고 비개발자인 도메인 전문가가 위 코드를 이해하고 검증하기를 기대하기 힘들다.
	- 조금더 직접적이고, 직관적으로 도메인 모델을 반영할 수 있는 DSL이 필요하다.
        
        </br>

	    #### 메소드 체인
	
		- DSL에서 가장 흔한 방식 중 하나를 살펴보자. 이 방법을 이용하면 한 개의 메소드 호출 체인으로 거래 주문을 정의할 수 있다.
			```java
			Order order = forCustomer("BigBank");
					.buy(80).stock("IBM").on("NYSE").at(125.00)
					.sell(50).stock("GOOGLE").on("NASDAQ").at(375.00)
					.end();
			```
			- 플루언트 API로 도메인 객체를 만드는 몇 개의 빌더를 구현해야 한다.
			- 다음 코드와 같이 최상위 수준 빌더를 만들고 주문을 감싼 다음, 한 개 이상의 거래를 주문에 추가할 수 있어야 한다.
				```java
				public class MethodChainingOrderBuilder {

				  public final Order order = new Order(); //빌더로 감싼 주문

				  private MethodChainingOrderBuilder(String customer) {
					order.setCustomer(customer);
				  }

				  public static MethodChainingOrderBuilder forCustomer(String customer) {
					//고객의 주문을 만드는 정적 팩토리 메소드
					return new MethodChainingOrderBuilder(customer);
				  }

				  public TradeBuilder buy(int quantity) {
					//주식을 사는 TradeBuilder 만들기
					return new TradeBuilder(this, Trade.Type.BUY, quantity);
				  }

				  public TradeBuilder sell(int quantity) {
					//주식을 파는 TradeBuilder 만들기
					return new TradeBuilder(this, Trade.Type.SELL, quantity);
				  }

				  private MethodChainingOrderBuilder addTrade(Trade trade) {
					order.addTrade(trade); //주문에 주식 추가
					return this; //유연하게 추가 주문을 만들어 추가할 수 있도록 주문 빌더 자체를 반환
				  }
				  
				  public Order end() {				
					return order; //주문 만들기를 종료하고 반환
				  }
				}
				```
			- 주문 빌더의 buy(), sell() 메소드는 다른 주문을 만들어 추가할 수 있도록 자신을 만들어 반환한다.
				```java
				public static class TradeBuilder {
					private final MethodChainingOrderBuilder builder;
					public final Trade trade = new Trade();

					private TradeBuilder(MethodChainingOrderBuilder builder, Trade.Type type, int quantity) {
					  this.builder = builder;
					  trade.setType(type);
					  trade.setQuantity(quantity);
					}

					public StockBuilder stock(String symbol) {
					  return new StockBuilder(builder, trade, symbol);
					}
				}
				```
			- 빌더를 계속 이어가려면 Stock 클래스의 인스턴스를 만드는 TradeBuilder의 공개 메소드를 이용해야 한다.
				```java
				public static class StockBuilder {

					private final MethodChainingOrderBuilder builder;
					private final Trade trade;
					private final Stock stock = new Stock();

					private StockBuilder(MethodChainingOrderBuilder builder, Trade trade, String symbol) {
					  this.builder = builder;
					  this.trade = trade;
					  stock.setSymbol(symbol);
					}

					public TradeBuilderWithStock on(String market) {
					  stock.setMarket(market);
					  trade.setStock(stock);
					  return new TradeBuilderWithStock(builder, trade);
					}

				}
				```
			- StockBuilder는 주식의 시장을 지정하고, 거래에 주식을 추가하고, 최종 빌더를 반환하는 on() 메소드 한 개를 정의한다.
				```java
				public static class TradeBuilderWithStock {

					private final MethodChainingOrderBuilder builder;
					private final Trade trade;

					public TradeBuilderWithStock(MethodChainingOrderBuilder builder, Trade trade) {
					  this.builder = builder;
					  this.trade = trade;
					}

					public MethodChainingOrderBuilder at(double price) {
					  trade.setPrice(price);
					  return builder.addTrade(trade);
					}

				 }
				```
			- 한 개의 공개 메소드 TradeBuilderWithStock은 거래되는 주식의 단위 가격을 설정한 다음
				원래 주문 빌더를 반환한다.
			- 코드에서 볼 수 있듯이 MethodChainingOrderBuilder가 끝날 때까지 다른 거래를 플루언트 방식으로 추가할 수 있다.
			- 이 접근 방법은 정적 메소드 사용을 최소화하고, 메소드 이름이 인수의 이름을 대신하도록 만듦으로 이런 형식의
				DSL 가독성을 개선하는 효과를 더한다.
			- 빌더를 구현해야 한다는 것이 메소드 체인의 단점이다.
				- 상위 수준의 빌더를 하위 수준의 빌더와 연결할 많은 접착 코드가 필요하다.
		
        </br>		
                
	    #### 중첩된 함수 이용
	
		- 중첩된 함수 DSL 패턴은 아름에서 알 수 있듯이 다른 함수 안에 함수를 이용해 도메인 모델을 만든다.
		
			```java
			Order order = order("BigBank",
								buy(80,
									stock("IBM", on("NYSE")), at(125.00)),
								sell(50,
									stock("GOOGLE", on("NASDAQ")), at(375.00))
								);
			```
			
		- 이 방식의 DSL을 구현하는 코드는 메소드체인 방식으로 구현한 코드에 비해 간단하다.
		
		- 다음 예제 코드의 NestedFunctionOrderBuilder는 이런 DSL형식으로 사용자에게 API를 제공할 수 있음을 보여준다.
			```java
			public class NestedFunctionOrderBuilder {

			  public static Order order(String customer, Trade... trades) {
				Order order = new Order();
				order.setCustomer(customer);
				Stream.of(trades).forEach(order::addTrade);
				return order;
			  }

			  public static Trade buy(int quantity, Stock stock, double price) {
				return buildTrade(quantity, stock, price, Trade.Type.BUY);
			  }

			  public static Trade sell(int quantity, Stock stock, double price) {
				return buildTrade(quantity, stock, price, Trade.Type.SELL);
			  }

			  private static Trade buildTrade(int quantity, Stock stock, double price, Trade.Type buy) {
				Trade trade = new Trade();
				trade.setQuantity(quantity);
				trade.setType(buy);
				trade.setStock(stock);
				trade.setPrice(price);
				return trade;
			  }

			  public static double at(double price) { // 거래된 주식의 단가를 정의하는 더미 메소드
				return price;
			  }

			  public static Stock stock(String symbol, String market) {
				Stock stock = new Stock(); // 거래된 주식 만들기
				stock.setSymbol(symbol);
				stock.setMarket(market);
				return stock;
			  }

			  public static String on(String market) { // 주식이 거래된 시장을 정의하는 더미 메소드 정의
				return market;
			  }

			}
			```
			
		- 메소드체인에 비해 함수의 중첩 방식이 도메인 객체 계층 구조에 그대로 반영된다는 것이 장점이다.
		
		- 해당 방식의 문제점
			- 결과 DSL에 더 많은 괄호를 사용해야 한다는 단점이 있음
			- 인수 목록을 정적 메소드에 넘겨줘야하는 제약이 있음
			- 도메인 객체에 선택 사항 필드가 있으면 인수를 생략할 수 있도록 여러 메소드 오버라이드를 구현해야 함
			- 인수의 의미가 이름이 아니라 위치에 의해 정의 되었다.
				- at(), on() 메소드에서 했던 것 처럼 인수의 역활을 확실하게 만드는 여러 더미 메소드를 이용해
				  해당 문제를 조금은 완화 할 수 있다.
		
        </br>
        
	    #### 람다 표현식을 이용한 함수 시퀀싱
		
        - 해당 DSL 패턴은 람다 표현식으로 정의한 함수 시퀀스를 사용한다.
			```java
			Order order = LambdaOrderBuilder.order( o -> {
			  o.forCustomer( "BigBank" );
			  o.buy( t -> {
				t.quantity(80);
				t.price(125.00);
				t.stock(s -> {
				  s.symbol("IBM");
				  s.market("NYSE");
				});
			  });
			  o.sell( t -> {
				t.quantity(50);
				t.price(375.00);
				t.stock(s -> {
				  s.symbol("GOOGLE");
				  s.market("NASDAQ");
				});
			  });
			});
			```
		
		- 이런 DSL을 만드려면 람다 표현식을 받아 실행해 도메인 모델을 만들어 내는 여러 빌더를 구현해야 한다.
		
		- 메소드 체인 패턴에는 주문을 만드는 최상위 빌더를 가졌지만 이번에는 ```Consumer``` 객체를 빌더가 인수로 받으므로
		  DSL 사용자가 람다 표현식으로 인수를 구현할 수 있게 했다.
			```java
			public class LambdaOrderBuilder {

			  private Order order = new Order(); // 빌더로 주문을 감쌈

			  public static Order order(Consumer<LambdaOrderBuilder> consumer) {
				LambdaOrderBuilder builder = new LambdaOrderBuilder();
				consumer.accept(builder);	// 주문 빌더로 전달된 람다 표현식 실행
				return builder.order;	// OrderBuilder의 Consumer를 실행해 만들어진 주문을 반환
			  }

			  public void forCustomer(String customer) {
				order.setCustomer(customer);	// 주문을 요청한 고객 설정
			  }

			  public void buy(Consumer<TradeBuilder> consumer) {
				trade(consumer, Trade.Type.BUY);	// 주식 매수 주문을 만들도록 TradeBuilder 소비
			  }

			  public void sell(Consumer<TradeBuilder> consumer) {
				trade(consumer, Trade.Type.SELL);	// 주식 매도 주문을 만들도록 TradeBuilder 소비
			  }

			  private void trade(Consumer<TradeBuilder> consumer, Trade.Type type) {
				TradeBuilder builder = new TradeBuilder();
				builder.trade.setType(type);
				consumer.accept(builder);	// TradeBuilder로 전달할 람다 표현식 실행
				order.addTrade(builder.trade);	// TradeBuilder의 Consumer를 실행해 만든 거래를 주문에 추가
			  }

			  public static class TradeBuilder {

				private Trade trade = new Trade();

				public void quantity(int quantity) {
				  trade.setQuantity(quantity);
				}

				public void price(double price) {
				  trade.setPrice(price);
				}

				public void stock(Consumer<StockBuilder> consumer) {
				  StockBuilder builder = new StockBuilder();
				  consumer.accept(builder);
				  trade.setStock(builder.stock);
				}

			  }

			  public static class StockBuilder {

				private Stock stock = new Stock();

				public void symbol(String symbol) {
				  stock.setSymbol(symbol);
				}

				public void market(String market) {
				  stock.setMarket(market);
				}

			  }

			}
			```
			
			- 이 패턴은 이전 두 가지 DSL 형식의 두 가지 장점을 더한다.
				- 메소드 체인 패턴처럼 플루언트 방식으로 거래 주문 정의 가능
				- 중첩 함수 형식 처럼 다양한 람다 표현식의 중첩 수준과 비슷하게 도메인 객체의 계층 구조 유지
			
			- 단점
				- 많은 설정 코드 필요
				- DSL 자체가 자바 8 람다 표현식 문법에 의한 잡음의 영향을 받는다.
		
        </br>
	    
        #### 조합하기
		
        - DSL 패턴 각자가 장단점을 갖고 있다. 새로운 DSL을 개발해 주식 거래 주문을 정의할 수 있다.
			```java
			Order order =
				forCustomer("BigBank",	// 최상위 수준 주문의 속성을 지정하는 중첩 함수
					buy(t -> t.quantity(80)	// 한 개의 주문을 만드는 람다 표현식
						.stock("IBM")	// 거래 객체를 만드는 람다 표현식 바디의 메서드 체인
						.on("NYSE")
						.at(125.00)),
					sell(t -> t.quantity(50)
						.stock("GOOGLE")
						.on("NASDAQ")
						.at(375.00)));
			```
			
		- 이 예제에서는 중첩된 함수 패턴을 람다 기법과 혼용했다.
		
			```java
			public class MixedBuilder {

			  public static Order forCustomer(String customer, TradeBuilder... builders) {
				Order order = new Order();
				order.setCustomer(customer);
				Stream.of(builders).forEach(b -> order.addTrade(b.trade));
				return order;
			  }

			  public static TradeBuilder buy(Consumer<TradeBuilder> consumer) {
				return buildTrade(consumer, Trade.Type.BUY);
			  }

			  public static TradeBuilder sell(Consumer<TradeBuilder> consumer) {
				return buildTrade(consumer, Trade.Type.SELL);
			  }

			  private static TradeBuilder buildTrade(Consumer<TradeBuilder> consumer, Trade.Type buy) {
				TradeBuilder builder = new TradeBuilder();
				builder.trade.setType(buy);
				consumer.accept(builder);
				return builder;
			  }
			  ```	
			  
		- 헬퍼 클래스 TradeBuilder 와 StockBuilder 는 내부적으로 메서드 체인 패턴을 구현해 플루언트 API를 제공한다.
			  ```java
                  public static class TradeBuilder {

                    private Trade trade = new Trade();

                    public TradeBuilder quantity(int quantity) {
                      trade.setQuantity(quantity);
                      return this;
                    }

                    public TradeBuilder at(double price) {
                      trade.setPrice(price);
                      return this;
                    }

                    public StockBuilder stock(String symbol) {
                      return new StockBuilder(this, trade, symbol);
                    }
                  }

                  public static class StockBuilder {

                    private final TradeBuilder builder;
                    private final Trade trade;
                    private final Stock stock = new Stock();

                    private StockBuilder(TradeBuilder builder, Trade trade, String symbol) {
                      this.builder = builder;
                      this.trade = trade;
                      stock.setSymbol(symbol);
                    }

                    public TradeBuilder on(String market) {
                      stock.setMarket(market);
                      trade.setStock(stock);
                      return builder;
                    }
                  }
			  }
			  ```
		
		- 이 예제 에서는 위에서 설명한 세 가지 DSL 패턴을 혼용해 가독성 있는 DSL을 만드는 방법을 보여준다.
		
		- 여러 패턴의 장점을 이용할 수 있지만 이 기법에도 결점이 있다.
			- 결과 DSL이 여러가지 기법을 혼용하고 있으므로 한 가지 기법을 적용한 DSL에 비해 사용자가 DSL을 배우는데 시간이 오래걸린다.
			
        </br>
        
	    #### DSL에 메소드 참조 사용하기
	
		- 주식 거래 도메인 모델에 다른 간단한 기능을 추가한다.
			- 주문의 총 합에 0개 이상의 세금을 추가해 최종값을 계산하는 기능을 추가한다.
				```java
				public class Tax {
				  public static double regional(double value) { 
					return value * 1.1; 
				  }

				  public static double general(double value) {
					return value * 1.3;
				  }

				  public static double surcharge(double value) {
					return value * 1.05;
				  }
				}
				```
		
		- 불리언 플래그를 설정하는 최소 DSL을 제공하는 TaxCalculator
			```java
			public class TaxCalculator {
			  private boolean useRegional;
			  private boolean useGeneral;
			  private boolean useSurcharge;

			  public TaxCalculator withTaxRegional() {
				useRegional = true;
				return this;
			  }

			  public TaxCalculator withTaxGeneral() {
				useGeneral= true;
				return this;
			  }

			  public TaxCalculator withTaxSurcharge() {
				useSurcharge = true;
				return this;
			  }
				
			}
			```
			
			- 다음 코드처럼 TaxCalculator는 지역 세금과 추가 요금을 주문에 추가하고 싶다는 점을 명확하게 보여준다.
				```java
				double value = new TaxCalculator().withTaxRegional()
												.withTaxSurcharge()
												.calculate(order);
				```
				
			- 코드가 장황하고, 도메인의 각 세금에 해당하는 불리언 필드가 필요하므로 확장성도 제한적이다.
				- 자바의 함수형 기능을 이용하여 리팩토링하면 더 간결하고 유연한 방식으로 같은 가독성을 달성할 수 있다.
					```java
					public class TaxCalculator {
						public DoubleUnaryOperator taxFunction = d -> d; //	주문 값에 적용된 모든 세금을 계산하는 함수

						public TaxCalculator with(DoubleUnaryOperator f) {
							taxFunction = taxFunction.andThen(f); // 새로운 세금 계산 함수를 얻어서 인수로 전달된 함수와 현재 함수를 합침
							return this;
						}
					
						public double calculateF(Order order) {
							return taxFunction.applyAsDouble(order.getValue()); // 주문의 총 합에 세금 계산 함수를 적용해 최종 주문값을 계산
						}
					}
					```
					
				- 리팩토링한 TaxCalculator는 다음처럼 사용가능
					```java
					double value = new TaxCalculator().with(Tax::regional)
													.with(Tax::surcharge)
													.calculateF(order);
					```
					
				- 메소드 참조는 코드를 간결하게 만든다. 
				- 새로운 세금 함수를 Tax클래스에 추가해도 함수형 TaxCalculator를 바꾸지 않고 바로 사용할 수 있는 유연성도 제공한다.

</br>

### 실생활의 자바 8 DSL

- DSL 패턴의 장점과 단점
	
    <table>
        <tr>
            <td>패턴 이름</td>
            <td>장점</td>
            <td>단점</td>
        </tr>
        <tr>
            <td>메서드 체인</td>
            <td>
                * 메소드 이름이 키워드 인수 역할을 한다. </br> 
                * 선택형 파라미터와 잘 동작한다. </br> 
                * DSL 사용자가 정해진 순서로 메서드를 호출하도록 강제할 수 있다. </br> 
                * 정적 메소드를 최소화하거나 없앨 수 있다. </br> 
                * 문법적 잡음을 최소화한다.
            </td>
            <td>
                * 구현이 장황하다. </br>
                * 빌드를 연결하는 접착 코드가 필요하다. </br>
                * 들여쓰기 규칙으로만 도메인 객체 계층을 정의한다.
            </td>
        </tr>
        <tr>
            <td>중첩 함수</td>
            <td>
                * 구현의 장황함을 줄일 수 있다. </br>
	            * 함수 중첩으로 도메인 객체 계층을 반영한다.
            </td>
            <td>
                * 정적 메서드이 사용이 빈번하다.</br>
                * 이름이 아닌 위치로 인수를 정의한다. </br>
                * 선택형 파라미터를 처리할 메서드 오버로딩이 필요하다.
            </td>
        </tr>
        <tr>
            <td>람다를 이용한 함수 시퀀싱</td>
            <td>
                * 선택형 파라미터와 잘 동작한다. </br>
                * 정적 메소드를 최소화하거나 없앨 수 있다. </br>
                * 람다 중첩으로 도메인 객체 계층을 반영한다. </br>
                * 빌더의 접착 코드가 없다.
            </td>
            <td>
                * 구현이 장황하다. </br>
	            * 람다 표현식으로 인한 문법적 잡음이 DSL 에 존재한다.
            </td>
        </tr>
    </table>
    
	
- 참고) DSL을 사용하는 여러 라이브러리
	- JOOQ 
	- 큐컴버
	- 스프링 통합
	
- 마치며
	- DSL의 주요 기능은 개발자와 도메인 전문가 사이의 간격을 좁히는 것
	
    - DSL은 크게 내부적, 외부적 DSL로 분류할 수 있다.
	
    - JVM 에서는 스칼라, 그루비등의 다른 언어로 다중 DSL 개발 가능, 이들 언어는 자바보다 유연하며 간결하지만 자바와 통합하려면 빌드 과정이 복잡해지며 
	  상호 호환성 문제도 생길 수 있다.
	
    - 자바의 장황함과 문법적 엄격함 때문에 보통 자바는 내부적 DSL을 개발하는 언어로는 적합하지 않다.
	  하지만 자바 8의 람다 표현식과 메서드 참조 덕분에 상황이 많이 개선 되었다.
	
    - 최신 자바는 자체 API에 작은 DSL을 제공한다.
		- Stream, Collectors 클레스 등에서 제공하는 작은 DSL은 특히 컬렉션 데이터의 정렬, 필터링, 변환, 그룹화에 유용
	
    - 자바로 DSL을 구현할 때 보통 메서드 체인, 중첩 함수, 함수 시퀀싱 세가지 패턴이 사용된다. 모든 기법을 한 개의 DSL에 합쳐 장점만을 누릴 수 있다.
