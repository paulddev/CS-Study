# DeadLock (교착상태)
![image](https://user-images.githubusercontent.com/31722512/158623918-6b779dc0-e963-4c00-b257-44f50de48ae0.png)

## The Deadlock Problem
`Deadlock`
- 일련의 프로세스들이 서로 가진 자원을 기다리며 block된 상태

`Resource`
- 하드웨어, 소프트웨어 등을 포함하는 개념
- I/O Device, CPU cycle, memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차
  * Request, Allocate, Use, Release

Deadlock Example 1
- 시스템에 2개의 tape drive가 있다.
- 프로세스 p1과 p2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

Deadlock Example 2
- Binary semaphores A and B
- p0은 P(A); P(B);
- p1은 P(B); P(A);

## Deadlock 발생의 4가지 조건
핵심은 4가지 조건 중 어느 하나를 만족하지 못하면 `데드락`이 발생하지 않는다.

`Mutual Exclusion(상호배제) : 일단 내가 자원을 가지고 있으면 독점적으로 써야한다.`
- 매 순간 하나의 프로세스만이 자원을 사용할 수 있다.

`No Preemption(비선점)`
- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않는다.

`Hold and wait(보유 대기)`
- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있는 상태

`Circular wait(순환대기)`
- 자원을 기다리는 프로세스간에 사이클이 형성되어야 함.
- 프로세스 P0, P1, P2, ... , Pn이 있을 때
- P0은 P1이 가진 자원을 기다림
- P1은 P2가 가진 자원을 기다림
- Pn-1은 Pn이 가진 자원을 기다림
- Pn은 P0이 가진 자원을 기다림

## Resource-Allocation Graph(자원할당 그래프)
![image](https://user-images.githubusercontent.com/31722512/158624293-fbc3fd20-9998-43e1-ba68-ae18a25c9af5.png)
- `자원에서 프로세스로 들어가는 화살표` : 자원이 해당 프로세스에 속해있다.
- `프로세스에서 자원으로 들어가는 화살표` : 프로세스가 자원을 요청한 상태 (아직 획득하지 못함)

![image](https://user-images.githubusercontent.com/31722512/158624839-cf618762-b155-4443-a78f-a85e5aa991b6.png)
- 그래프에 사이클이 있을 때
  * 만약 자원이 한개라면 데드락
  * 자원이 여러 개라면 데드락일 수 있고, 아닐 수도 있다.

- 왼쪽 그래프는 데드락인 이유 : R2 자원을 P1 프로세스와 P2 프로세스가 각각 점유하고 있으면서 P3 프로세스가 R2 자원을 요청중이기 때문에, 게다가 2개의 사이클이 존재
- 오른쪽 그래프는 데드락이 아닌 이유 : 사이클이 존재하지만, R1 자원과 R2 자원의 개수와 점유하고 있는 프로세스의 개수와 일치하기 때문이다. 
- (P2, P4는 점유하지만 다른 자원을 요청하지 않기 때문에 언젠간 자원을 내놓게 된다.)

## Deadlock 처리 방법
`Deadlock Prevention`
- 자원 할당 시 deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

`Deadlock Avoidance`
- 자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
- 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원을 할당한다.

`Deadlock Detection and recovery` : 데드락이 발생하게 냅두고, 발생했을 때 처리한다.
- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견시 recover(회복)한다.

`Deadlock ignorance`
- Deadlock을 시스템이 책임지지 않는다.
- UNIX를 포함한 대부분의 OS가 채택

## Deadlock Prevention (데드락이 발생하는 4가지 조건을 원천적으로 차단해서 막아보자!)
`Mutual Exclusion`
- 공유해서는 안되는 자원의 경우 반드시 성립해야 한다. (막을 수 있는 방법은 없다.)

`Hold and wait`
- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
- 방법 1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
- 방법 2. 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청한다.

`No Preemption`
- 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점된다.
- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
- State 를 쉽게 save 하고 restore 할 수 있는 자원에서 주로 사용 (CPU, Memory)

`Circular wait`
- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
- 예를 들어 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj을 할당받기 위해서는 우선 Ri를 Release 해야 한다.

=> `Utilization 저하`, `throughput 감소`, `Starvation 문제` 발생

## Deadlock Avoidance
- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 `각 자원별 최대 사용량을 미리 선언`하도록 하는 방법

`Safe state`
- 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태

`Safe sequence`
- 프로세스의 sequence <P1, P2, ... ,Pn>이 safe 하려면 Pi(1 <= i <= n)의 자원 요청이 "가용 자원 + 모든 Pj(j<i)의 보유 자원"에 의해 충족되어야 한다.
- 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장한다.
  * Pi의 자원 요청이 즉시 충족될 수 없다면 모든 Pj(j<i)가 종료될 때까지 기다린다.
  * Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다.

![image](https://user-images.githubusercontent.com/31722512/158629253-55bb61d9-ec3a-4fb8-9e35-c8fe7292f7c7.png)

## Resouce Allocation Graph Algorithm
- 자원에 대한 인스턴스가 하나밖에 없는 환경에서의 데드락을 어떻게 피해갈지에 대해서 살펴보자.

![image](https://user-images.githubusercontent.com/31722512/158629481-ccb8d81f-8477-462f-bdba-1a0f1c5f80f5.png)
- Cycle 생성 여부 조사시 프로세스의 수가 N일 때 O(N^2) 시간 복잡도가 걸린다.

## Example of Banker's Algorithm
- 자원에 대한 인스턴스가 여러개인 환경에서 데드락을 어떻게 피해갈지에 대해서 살펴보자.
- 항상 최악의 상황을 고려한다.

![image](https://user-images.githubusercontent.com/31722512/158630459-5262fe80-41b6-4f8e-a372-59ee29f3b959.png)
- `Max` : 내 평생에 자원을 최대로 쓴다는 가정하에 나타낸 수치
- 한마디로 `Need`에 표시된 최악의 자원 요청을 현재 `Available`자원 상태로 충족이 안되면 자원을 주지 않는다는 것이다.
- Safe 한 Sequence 가 존재한다면 이 시스템은 safe state로 판단한다.
- 다만 비효율적이다. 자원이 충분한 상태에서도 안전하지 않으면 자원을 주지 않기 때문에

## Deadlock Detection and recovery
`Deadlock Detection`
- Resource type 당 single instance 인 경우
  * 자원할당 그래프에서의 cycle이 곧 데드락을 의미

- Resource type 당 multiple instance 인 경우
  * Banker's algorithm 과 유사한 방법 활용

`Wait-for graph` 알고리즘
- Resource 당 single instance 인 경우
- Wait-for graph
  * 자원할당 그래프의 변형
  * 프로세스만으로 node 구성
  * Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk -> Pj
- Algorithm
  * Wait-for graph에 사이클이 존재하는지를 주기적으로 조사
  * O(n^2)

![image](https://user-images.githubusercontent.com/31722512/158634631-be99d0d8-9001-4279-b86c-36ad47e2f800.png)
- 자원의 최대 사용량을 미리 알릴 필요 없음 -> 그래프에 점선이 없다.

![image](https://user-images.githubusercontent.com/31722512/158635727-ca1f76cb-e788-41a0-baca-d07e0241a160.png)
- multiple instance 인 경우

데드락이 발견이 된다면? Recovery를 해야 한다. <br>
`Recovery`
- Process termination (프로세스 사살)
  * Abort all deadlocked processes
  * Abort one process at a time until the deadlock cycle is eliminated (하나씩 죽여보면서 데드락이 걸리지 않을때까지)

- Resource Preemption
  * 비용을 최소화할 victim(희생양) 의 선정하고 해당 희생양의 자원을 뺏어버린다.(선점한다)
  * safe state로 rollback하여 process 를 restart
  * Starvation 문제
    1. 동일한 프로세스가 계속해서 victim으로 선정되는 경우
    2. cost factor에 rollback 횟수도 같이 고려

## Deadlock Ignorance
- Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않는다.
  * Deadlock이 매우 드물게 발생하므로 deadlock 에 대한 조치 자체가 더 큰 오버헤드일 수 있다.
  * 만약, 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process를 죽이는 등의 방법으로 대처한다.
  * UNIX, Windows 등 대부분의 범용 OS가 이를 채택하고 있다.
