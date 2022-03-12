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
- 자신이 요청한 event가 만족되면 다시 Ready로 올라간다.
- CPU를 주어도 당장 instruction을 수행할 수 없는 상태
- Process 자신이 요청한 event(I/O)가 즉시 만족되지 않아 이를 기다리는 상태
- 디스크에서 file을 읽어와야 하는 경우

`New` : 프로세스가 생성중인 상태 <br>
`Terminated` : 수행(execution)이 끝난 상태

`Suspended(stopped)` : 외부에서 resume 해 주어야 Active 된다.
- 외부적인 이유`(중기 스케줄러) 혹은 유저(사람)`로 프로세스의 수행이 정지된 상태
- 프로세스는 통째로 디스크에 swap out 된다.
- ex) 사용자가 프로그램을 일시 정지시킨 경우
  * 시스템이 여러 이유로 프로세스를 잠지 중단 시킴 -> 메모리에 너무 많은 프로세스가 올라와 있을 때)

## 프로세스의 상태도
![ProcessState](https://user-images.githubusercontent.com/31722512/158030454-a4c1c26e-1cfc-4a7e-911d-f44be7b94d6a.PNG)

`CPU`를 어쩔수 없이 내놓을 수 밖에 없는 경우
- Timer 할당 시간 만료될 때
- I/O 나 event를 기다릴 때
- 종료될 때

![1](https://user-images.githubusercontent.com/31722512/158030630-22f4b8f3-c999-4f50-9d6c-b8a493a36147.png)

## Process Control Block(PCB)
운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
다음의 구성 요소를 가진다. (구조체로 유지)

`OS가 관리상 사용하는 정보`
- Process state, Process ID
- scheduling information, priority

`CPU 수행 관련 하드웨어 값`
- Program counter, register

`메모리 관련`
- Code, Data, Stack의 위치 정보

`파일 관련`
- Open file descriptors...

## 문맥 교환(Context Switch)
CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행한다.
- CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장한다
- CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어온다.

`시스템 콜`이나 `인터럽트` 발생 시 반드시 `Context Switch`가 일어나는 것은 아니다.
![2](https://user-images.githubusercontent.com/31722512/158030938-03cb1fad-e2a7-4ba8-9ac3-80af3654654b.png)

## 프로세스를 스케줄링하기 위한 큐
`Job queue` : 현재 시스템 내에 있는 모든 프로세스의 집합 <br>
`Ready queue` : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합 <br>
`Device queues` : I/O device의 처리를 기다리는 프로세스의 집합 <br>
프로세스들은 각 큐들을 오가며 수행된다!

Ready queue 에 있다가 CPU를 잡게 된다면?
- I/O 요청을 할 때, CPU를 내놓는다.
- 할당 시간이 만료되어 Timer에 의해 CPU를 내놓는다.
- fork a child 할 때, CPU를 내놓는다.
- 종료할 때, CPU를 내놓는다.

## 스케줄러(Scheduler)
`Long-term scheduler(장기 스케줄러 or job scheduler)`
- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- 프로세스에 memory(및 각종 자원)을 주는 문제
- `degree of Multiprogramming` 을 제어 (메모리에 동시에 올라가는 프로그램 개수)
- time sharing system에는 보통 장기 스케줄러가 없다. 무조건 Ready


`Short-term scheduler(단기 스케줄러 or CPU scheduler)`
- 어떤 프로세스를 다음번에 running시킬지 결정
- 프로세스에 CPU를 주는 문제
- 충분히 빨라야 함(millisecond 단위)

`Medium-term scheduler(중기 스케줄러 or Swapper)`
- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
- 프로세스에게서 memory를 뺏는 문제
- `degree of Mulitprogramming` 을 제어

## 스케줄러가 추가된 프로세스 상태도
![3](https://user-images.githubusercontent.com/31722512/158031490-716dbd96-f950-4ffc-885e-53c5de79e52f.png)

![4](https://user-images.githubusercontent.com/31722512/158031565-411ddc48-7e97-4f0a-963c-6bec7271887e.png)










