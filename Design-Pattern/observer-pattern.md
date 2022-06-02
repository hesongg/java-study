observer-pattern.md

# Java Observer Pattern

#### Observer 패턴 다이어그램
![image](https://user-images.githubusercontent.com/77953474/171556989-1fe423fd-afd6-4d56-8562-c37a7395871c.png)

출처) https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4

</br>

- 옵저버
	- 어떤 이벤트가 발생했을 때, 한 객체(주제-subject라 불리는)가 다른 객체 리스트(옵저버-observer 라 불리는) 에 
	  자동으로 알림을 보내야하는 상황에서 옵저버 디자인 패턴을 사용한다. GUI 애플리케이션에서 옵저버 패턴이 자주 등장함.
	  
	- 옵저버 예제) 다양한 옵저버를 그룹화할 ```Observer``` 인터페이스 필요하다. 
		```java
		interface Observer {
			void notify(String tweet);
		}
		class NYTimes implements Observer {
			public void notify(String tweet) {
				System.out.println(tweet);
			}
		}
		```
	
	- 그리고 주제도 구현해야 한다. 다음은 ```Subject``` 인터페이스 정의다.
		```java
		interface Subject {
			void registerObserver(Observer o);
			void notifyObservers(String tweet);
		}
		```
	
	- 주제는 ```registerObserver``` 메서드로 새로운 옵저버를 등록한 다음에 ```notifyObservers``` 메서드로 트윗의 옵저버에 이를 알린다.
		```java
		Class Feed implements Subject {
			private final List<Observer> observers = new ArrayList<>();
			public void registerObserver(Observer o) {
				this.observers.add(o);
			}
			public void notifyObservers(String tweet) {
				observers.forEach(o -> o.notify(tweet));
			}
		}
		```
	
	- 구현은 간단하다. Feed는 트윗을 받았을 때 알림을 보낼 옵저버 리스트를 유지한다.
		```java
		Feed f = new Feed();
		f.registerObserver(new NYTimes());
		f.notifyObservers(“notify Observer !”);
		```
	
		- 람다 표현식 사용하기
			- 옵저버를 명시적으로 인스턴스화하지 않고 람다 표현식을 직접 전달해서 실행할 동작을 지정할 수 있다. 
				```java
				f.registerObserver((String tweet) -> {
					System.out.println(“tweet !”);	
				});
				```
				
		- 항상 람다 표현식을 사용하는것은 아니다. 이 예제에서는 실행해야 할 동작이 비교적 간단하므로 람다 표현식으로 불필요한 코드를 제거하는 것이 바람직하다. 
			하지만 옵저버가 상태를 가지며, 여러 메서드를 정의하는 등 복잡하다면 기존의 클래스 구현방식을 고수하는 것이 바람직 할 수도 있다.
