여러개의 스레드 끼리 하나의 자원을 가지고 누가 먼저 가져갈건지 경쟁하는 것.
일반적으로 경쟁상태에 빠지면 Mutual exclusion, deadlock, starvation와 같은 제어 문제가 생김.

##### Mutual exclusion(상호 배제)
race condition 두개 이상의 프로세스가 하나의 공통된 자원에 동시에 접근하려고 할 때 생기는 거니깐 하나의 프로세스가 접근했거나 사용중이면, 다른 프로세스들은 해당 자원에 접근을 하지 못하게 막거나 해줘야함. 이걸 Mutual exclusion 이라고 함.
> 아예 접근 못하게 자원 관리 해주는 것.
#### deadlock ([교착 상태](https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A9_%EC%83%81%ED%83%9C))
Mutual exclusion를 시행하면 추가적인 제어 문제가 발생하는데 그게 데드락(deadLock)이다.
서로 다른 프로세스 두개가 두개의 자원을 각각 가지고 있을 때 서로 사용하고 있던 그 자원을 자기가 다시 사용하려고 서로가 기다리는 상황.
![[image5.png]]

##### starvation (기아 상태)

