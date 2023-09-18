> [참고 블로그](https://homoefficio.github.io/2020/12/11/Java-Concurrency-Evolution/)

동시성 처리 관련 여러 방식들을 나열하고 [[Project Loom (미작성)]] 코드도 일부 작성할 예정.

> [블로그의 참고 원문](https://dzone.com/articles/java-concurrency-evolution)

자바 1.1에서 가상 스레드를 지원했다가 폐기되고 OS 네이티브 스레드를 지원했다가 프로그래밍 스레드 흐름이 가상 스레드 흐름으로 넘어가면서 [[Project Loom (미작성)]]이 도입되면서 전환된거지.

### 자바 스레드
- JVM 스레드 === OS 스레드. OS 스레드를 커스텀한게 JVM 스레드.
- OS 스레드는 겁나 느림. OS는 JVM 내부에 대해 개입 안함.
- 스레드간 데이터 교신은 커널을 거쳐야해서 자원이 많이 소모됨. (느림)
- OS의 [[continuation]] 구현체는 자바 콜 스택 뿐만 아니라 네이티브 콜 스택도 포함하며, 자원을 많이 소모한다.
- OS 스레드 갯수 <= CPU 코어 갯수
- 스레드에 사용되는 스택 메모리는 OS에 의해 힙 외의 영역에 마련된다.
![[image1.png]]

### 태스크
스레드에서 동작하는 단위.

### 네이티브 멀티 스레딩
- CPU코어나 메모리의 자원을 효율적으로 사용할 수 있을 것인가.
- 세밀한 스레드를 조정 및 관리할 수 있을 것인가.
- 제어 흐름과 컨텍스트 유실 (스택 트레이스가 스레드에 바운드 되므로 (한정되므로) 문제가 있는 스택 트레이스를 볼 수 없다.)
- 실행 동기화
- 디버깅과 테스트 어렵지..

### ExcutorService
스레트 풀을 통해서 스레스 생성 부담을 줄여줌.
태스트는 submit 되서 큐에 들어가고, 작업 가능한 스레드가 큐에서 태스크를 가져다가 실행함.
![[image2.jpg]]

- 아직도 JVM 스레드 갯수는 OS 스레드 갯수에 영향을 받고 있음
- 스레드 풀에서 스레드를 하나 가져가면 동작하지 않아도 다른곳에서 가져갈 수 없음. 
- 결과 값이 Feture가 반환되지만 조립은 할 수 없다 ? (get()을 써서 대기해야함. 각각의 스레드가 get()을 쓰면 각 스레드가 끝날때까지 기다려주는데 이것들을 하나로 취합하기 애매함 ?)

```java
private static final CountDownLatch latch = new CountDownLatch(2000);
...
latch.await(); // async/await 에서 await가 맞음.
// 이게 async/await랑 비슷한 개념으로 동작하게 해줌.
```

### [[Fork&Join 프레임워크]]
Java7에서 ExecutorService 기반으로 작성된 프레임워크.
재귀적으로 더 작은 크기로 쪼갤 수 있는 태스크를 효율적으로 처리하기 위해서 만들어짐.
ExecutorService를 대체는 못하고 병행해서 사용됨.

ExecutorService와 다른점은 작업 빼가기(work-stealing)임.
![[image3.png]]

특정 스레드 풀에 가득차있으면 다른 스레드가 가득차 있는 큐에서 태스크를 가져와서 처리함.

이전 방식과 다른 차이점들을 알아보기에는 참고할만한 소스.
> [깃허브](https://github.com/bejancsaba/java-concurrency-evolution/blob/main/src/test/java/com/concurrency/evolution/C6_ForkJoinFramework.java)

이전과는 조금 다르게 데드락이 생기는 빈도가 적다. 아예 발생하지 않는건 아닌데, 발생한다면 원인은 어떻게 태스크를 가장 작은단위로 분할될 수 있는지가 핵심이다.

깃허브쪽에 있는 예제 소스는 재귀적 분할을 할정도로 기능이 분할되어있지 않고, 성능도 좋지 않아서 ExecutorService보다 성능은 떨어지는데 데드락은 생기지 않으므로 안정성은 더 좋다고 할 수 있다.

### CompletableFuture
자바 8에 도입된 CompletableFuture는 Fork&Join 프레임워크 기반으로 만들어짐.
연산 결과를 모아서(combine) 처리할 수 있는 메서드가 하나도 없었고, 에러 처리를 위한 방법도 없었던 Feature 인터페이스 도입 이후 진화가 CompletableFuture 에서 이루어졌다.

개선점.
- 더 개선된 함수형 프로그래밍 스타일 도입.
- 로직을 조립(compose)하고, 결과를 모아서 처리(combine)하고, 비동기 연산 과정을 실행하고, 에러를 처리할 수 있는 50여개의 메서드 추가.
- CompletableFuture 의 평문형 API([[fluent API]]) 대부분은 뒤에 Async 접미사가 붙은 것과 붙지 않은 것, 이렇게 2가지씩 짝지어져 있다. Async 접미사가 붙은 메서드는 해당 연산을 다른 스레드에서 실행하려고 할 때 사용된다.

<<<<<<< HEAD
CompletableFuture는 자바에 몇안되는 모나드([[monad]])이다.
=======
### Reactive
리액티브 아키텍처 !== [[리액티브 프로그래밍 (미작성)]]
CompletableFuture가 발전해서 리액티브 방식이 됬다고 볼수있는데 그 이상임.
비동기 데이터 스트림을 처리하고 에러 처리와 배압(backpressure)를 확고하게 지원하는 리액티브 프로그래밍만 다룸.
리액티브 프로그래밍의 주 목표는 `프로그램 구조를 비동기 이벤트 스트림으로 재구성 하는 것`, 스레드 관리는 라이브러리&프레임워크에 위임한다.

주목할 포인트
- 데이터 발생 - Observable, 데이터 소비 - Observer, 스레드 관리 - Scheduler의 삼위 일체.
- 리액티브 방식을 도입시 프로그램 흐름 전부다 리액티브 방식으로 같이 바뀌어야 한다는 점에서 전염성이 강하다. 일부 블로킹 코드가 있을 경우 장점이 전혀 발휘되지 못함.
- 구현체가 다양함. [[RxJava]]가 있었다가 스프링의 [[Reactor]]가 대세래... 액터보델을 구현하는 Akka 프레임워크는 RxJava나 Reactor보다 더 급진적인 리액티브 프로그래밍을 적용하고 있음..

> 이거 되게 빡쌘데?????

#### [[Project Loom (미작성)]] (프로젝트 룸)
지금 JDK 21에 정식 지원 되는 듯...
프로젝트 룸은 가상 스레드와 관련된 기능들이 포함되어 있음.
그렇게 될 경우 OS 스레드와 JVM 스레드는 1=1이라는 오랜 등식이 성립되지 않게 됨.

주목 포인트
- 메타데이터, 스택 메모리, 컨텍스트 스위치 시간이 네이티브 OS 스레드의 수 분의 일밖에 되지 않을만큼 가볍다 ( 가볍다는 소리 )
- 지원이 불안정함. (JDK 21에서나 릴리스될 듯)

[대충 샘플 코드](https://github.com/bejancsaba/java-concurrency-evolution-loom/blob/main/src/main/java/com/concurrency/Main.java)

