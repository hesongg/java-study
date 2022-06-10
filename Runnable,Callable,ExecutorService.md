### Runnable 과 Callable의 차이

Thread는 ```Runnable```과 ```Callable```의 구현된 함수를 수행한다는 공통점이 있다.

- 둘의 차이점
    - Runnable : 어떤 객체도 리턴하지 않음. Exception이 없다
    - Callable : 특정 타입의 객체를 리턴함. Exception을 발생시킬 수 있음

<br> 

### Runnable

- 인자가 없고 리턴 값이 없다
- Thread에 인자로 바로 전달할 수 잇다.

- 인터페이스 Runnable
    ```java
    public interface Runnable {
        public abstract void run();
    }
    ```
- 사용 예시
    ```java
    public class Example {
        public static void main(String[] args) {
            Thread thread = new Thread(
                () -> System.out.println("Runnable Test !"));
            thread.start();
        }
    }
    ```
    
<br>

### Callable

- 인자를 받지 않으며, 특정 타입의 객체 리턴
- call() 메소드 수행 중 ```Exception``` 발생 가능

- 인터페이스 ```Callable```
    ```java
    public interface Callable<V> {
        V call() throws Exception;
    }
    ```
    
 - 사용 예시
    ```java
    public class CallableExample {
        public static void main(String[] args) throws ExecutionException, InterruptedException {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Future<String> future = executor.submit(() -> "Callable Test !");

            // 결과가 리턴되기를 기다린다.
            System.out.println("result : " + future.get());
        }
    }
    ```
    
<br>

### ExecutorService

- ```ExecutorService``` : 병렬작업 시 여러개의 작업을 효율적으로 처리하기 위해 제공되는 JAVA 라이브러리
    - ThreadPool 기능 제공 (Queue로 Task를 관리한다.)

- Executors 클래스에서 제공하는 Static Factory Method(정적 팩토리 메소드)를 사용하여 ExecutorService 선언 가능
    ```java
    ExecutorService executorService = Executors.newCachedThreadPool();

    ExecutorService executorService = Executors.newFixedThreadPool(int nThreads);

    ExecutorService executorService = Executors.newSingleThreadExecutor();
    ```
    - ```CachedThreadPool```
        - 쓰레드를 캐싱하는 쓰레드풀 (일정시간동안 쓰레드를 검사하여 60초동안 작업이 없으면 Pool에서 제거)
        - CachedThreadPool은 쓰레드수가 폭발적으로 증가할 수 있다는 단점이 있다. 
            - Thread의 제한 없이 무한정 생성하고, 해당 쓰레드의 작업이 60초간 없을 경우 Pool에서 제거하는 방식이기 때문에 작업이 계속적으로 쌓이는 환경에서는 해당 Thread가 소멸되는 것보다, 생성되는 양이 더 많을 수 있다.
    - ```FixedThreadPool```
        - 고정된 개수를 가진 쓰레드풀
        - fixedThreadPool을 생성할때, 해당 머신의 CPU코어수를 기준으로 생성하면 더 좋은 퍼포먼스를 얻을 수 있다.
    - ```SingleThreadExecutor```
        - 한 개의 쓰레드로 작업을 처리하는 쓰레드풀 -> TaskPool의 개념이 더 적합
      
- ExecutorService 메소드 ```submit()```
    - Task를 할당하고 ```Future``` 타입의 결과값을 받는다. 결과 리턴이 되어야해서 Callable을 구현한 Task를 인자로 준다.
