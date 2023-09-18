여러개의 스레드 끼리 하나의 자원을 가지고 누가 먼저 가져갈건지 경쟁하는 것.
일반적으로 경쟁상태에 빠지면 [[Mutual exclusion]], [[deadlock]], [[starvation]]와 같은 제어 문제가 생김.

이런 race condition을 예방할 수 있는게 [[Semaphore]](세마포어)와 Mutex(뮤텍스, [[Mutual exclusion]]의 약어)가 있다.

race condition 발생 조건.
1. 2가지 처리가 변수를 공유  ([[Actor 모델]])
2. 적어도 하나의 처리가 그 변수를 변경할 수 있음. ([[불변]])
3. 한쪽 처리가 한 단락 마무리 되기 전, 다른 쪽의 처리가 끼어들 가능성이 있음.

방지하려면 위 조건중 하나만이라도 해결하면 됨.

3번의 경우
하나의 프로세스가 처리를 하는 과정에 있으면, 외부에서 끼어들지 않는 기법들이 생겨남.
그 중 하나가 서로 협력하는 스레드가 생겨남.
스레드는 기본적으로 preemptive(선점형)하므로 non-preemptive(비선점형)한 스레드를 만들자는 개념.
fiber, coroutine, Green thread와 같은 기법을 사용하는 Ruby의 Fiber Class, Python이나 JS의 Generator가 그 예임.
또 다른 하나는 처리하고 있는 스레드가 사용중인 자원에 접근하지 말라는 표식을 사용함. (Lock, [[Mutex]], [[Semaphore]])
이 해결 방법은 deadlock에 대한 이해도가 있는 상태에서 사용해야만 함.
그래서 이를 약간 편하게 해주는 DB의 Transaction 기법을 적용한 [[Transactional memory]] 기법이 탄생.
해보고 실패하면 다시 처음으로 돌아가고 성공하면 변경을 공유하는 것.
이는 락을 걸지 않아도 되서 변행처리가 가능하지만, 고쳐 쓰는 작업은 자원이 많이 소요된다.