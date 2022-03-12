# 프로세스의 개념
프로세스는 실행중인 프로그램을 의미한다.

`프로스세의 문맥(Context)`
- CPU 수행 상태를 나타내는 하드웨어 문맥을 말함
  * Program Counter(PC) - 프로세스 주소공간의 Code 영역에 하나 하나씩 실행할 수 있도록 현재 실행할 코드를 가리킨다.
  * 각종 Register
- 프로세스의 주소 공간
  * Code, Data, Stack
- 프로세스 관련 커널 자료구조
  * PCB(Process Control Block)
  * Kernel Stack (시스템 콜 같이 커널의 코드를 실행할 때 사용되는 공간, 커널도 함수로 구성되어 있다.) <br>
    커널의 공간은 공유하는 영역이기 때문에 따로 프로세스마다 사용될 커널 스택을 만들어 꼬이지 않게 운용한다.
    
## 프로세스의 상태 (Process State)
프로세스는 상태가 변경되며 수행된다.

`Running` : CPU를 잡고 instruction을 수행중인 상태 <br>
`Ready` : CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족한 상태) <br>
`Blocked(wait, sleep)`
- CPU를 주어도 당장 instruction을 수행할 수 없는 상태
- Process 자신이 요청한 event(I/O)가 즉시 만족되지 않아 이를 기다리는 상태
- 디스크에서 file을 읽어와야 하는 경우

`New` : 프로세스가 생성중인 상태 <br>
`Terminated` : 수행(execution)이 끝난 상태
