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
- Memory Controller
- DMA Controller
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
