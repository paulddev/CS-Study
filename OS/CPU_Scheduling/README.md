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

1, 4 에서의 스케줄링은 `nonpreemptive (강제로 빼앗지 않고 자진 반납)` <br>
All other scheduling is `preemptive (강제로 빼앗음)`

