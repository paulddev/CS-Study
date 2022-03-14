# CPU Scheduling

## CPU and I/O Bursts in Program Execution
![13](https://user-images.githubusercontent.com/31722512/158058262-18394fc6-cc9f-44ec-832e-ee2e69e28b64.png)

어떤 프로그램이 실행된다는 것은 CPU burst와 I/O burst를 번갈아가며 사용하게 된다.

## CPU-burst Time의 분포
![14](https://user-images.githubusercontent.com/31722512/158058418-4a888f99-67f0-41f2-a811-42fbce54dc02.png)


## 프로세스의 특성 분류
프로세스는 그 특성에 따라 당므 두 가지로 나눈다.
- I/O-bound process
  * CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
  * many short CPU bursts

- CPU-bound process
  * 계산 위주의 job
  * few very long CPU bursts

## CPU Scheduler & Dispatcher
`CPU Scheduler` : Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.

`Dispatcher`
- CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
- 이 과정을 Context Switch(문맥 교환)

CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우다.
1. Running -> Blocked (I/O 요청하는 시스템 콜)
2. Running -> Ready (할당시간 만료 timer interrupt)
3. Blocked -> Ready (I/O 완료후 인터럽트)
4. Terminate

1, 4 에서의 스케줄링은 `nonpreemptive (비선점형) (강제로 빼앗지 않고 자진 반납)` <br>
All other scheduling is `preemptive (선점형) (강제로 빼앗음)`

# Scheduling Algorithms
- FCFS (First-Come First-Served)
- SJF (Shortest-Job-First)
- SRTF (Shortest-Remaining-Time-First)
- Priority Scheduling
- RR (Round Robin)
- Multilevel Queue
- Multilevel Feedback Queue

## Scheduling Criteria 성능 척도
사장 입장 <br>
`CPU utilization (이용률)` 전체 시간 중에서 CPU가 놀지 않고 일한 비율 <br>
`Throughput(처리량)` 주어진 시간 동안 몇 개의 작업을 완료했는가? <br>

고객 입장 <br>
`Turnaround time (소요시간, 반환시간)` 다쓰고 나갈 때까지 걸린 시간 <br>
`Waiting time (대기 시간)` Ready queue에 대기한 시간 <br>
`Response time (응답 시간)` Ready queue에 들어와서 처음으로 CPU를 얻는 시간 <br>


## FCFS (First-Come First-Served)
- `먼저온 순서대로 처리해주는 알고리즘`
- 비선점형(Nonpreemptive) 하다.

![15](https://user-images.githubusercontent.com/31722512/158061605-2da9eacc-a7c5-4129-ba3d-33c80f77ccd9.PNG)

![16](https://user-images.githubusercontent.com/31722512/158061844-54ff7ced-d127-4879-9816-85a498e498f9.PNG)

`Convoy Effect` : 긴 프로세스 처리로 인해 작은 프로세스가 오래 기다리게 되는 현상

## SJF (Shortest-Job-First)
- CPU를 사용하고자 하는 시간이 짧은 프로세스에게 CPU를 먼저 주는 스케줄링
- 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
- `CPU Burst time이 가장 짧은 프로세스를 제일 먼저 스케줄`

Nonpreemptive
- 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점당하지 않는다.

Preemptive
- 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗긴다.
- 이 방법을 `Shortest-Remaining-Time-First(SRTF)`이라고도 부른다.

SJF is optimal (이것보다 더 짧은 경우가 나올 수 없다.)
- 주어진 프로세스에 대해 minimum average waiting time을 보장

![17](https://user-images.githubusercontent.com/31722512/158062102-bc9e0be5-5a21-4651-b576-6fca7f47bbf1.PNG)

![18](https://user-images.githubusercontent.com/31722512/158062123-eb6f40e0-183c-42db-bd9c-f5d82f3922c7.PNG)

SJF 문제점
1. starvation(기아 현상) : 계속해서 짧은 프로세스가 도착하면, 자기는 실행할 수 없음
2. 매번 CPU의 burst time을 알 수 없다.

다음번 CPU burst time을 어떻게 알 수 있을까?
- 추정(estimate)만이 가능하다.
- 과거의 CPU burst time을 이용해서 추정한다.

![19](https://user-images.githubusercontent.com/31722512/158062395-7f19b349-fe32-4036-b623-886ab1f6e5c3.PNG)

미래를 예측하는 방법은 과거의 기록을 보고 결정한다.

## Priority Scheduling
` 우선순위`가 제일 높은 프로세스에게 CPU를 스케줄링 하는 알고리즘

Preemptive
- 우선순위가 제일 높은 프로세스에게 CPU를 줬는데, 우선순위가 더 높은 새로운 프로세스가 CPU를 빼앗을 수 있는 경우

Nonpreemptive
- 우선순위가 더 높은 프로세스가 들어오게 되더라도, 기존에 CPU를 잡은 프로세스가 CPU를 내놓지 않는 상태

문제점
- `Starvation(기아 현상)` : 우선순위가 작은 프로세스는 실행되지 못하는 현상

해결법
- `Aging(에이징, 경로사상)` : 시간이 흐를때마다 프로세스의 우선순위를 증가시켜준다.

## Round Robin(RR)
- 각 프로세스는 동일한 크기의 `할당 시간(time quantum)`을 가진다.
- 할당 시간이 지나면 프로세스는 `선점(Preempted)`당하고, ready queue의 제일 뒤에 가서 다시 줄을 선다.
- n 개의 프로세스가 ready queue에 있고 할당 시간이 `q time unit`인 경우 각 프로세스는 최대 `q time unit` 단위로 CPU 시간의 1/n 을 얻는다.
- `어떤 프로세스도 (n - 1)q time unit 이상 기다리지 않는다.` -> 응답성이 좋아진다.

Performance
- q large -> FCFS
- q small -> context switch 오버헤드가 커진다.

![20](https://user-images.githubusercontent.com/31722512/158062891-7c1bd4e5-0a59-4557-b8b6-bf4913e29bd8.PNG)

전체적인 Turnaround time은 길어지지만, 핵심은 `응답성`이 빨라진다는 것이다.

## Multilevel Queue
![21](https://user-images.githubusercontent.com/31722512/158179152-32cc1571-5807-4698-8a8c-de7fb0f17a98.png)

- Ready queue를 여러 개로 분할
  * foreground (interactive)
  * background (batch - no human interaction)

- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  * foreground - RR
  * background - FCFS
- 큐에 대한 스케줄링이 필요
  * Fixed priority scheduling
    1. serve all from foreground then from background.
    2. Possibility of starvation.
  * Time slice
    1. 각 큐에 CPU time을 적절한 비율로 할당
    2. 80% to foreground in RR, 20% to background in FCFS

차별적인 방법

## Multilevel Feedback Queue
![22](https://user-images.githubusercontent.com/31722512/158179472-72dec705-5ec3-4335-aaac-ddd6e8ac9f59.png)

- 프로세스가 다른 큐로 이동 가능
- 에이징(Aging)을 이와 같은 방식으로 구현할 수 있다.
- Multilevel-feedback-queue scheduler를 정의하는 파라미터들
  * Queue 수
  * 각 큐의 scheduling algorithm
  * Process를 상위 큐로 보내는 기준
  * Process를 하위 큐로 내쫓는 기준
  * 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

Example) <br>
Three queues
1. q0 - time quantum 8 milliseconds
2. q1 - time quantum 16 milliseconds
3. q2 - FCFS

Scheduling
- new job이 queue 0으로 들어감
- CPU를 잡아서 할당 시간 8 milliseconds 동안 수행됨
- 8 milliseconds 동안 다 끝내지 못했으면 queue 1으로 내려감
- queue 1 에 줄서서 기다렸다가 CPU를 잡아서 16 milliseconds 동안 수행됨
- 16 milliseconds 에 끝내지 못한 경우 queue 2로 쫓겨남

## Multiple-Processor Scheduling
- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- `Homogeneous processor`인 경우
  * Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
  * 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해진다.
- `Load sharing`
  * 일부 프로세서에 job이 몰리지 않도록 `부하를 적절히 공유`하는 메커니즘 필요
  * 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- `Symmetric Multiprocessing (SMP)`
  * 각 프로세서가 각자 알아서 스케줄링 결정
- `Asymmetric multiprocessing`
  * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따른다.

## Real-Time Scheduling
`Hard real-time systems`
- Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함(데드라인 보장해야 된다.)

`Soft real-time computing`
- Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야 함.

## Thread Scheduling
`Local Scheduling`
- User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정

`Global Scheduling`
- Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정한다.


## Algorithm Evaluation (알고리즘 평가하는 방법)
`Queueing models (큐잉 모델)`
- 확률 분포로 주어지는 `arrival rate`와 `service rate` 등을 통해 각종 performance index 값을 계산한다.

`Implementation (구현) & Measurement (성능 측정)`
- 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교

`Simulation(모의 실험)`
- 알고리즘을 모의 프로그램으로 작성후 `trace (시뮬레이션 프로그램에 들어갈 Input data)`를 입력으로 하여 결과 비교한다.
