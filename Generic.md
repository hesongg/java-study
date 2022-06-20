# 제네릭(Generic)

<br>

### 제네릭 이란?

- 데이터의 타입을 일반화하는 것을 의미
- 자바에서 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에서 사용하는 것으로,
    컴파일 과정에서 타입을 체크해주는 기능
- 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 안정성이 높아지고,
    캐스팅이 용이하다는 장점을 가진다.
- 클래스 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법을 의미

<br>

### 제네릭의 장점

- 제네릭을 사용하면 잘못된 타입이 들어올 수 있는 것을 컴파일 단계에서 방지 가능
- 클래스 외부에서 타입을 지정해주기 때문에 따로 타입을 체크하고 변환해줄 필요가 없다. 
- 비슷한 기능을 지원하는 경우 코드의 재사용성이 높아진다.

<br>

### Generic(제네릭) 사용방법

- 보통 제네릭은 아래 표의 타입들로 쓰인다. (특별한 제한은 없음)

    |타입|설명|
    |----|----|
    |\<T>|Type|
    |\<E>|Element|
    |\<K>|Key|
    |\<V>|Value|
    |\<N>|Number|

- HashMap과 같이 Key로 Value를 얻는 목적의 인터페이스와 클래스를 구현한다고 가정하자.
    ```java
    public interface KeyValue <K, V> {
        public V get(K key);
    }

    public static class keyValueClass<K, V> implements KeyValue <K, V> {
        @Override
        public V get(K key) {
            V value = ...; //key에 매핑되는 값을 정한다
            return value;
        }
    }

    public static void main(String[] args) {
        KeyValue<String, Integer> kv1 = new keyValueClass<String, Integer>();
        KeyValue<Double, Boolean> kv2 = new keyValueClass<Double, Boolean();
    }
    ```
    
    - ```KeyValue<String, Integer> kv1 = new keyValueClass<String, Integer>();``` 의 경우
        - 해당 클래스의 <K, V> 제네릭 타입은 <String, Integer> 으로 모두 변환된다.
    
    - Key와 Value의 데이터 타입을 외부에서 클래스 생성 시에 정하고 싶을 때 위와 같이 인터페이스와 클래스를 생성하여 구현 가능
        - 객체 생성 시에 구체적인 타입을 명시한다.

- 타입 파라미터로 명시할 수 있는것은 참조 타입(Reference Type) 밖에 없다.
    - int, double, char 같은 기본형(primitive type)은 올 수 없음
    - Wrapper 클래스

- 제네릭 메서드
    - 제네릭 클래스와 별도로 메서드에 한정한 제네릭도 사용 가능하다.
        ```java
        public static class TestClass<E>{
            private E element;

            void setElement(E element) {
                this.element = element;
            }

            E getElement(){
                return element;
            }
            
            //제네릭 메서드 선언
            <T> T genericGetMethod(T t){
                return t;
            }
        }

        public static void main(String[] args) {
            TestClass<String> tc = new TestClass<>();

            tc.setElement("element");

            // 출력 결과 = java.lang.String
            System.out.println(tc.getElement().getClass().getName());

            // 출력 결과 = java.lang.Integer
            System.out.println(tc.genericGetMethod(123).getClass().getName());
        }
        ```
        
        - 타입 파라미터(Type Parameter) ```<T>``` 를 추가해서 클래스의 제네릭타입 ```<E>```와 별도로 제네릭 메서드를 만들었다.


    - 위와 같이 메서드 앞에 타입 파라미터를 설정해주는 방식이 왜 필요할까?
        - 정적 메서드로 선언할 때 필요하기 때문
        - 제네릭은 유형을 외부에서 지정한다. 즉, 해당 클래스 객체를 인스턴스화 할 때 <> 괄호 사이에 지정해주는 타입 파라미터로 타입이 정해진다.
        - 하지만 ```static```이란 기본적으로 프로그램 실행 시 메모리에 이미 올라가 있다.
        - 그렇기 때문에 제네릭이 사용되는 메서드를 정적(static)메서드로 두고 싶은 경우 제네릭 클래스와 별도로 독립적인 제네릭이 사용되어야한다.
            ```java
            public static class TestClass<E>{
                //오류 발생 !
                static E genericGetMethod(E ele){
                    return ele;
                }
                
                //정상 1
                static <E> E genericGetMethod(E ele){
                    return ele;
                }
                
                //정상 2
                static <T> T genericGetMethod(T ele){
                    return ele;
                }
            }
            ```

<br>

### Generic과 와일드 카드

- ```extends```, ```super```, ```? (와일드카드)```

<br>

#### 제네릭 바운디드 타입

- 제네릭에서 사용되는 타입을 특정 범위 내로 좁혀서 제한할 수 있다.
    ```java
    <K extends T> // 타입파라미터 K는 T와 T의 자손 타입만 가능
    <K super T> // 타입파라미터 K는 T와 T의 부모(조상) 타입만 가능
    
    <? extends T>   // T와 T의 자손 타입만 가능
    <? super T> // T와 T의 부모(조상) 타입만 가능
    <?> // 모든 타입 가능. <? extends Object> 와 같은 의미
    ```
    
- ```extends T``` : 상한 경계
- ```? super T``` : 하한 경계
- ```<?>``` : 와일드 카드(Wild Card)

- 바운디드 타입 예시
    ```java
    public interface Num <N extends Number> {
        N getThis();
    }

    public static class NumberClass<E extends Number> implements Num{
        E number;

        @Override
        public E getThis() {
            return number;
        }
    }


    public static void main(String[] args) {
        //Integer는 Number의 자손 타입이 맞으므로 정상
        NumberClass i = new NumberClass<Integer>();
        i.number = 123;
        System.out.println(i.getThis());
        
        //String은 Number의 자손 타입이 아니기때문에 오류가 발생한다 !
        NumberClass s = new NumberClass<String>();
    }
    ```

- ```K extends T``` 와 ```? extends T``` 의 차이
    - "유형 경계를 지정" 하는 것은 같으나 경계가 지정되고 K는 특정 타입으로 지정이되지만, ?는 타입이 지정되지 않는다는 의미이다.(타입 참조 불가)
    - 특정 타입의 데이터를 조작하고자 할 경우에는 K 같이 특정 제네릭 인수로 지정을 해주어야 한다.
    
    - 와일드 카드 예시
        ```java
        public class SortClass <E extends Comparable<? super E> { ... }
        ```
        - 여기서 ```<E extends Comparable<? super E>```의 뜻은 ```E 자기 자신 및 조상 타입과 비교할 수 있는 E``` 이다.
    



<br>


#### 참고 링크
- https://st-lab.tistory.com/153
- https://velog.io/@zayson/%EB%B0%B1%EA%B8%B0%EC%84%A0%EB%8B%98%EA%B3%BC-%ED%95%A8%EA%BB%98%ED%95%98%EB%8A%94-Live-Study-14%EC%A3%BC%EC%B0%A8-%EC%A0%9C%EB%84%88%EB%A6%AD
