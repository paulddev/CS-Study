# System Structure & Program Execution
운영체제를 설명하기 앞서서 컴퓨터 하드웨어에 대해서 살펴보자.

## 컴퓨터 시스템 구조
- CPU
- Memory
- I/O device

`CPU` <br>
- mode bit (커널 모드 / 사용자 모드)
- registers (CPU 안의 메모리)
- interrupt line (CPU 에게 알리는 핫라인, 명령어를 수행하고 CPU가 인터럽트 라인을 체크한다.)

`Memory` : CPU의 작업 공간(매 클럭마다 Instruction(명령어)를 읽어서 수행한다.) <br>
- OS (메모리 가장 하단에 깔려 있다.)
- 사용자 프로그램
- Memory Controller (CPU 접근과 DMA 접근을 조율하는 역할)
- DMA Controller (CPU도 접근할 수 있고 DMA도 메모리에 접근할 수 있게 된다. 중간 중간 들어오는 I/O 인터럽트를 대신 처리한다.)
- Timer (특정 프로그램이 CPU 독점을 막기 위해 할당된 시간을 부여, 시간을 넘게되면 CPU에게 인터럽트를 날린다.)

`I/O device` <br>
- device Controller
- local buffer

## mode bit
사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치 필요

`mode bit`을 통해 하드웨어적으로 두 가지 모드의 operation 을 지원한다. <br>
1 사용자 모드 : 사용자 프로그램 수행 <br>
0 커널 모드   : OS 코드 수행

- 보안을 해칠 수 있는 중요한 명령어는 커널 모드에서만 수행이 가능한 `특권 명령`으로 규정한다.
- 인터럽트나 예외 발생시 하드웨어가 mode bit 을 0으로 바꾼다.
- 사용자 프로그램에게 CPU를 넘기기 전에 mode bit 을 1로 셋팅한다.

## Timer (하드웨어)
- 정해진 시간이 흐른 뒤 운영제체에게 제어권이 넘어가도록 인터럽트를 발생시킴
- 타이머는 매 클럭 틱 때마다 1씩 감소
- 타이머 값이 0이 되면 타이머 인터럽트 발생
- CPU를 특정 프로그램이 독점하는 것으로부터 보호

타이머는 time sharing 을 구현하기 위해 널리 이용되며, 현재 시간을 계산하기 위해서도 사용되고 있다.

## Device Controller
`I/O Device Controller`
- 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU
- 제어 정보를 위해 control register, status register 를 가짐
- local buffer 를 가짐 (일종의 data register)

I/O는 실제 device 와 local buffer 사이에서 일어난다. <br>
Device Controller는 I/O 가 끝났을 경우 interrupt 로  CPU에 그 사실을 알린다.

`Device Driver (장치 구동기)` : OS 코드 중 각 장치별 처리 루틴 (Software) <br>
`Device Controller (장치제어기)` : 각 장치를 통제하는 일종의 작은 CPU (Hardware)

## 인터럽트 (Interrupt)
인터럽트 당한 시점의 `레지스터`와 `program counter(PC)`를 save 한 후, CPU의 제어를 `인터럽트 처리 루틴`에 넘긴다.

인터럽트 (넓은 의미)
- `Interrupt(하드웨어 인터럽트)` : 하드웨어가 발생시킨 인터럽트 (Timer 시간이 다된 경우)
- `Trap(소프트웨어 인터럽트)`
  1. Exception : 프로그램이 오류를 범한 경우
  2. System call : 프로그램이 커널 함수를 호출하는 경우

인터럽트 관련 용어
- `인터럽트 벡터` : 해당 인터럽트의 처리 루틴 주소를 가지고 있음 (어디에 있는 함수를 실행해야 하는지를 가리키는 주소를 저장하고 있다.)
- `인터럽트 처리 루틴(Interrupt Service Routine, 인터럽트 핸들러)` : 해당 인터럽트를 처리하는 커널 함수 (실제로 인터럽트를 처리하는 코드)

## 입출력(I/O)의 수행
모든 입출력 명령은 `특권 명령` <br>
사용자 프로그램은 어떻게 I/O를 하는가?
- 시스템 콜(System call) : 사용자 프로그램은 운영체제에게 I/O 요청한다.
- `trap`을 사용하여 `인터펍트 벡터`의 특정 위치로 이동
- 제어권이 인터럽트 벡터가 가리키는 `인터럽트 서비스 루틴`으로 이동
- 올바른 I/O 요청인지 확인 후 I/O 수행
- I/O 완료 시 제어권을 시스템 콜 다음 명령으로 옮김

`시스템 콜` : 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것 <br>
메모리 안에서 사용자 프로그램 A가 커널 OS에 System call을 요청

## 동기식 입출력과 비동기식 입출력
`동기식 입출력(Synchronous I/O)`
- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에게 넘어감
- 구현 방법 1
  * I/O가 끝날 때까지 CPU를 낭비시킴
  * 매시점 하나의 I/O만 일어날 수 있음
- 구현 방법 2
  * I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
  * I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
  * 다른 프로그램에게 CPU를 줌

`비동기식 입출력(Asynchronous I/O)`
- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에게 즉시 넘어감

두 경우 모두 I/O의 완료는 `인터럽트`로 알려준다.

## DMA (Direct Memory Access)
- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU 중재 없이 device Controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
- 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴

## 서로 다른 입출력 명령어
- I/O 를 수행하는 special instruction에 의해 (일반적인 경우)
- Memory Mapped I/O에 의해 (I/O 장치도 메모리 주소에 연장 주소로 잡는 경우)

## 저장장치 계층 구조
`speed`, `Cost`, `Volatility`

1. Registers    (CPU가 접근하는 공간)
2. Cache memory (CPU가 접근하는 공간)
3. Main memory  (CPU가 접근하는 공간)
4. Magnetic Disk
5. Optical Disk
6. Magnetic Tape

1에 가까울수록 speed 가 빠르고, 비용이 비싸다.<br>
6에 가까울수록 speed 가 느리고, 비용이 싸다.

중간 중간 속도를 완충하기 위해서 저장장치가 여러 개 생기게 되었다.

`Caching` : 어쩔 수 없이 한 번은 읽어야 하지만, 다음에 다시 읽으려고 할때 캐시에 저장되어 있다면 바로 사용할 수 있다.

## 프로그램의 실행 (메모리 Load)
어떤 프로그램 A을 실행시키면, `File System`에서 가져와 A 프로그램만의 0번지로부터 시작되는 (Stack, Data, Code) 프로세스 A의 Address Space가 생긴다. <br>
이러한 독자적인 주소 공간(Virtual Memory)이 생기게 되고 이것을 `물리적인 메모리 공간(메인 메모리)`에 Address Translation 을 통해 <br>
물리적인 메모리에 둘지 혹은 Swap area (디스크 공간)에 둘지 결정하게 된다. 즉시 사용된다면 메인 메모리에 두겠지만, 사용하지 않는다면 disk 에 대기 시켰다가, 사용할 때 끌어서 올리는<br>
형태로 사용된다. (그런데 메모리에 올릴 때 프로세스의 모든 것을 올리는 것이 아니라 당장 필요한 부분만 올리게 된다.) => 메모리 공간은 한정되어있기 때문에

`code` : 프로그램의 기계어 코드 <br>
`data` : 전역 변수 <br>
`stack` : 임시 공간 <br>


## 커널 주소 공간의 내용
`code`
- 커널 코드 영역
- 시스템 콜, 인터럽트 처리 코드
- 자원 관리를 위한 코드
- 편리한 서비스 제공을 위한 코드

`Data`
- 운영체제가 사용하는 자료구조
- CPU, MEMORY, DISK
- PCB(Process A), PCB(Process B)

`Stack`
- Process A의 커널 스택
- Process B의 커널 스택

## 사용자 프로그램이 사용하는 함수
`함수`
- 사용자 정의 함수
  * 자신의 프로그램에서 정의한 함수

- 라이브러리 함수
  * 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
  * 자신의 프로그램의 실행 파일에 포함되어 있다.

`커널 함수`
- 운영체제 프로그램의 함수
- 커널 함수의 호출 => 시스템 콜


## 프로그램의 실행
1. A의 주소공간 : 유저모드
2. 시스템 콜 요청
3. Kernel mode (인터럽트 벡터를 통해서 주소를 확인하고 그 주소를 찾아가 인터럽트 서비스 루틴 실행)
4. 시스템 콜이 끝날 때, A의 주소공간으로 이동하고 이어서 실행
5. 시스템 콜 요청
6. Kernel mode
7. 프로그램 종료
