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

## Timer
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
- `Interrupt(하드웨어 인터럽트)` : 하드웨어가 발생시킨 인터럽트
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


