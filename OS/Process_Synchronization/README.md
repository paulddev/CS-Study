# Process Synchronization

## 데이터의 접근
![23](https://user-images.githubusercontent.com/31722512/158187323-9c2b6cf4-76a5-4351-93b1-3d9d9efb574c.png)

## Race Condition (경쟁 상태)
![24](https://user-images.githubusercontent.com/31722512/158187670-70c6ad34-88ee-463a-8245-70b30f9da3f1.png)

## OS에서 Race condition 는 언제 발생하는가?
1. Kernel 수행 중 인터럽트 발생 시
2. Process 가 system call을 하여 Kernel mode로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor에서 shared memory 내의 kernal data

## Race Condition (1/3)
![25](https://user-images.githubusercontent.com/31722512/158189174-09349528-b4fb-4a83-9084-b712f60097fe.png)

`Kernel 수행하는 도중(Count 값을 증가하는 도중에) 인터럽트가 발생하는 상황` <br>
1의 과정을 수행하고 나서 인터럽트가 발생하여 2의 작업을 멈추고 인터럽트 핸들러가 먼저 처리가 된다. (Count--) <br>
인터럽트 처리가 끝나면 다시 원래로 돌아오게 되는데 1의 과정에서의 결과를 가지고 2와 3을 진행하게 된다.

원래라면 Count를 -1하고 +1하기 때문에 0이 되어야 하지만, 결과적으로 1이 되어버린다.

Count++ 라는 것은
1. load
2. increase
3. store

3가지를 통해서 저장하게 된다. (원자적이지 않기 때문에)

## Race Condition (2/3)
![26](https://user-images.githubusercontent.com/31722512/158189699-320112f8-0b8d-414d-9a4f-fcd8e5649a76.png)

![27](https://user-images.githubusercontent.com/31722512/158189810-3e305451-cdd0-4114-b3c0-9c34df40f397.png)

`본인의 코드를 실행하다가 시스템 콜 요청으로 커널에서 Count 값을 증가시키는 도중에, 할당 만료되어 문맥교환이 발생하는 상황` <br>
B 프로그램으로 넘어가게 되고, B 프로그램에서 코드를 실행하다가 다시 시스템 콜 요청으로 커널에서 Count 값을 +1 증가시킨다. <br>
다시 할당 만료되어 A 프로그램에서 이어서 커널의 Count 값을 증가를 진행한다.

결과적으로는 Count는 2가 되어야 하지만, 증가하는 도중에 문맥 교환이 발생했으므로 그 시점의 Count 값을 가지고 +1을 증가시킨 것이므로 결국 Count 는 총 1만 증가하게 된다.

## Race Condition (3/3)
![28](https://user-images.githubusercontent.com/31722512/158191996-374fe98a-7803-4953-9b54-c7c098c30a1c.png)

`작업 주체가 여러개인 경우(CPU가 여러 개인 경우)` => Data의 Lock을 걸어야한다.
1. 한번에 하나의 CPU만이 커널에 들어가는 방법
2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock (들어간다) / unlock (나온다)를 하는 방법

## Process Synchronization 문제
- 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.
- 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘이 필요하다.
- `Race Condition`
  * 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
  * 데이터의 최종 연산 결과는 그 데이터를 다룬 프로세스에 따라 달라지게 되는 상황

`Race condition`을 막기 위해서는 `concurrent process`는 동기화(Synchronize)되어야 한다.

## Race Condition 예시
![29](https://user-images.githubusercontent.com/31722512/158193037-504d729e-5d8d-4658-86db-648343b78766.png)

## The Critical-Secition Problem
- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 `critical section(공유 데이터를 접근하는 구역)`이 존재
- Problem
  * 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.

![31](https://user-images.githubusercontent.com/31722512/158193662-d1411373-3a82-4550-a89d-634a773c986c.png)

## 프로그램적 해결법의 충족 조건
`Mutual Exclusion(상호배제)`
- 프로세스 Pi가 Critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.

`Progress(진행)`
- 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있다면 critical section에 들어가게 해주어야 한다.

`Bounded Waiting(한정 대기)`
- 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.

- 가정
  * 모든 프로세스의 수행 속도는 0보다 크다.
  * 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

## Initial Attempts to Solve Problem
![32](https://user-images.githubusercontent.com/31722512/158194535-deb4bf4e-fb6c-424e-bc5d-82ea37f6f894.png)





