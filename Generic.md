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
    
    - Key와 Value의 데이터 타입을 외부에서 클래스 생성 시에 정하고 싶을 때 위와 같이 인터페이스와 클래스를 생성하여 구현 가능
        - 객체 생성 시에 구체적인 타입을 명시한다.

- 타입 파라미터로 명시할 수 있는것은 참조 타입(Reference Type) 밖에 없다.
    - int, double, char 같은 기본형(primitive type)은 올 수 없음
    - Wrapper 클래스



<br>

reference)
https://st-lab.tistory.com/153
https://velog.io/@zayson/%EB%B0%B1%EA%B8%B0%EC%84%A0%EB%8B%98%EA%B3%BC-%ED%95%A8%EA%BB%98%ED%95%98%EB%8A%94-Live-Study-14%EC%A3%BC%EC%B0%A8-%EC%A0%9C%EB%84%88%EB%A6%AD
