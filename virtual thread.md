## Virtual Thread

#### Virtual Thread 관련 공부한 내용을 정리해보자

<br>

#### Virtual Thread 란?
- 2018년 Java Project Loom 으로 시작된 자바의 경량 스레드 모델
- 2023년 JDK21 에 정식 feature 로 추가

<br>

#### 장점

- 장점 요약
  - 스레드 생성 및 스케줄링 비용이 기존 스레드보다 저렴
  - 스레드 스케줄링을 통해 Nonblocking I/O 지원
  - 기존 스레드를 상속하여 코드 호환

- 스레드 생성 비용 관련
  - 기존 자바 스레드는 생성 비용이 크다
    - 스레드 풀의 존재 이유
    - 사용 메모리 크기가 크다 (최대 2MB 정도)
    - OS에 의해 스케줄링 (System Call Overhead 발생)
  - Virtual Thread 는 생성 비용이 작다
    - 스레드 풀 개념이 아니다.
    - 사용 메모리 크기가 작도록 설계되었다. (약 50KB)
    - OS가 아닌 JVM내 스케줄링
      - 컨텍스트 스위칭 비용이 작다
    - 생성 속도와 스케줄링이 빠르다

- Nonblocking I/O 지원
  - Nonblocking I/O 는 blocking time 을 획기적으로 줄여준다.
  - ex) 웹플럭스 & netty
    - 스프링 웹플럭스는 네티의 이벤트 루프를 활용해서 blocking i/o 의 시간을 줄인다
    - 사양대비 높은 퍼포먼스 가능
  - 버추얼 스레드의 논블락킹 I/O
    - JVM 스레드 스케줄링
    - Continuation 개념 활용

- 기존 스레드 상속
  - 버추얼 스레드는 Thread 와 완벽하게 호환된다.
  - ExecutorService 도 Virtual thread 의 executorService 로 생성 가능

<br>

#### 동작 원리

- 자바의 일반 스레드 특징
  - 플랫폼 스레드
  - OS에 의해 스케줄링
  - 커널 스레드(OS)와 플랫폼 스레드(JVM)는 1:1 매핑
  - 작업 단위 Runnable
 
- Virtual Thread
  - 가상 스레드
  - JVM에 의해 스케줄링
  - JVM에 존재하는 캐리어 스레드와 1:N 매핑
  - 작업 단위 Continuation

- JVM 에 의한 스케줄링
  - ForkJoinPool 을 스케줄러로 사용
  - 프로세서 수의 캐리어 스레드를 워커 스레드로 사용
  - Work Stealing 방식으로 작업 수행
  - JVM 스케줄링 이유
    - 일반 스레드는 생성 및 스케줄링 시 커널 영역 접근
    - 버추얼 스레드는 커널 영역 접근 없이 단순 자바 객체 생성
    - 즉 버추얼 스레드는 생성/스케줄링 시 시스템 콜이 발생하지 않음
   
- Continuation 작업 단위
  - ex) 코루틴: suspend 작업 방식 (중단 지점 저장 및 재수행)
  - continuation 이란
    - 실행가능한 작업흐름
    - 중단 가능
    - 중단 지점으로부터 재실행 가능
  - 중단 지점 정보를 heap 에 보관, 수행 시에는 stack 에서 고나리
  - Continuation 객체로 테스트를 해보자
  - 버추얼 스레드는 필드로 continuation 을 가지고 있다
  - 버추얼 스레드가 실행되면 스케줄러의 캐리어 스레드 work queue 에 작업이 제출된다. (run continuation 람다)
  - 버추얼 스레드 yield 는 park 메서드로 수행
    - 자바 유틸의 LockSupport 의 park() 메서드를 호출하면 된다
    - 버추얼 스레드인 경우 continuation 의 yield 를 호출한다.
    - 버추얼 스레드가 아닌 경우(일반 스레드) unsafe 의 park 메서드 호출?
      - 네이티브 메서드로 커널 스레드를 블락킹한다.
      - 예시)
        - Thread.sleep()
        - Mono.block()
        - CompletableFuture.get()
    - 위 처럼 LockSupport 의 park 에 스레드 분기가 생김
  - continuation 이 work queue 에서 yield 되면 stack 에서 heap 으로 옮겨감 (work queue 에서 제거)
  - thread 는 작업 중단을 위해 커널 스레드를 중단
  - virtual thread 는 작업 중단을 위해 continuation yield
  - 작업이 block 되어도 실제 스레드는 중단되지않고 다른 작업 처리
  - 커널 스레드 중단이 없으므로 시스템 콜 x -> 컨텍스트 스위치 비용이 낮음

<br>

#### 성능

- 일반 쓰레드 방식에 비해
  - I/O Bound 작업은 더 높은 처리량
  - CPU Bound 작업은 더 낮은 처리량
  - 일반 스레드 서버는 특정 vuser 수 부터 장애 발생
- webflux 와 비교
  - 특정 vuser 수 이상 웹플럭스 처리량 급감
  - 컨텍스트 스위치 비용으로 인한 처리량 저하
  - 극한 상황의 테스트 환경의 경우에 차이가 많이나고 실제로 이정도 차이는 경험하기 힘들 듯
- I/O Bound 작업 효율 및 제한된 사양에서 최대 처리량 경험 가능

<br>

#### 주의 사항
- 캐리어 스레드 blocking (pin)
  - 캐리어 스레드를 block 하면 버추얼 스레드 활용 불가
    - synchronized / parallelStream 금지!
  - vm 옵션으로 감지 가능
    - -Djdk.tracePinnedThreads=short, full
  - 버추얼 스레드에서는 ReentrantLock 을 사용하도록 변경해야함
    - ex) 몽고db 같은 경우 신규 버전에서 지원, mysql 은 아직 이슈
  - 병목 가능성이 존재
  - 사용 라이브러리 release 점검
  - 변경 가능하면 java.util 의 ReentrantLock 을 사용하도록 변경 필요
- No pooling (풀링 금지)
  - 생성 비용이 저렴하기 때문
  - 사용할 때마다 생성 하도록 하자
  - 사용완료 후 GC
  - 스레드 무제한 생성 가능
- CPU bound task 금지
  - 결국 캐리어 스레드 위에서 동작하므로 성능 낭비
  - nonblocking 의 장점을 활용하지 못함
- 경량 스레드
  - 수백만개의 스레드 생성 컨셉
  - Thread Local 을 최대한 가볍게 유지해야함
  - 쉽게 생성 및 소멸
  - JDK21에 프리뷰 피쳐로 ScopedValue 등장
    - ThreadLocal 대체 개념
- Virtual thread 는 배압조절 기능이 없다
  - 기존 스레드의 경우 쓰로틀링 기능이 강제적으로 적용된 부분이 있다.
  - 유한 리소스의 경우 배압을 조절하도록 설정 (db 커넥션, 파일)  
  - 충분한 성능테스트 필요
    - 하드웨어 성능 부족 문제 발생 가능

<br>


#### reference)
- 우아한형제들 테크세미나 : https://www.youtube.com/watch?v=BZMZIM-n4C0
- kakao tech meet : https://tech.kakao.com/2023/12/22/techmeet-virtualthread/
- 추가 예정
