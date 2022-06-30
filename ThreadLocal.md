# ThreadLocal

#### ThreadLocal 이란?

- 멀티 스레드환경에서 각 스레드마다 독립적인 변수를 가지고 ```get()```, ```set()``` 메서드를 통해 값을 조작할 수 있다.

- 스레드가 종료되기전까지 값을 사용할 수 있다.

- 사용이 끝난 후, ```remove()``` 하지 않으면 스레드를 재사용 했을 때, 원하지 않는 결과가 나올 수 있다.

#### 테스트해본 코드

- 먼저 ThreadLocal 에 set할 도메인 객체를 생성
  ```java
  public class Domain {
    private String strValue;
    private int intValue;

    public int getIntValue() {
        return intValue;
    }

    public void setIntValue(int intValue) {
        this.intValue = intValue;
    }

    public String getStrValue() {
        return strValue;
    }

    public void setStrValue(String strValue) {
        this.strValue = strValue;
    }
  }
  ```
  
- ThreadLocal을 관리할 클래스 생성
  ```java
  public class DomainThreadLocal {
    private static final ThreadLocal<Domain> domainThreadLocal = new ThreadLocal<>();

    public static void setDomainThreadLocal(Domain domain){
        domainThreadLocal.set(domain);
    }

    public static String getDomainStrValThreadLocal(){
        return domainThreadLocal.get().getStrValue();
    }

    public static int getDomainIntValThreadLocal(){
        return domainThreadLocal.get().getIntValue();
    }

    public static void clearDomainThreadLocal(){
        domainThreadLocal.remove();
    }
  }
  ```
  
- 테스트 코드 및 결과
  ```java
  public static void main(String[] args) {
        Thread testThread1 = new Thread(() -> {
            Domain testDomain1 = new Domain();

            testDomain1.setStrValue("This Is Test");
            testDomain1.setIntValue(1);

            DomainThreadLocal.setDomainThreadLocal(testDomain1);

            System.out.println("testDomain1 str value : " + DomainThreadLocal.getDomainStrValThreadLocal());
            System.out.println("testDomain1 int value : " + DomainThreadLocal.getDomainIntValThreadLocal());
            
            DomainThreadLocal.clearDomainThreadLocal();
        });


        Thread testThread2 = new Thread(() -> {
            Domain testDomain2 = new Domain();

            testDomain2.setStrValue("Test Start");
            
            DomainThreadLocal.setDomainThreadLocal(testDomain2);

            System.out.println("testDomain2 str value : " + DomainThreadLocal.getDomainStrValThreadLocal());
            System.out.println("testDomain2 int value : " + DomainThreadLocal.getDomainIntValThreadLocal());
            
            DomainThreadLocal.clearDomainThreadLocal();
        });

        testThread1.run();
        testThread2.run();

        testThread1.run();
    }
    ```
    
    - 결과
    ```
    testDomain1 str value : This Is Test
    testDomain1 int value : 1
    testDomain2 str value : Test Start
    testDomain2 int value : 0
    testDomain1 str value : This Is Test
    testDomain1 int value : 1
    ```
    
    - 출력 결과에서 testThread2 쓰레드의 ThreadLocal 값을 출력해본 뒤에 다시 testThread1 쓰레드를 실행해봤을 때, testThread2 쓰레드와 개별적으로 값을 가지는걸 확인할 수 있다.
    
    - 마지막 라인 ```testThread1.run();``` 밑에 DomainThreadLocal 값을 출력해보면 ThreadLocal의 ```remove()``` 메서드를 호출 했기 때문에 오류가 발생하는 걸 확인할 수 있다.
       
 
 #### 실무에서 사용한 이유
 
 - 각 사용자들의 요청이 수행될 때, 인터셉터에서 해당 요청에 대한 정책(DB에 저장된 값, 실시간으로 수정될 수 있음)에 따라 검증을 수행하는 프로세스가 있는데..
    - WAS에서 static하게 가지고있는 ConcurrentHashMap 에 정책 값을 가지고 있고, 해당 Map의 값은 정책을 관리하는 화면에서 정책을 수정하면 실시간으로 변경된다.
    - 이 ConcurrentHashMap 을 관리하는 클래스에서는 volatile, synchronized 등 동기화 관리를 위한 키워드로 데이터를 관리하고 있기 때문에,
        각 사용자들의 요청이 수행될 때 마다, 인터셉터에서 검증을 수행하는 프로세스안에서 지속적으로 이 map에 접근하게되면 쓰레드 경합 문제가 발생할 수 있다.
    - 인터셉터에서 검증을 수행하기 전 최초 한번 Map에서 정책 값을 가져와서 해당 쓰레드의 ThreadLocal에 정책 값을 세팅하고,
        각 쓰레드의 검증을 수행하는 프로세스 안에서는 쓰레드 경합 없이 ThreadLocal에서 값을 꺼내어 정책 값을 사용할 수 있다.
