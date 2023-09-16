여러개의 스레드 끼리 하나의 자원을 가지고 누가 먼저 가져갈건지 경쟁하는 것.
일반적으로 경쟁상태에 빠지면 [[Mutual exclusion]], [[deadlock]], [[starvation]]와 같은 제어 문제가 생김.

이런 race condition을 예방할 수 있는게 [[Semaphore]](세마포어)와 Mutex(뮤텍스, [[Mutual exclusion]]의 약어)가 있다.
