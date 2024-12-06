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
