# Process Synchronization (프로세스 동기화), 다른말로 Concurrency Control (병행 제어)

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

## Algorithm 1
![image](https://user-images.githubusercontent.com/31722512/158395506-f27f27c7-7d98-413e-ba89-5b60a77dfa9a.png)

- 프로세스 p0, p1은 같은 코드를 가지고 있는 상태에서 보자.
- 내 turn이 아니면 While 문 안에서 뺑뺑이 돌다가, 내 차례가 될 때 CS에 들어가게 되고
- CS에서 나오면 다시 상대방이 들어갈 수 있게 turn을 넘겨준다.

- mutual exclusion을 만족한다.
- progress를 만족하지 못한다.

## Algorithm 2
![image](https://user-images.githubusercontent.com/31722512/158396259-21dd2a19-f421-49d6-906b-b0d27ab00d18.png)

- flag 를 도입한다.
- 본인의 flag를 true로 바꾸고, 상대방의 flag를 체크한다.
- 상대방 flag가 셋팅이 아니면 CS로 들어갔다가 나올 때 flag를 꺼둔다.

- mutual exclusion을 만족한다.
- Progress를 만족하지 못한다. (둘 다 2행까지 수행 후 끊임 없이 양보한다. => 아무도 CS에 접근할 수 없다.)
- 코드 하나하나 수행될 때마다 CPU를 빼앗길 수 있기 때문이다.

## Algorithm 3 (소프트웨어 해결방법)
![image](https://user-images.githubusercontent.com/31722512/158396766-cacf9dd7-8fd5-4a10-8f68-2ecd6315db50.png)

- turn 과 flag 를 같이 쓴다.
- 3개의 요구 조건을 만족한다.

- 다만 문제가 있다'
- `Busy Waiting(=Spin lock)` : 계속 CPU와 Memory 를 쓰면서 기다린다.

## Synchronization Hardware (하드웨어 지원)
![image](https://user-images.githubusercontent.com/31722512/158397880-347212ab-f7e7-47c7-ab8c-9fec0258b9e8.png)

핵심 2개의 명령어를 하나로 처리할 수 있도록 하드웨어가 지원한다.

Test & Set 은 Read 하고 1로 바꾸는 작업을 `atomic`하게 수행한다.
- lock이 0일 때 `Test_and_Set`으로 들어간 후 lock 1로 바뀐 다음에 CS에 접근하게 된다.
- lock이 1일 때 `Test_and_Set`으로 들어간 후 lock이 1이므로 bust wait으로 기다리게 된다. (읽은 시점에 1이였기 때문에

## Semaphores (세마포어)
![image](https://user-images.githubusercontent.com/31722512/158399076-56ed6983-1952-4c48-b9da-59ecb104126a.png)

세모포어도 일종의 추상 자료형이다.
- 공유자원을 획득하고 반납하는 것을 세마포어가 처리해준다.
- P연산은 자원을 가져가는 과정 (Lock 걸기)
- V연산은 자원을 다 사용하고 반납하는 과정 (Lock 해제)

- 아직까지 Busy Waiting 이 존재

## Critical Section of N Processes
![image](https://user-images.githubusercontent.com/31722512/158399824-92ea0af1-f999-497f-9e12-f5fdb8136bb5.png)

- 세마포어 변수 mutex 변수를 1로 두고
- 락을 걸때 P연산하고
- 빠져나올 때 V연산을 해준다.

- busy-wait은 효율적이지 못하다. (=spin lock)
- Block & Wakeup 방식의 구현은 어떨까? (=sleep lock)

## Block / Wakeup Implementation
![image](https://user-images.githubusercontent.com/31722512/158400570-595fa9e0-bb34-4259-a9c2-9a1a4ab2a074.png)

![image](https://user-images.githubusercontent.com/31722512/158400825-baab216e-2de8-47b1-88fc-315635013ba9.png)
- p연산은 자원을 획득하는 연산
  * 만약에 S.value < 0 이면 이미 자원을 모두 사용하고 있는 상태이므로 block 시킨다.
- v연산은 자원을 반납하는 연산 + 잠들어있는 프로세스가 있다면 wakeup 해주기
  * 만약에 자원을 반납하면서도 S.value <= 0 이라면? 누군가가 기다리고 있다는 상태이므로 잠들어있는 프로세스 하나를 리스트에서 빼와 깨워준다.

## Which is better?
Busy-wait vs Block/wakeup =>  Block/wakeup 이 효율적이다.

Block/wakeup overhead vs Critical section 길이
- CS가 긴 경우 Block/wakeup이 적당
- CS가 매우 짧은 경우 Block/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있다.
- 주로 mutual exclusion(lock/unlock)에 사용한다.

## Two Types of Semaphores

Counting semaphore
- 도메인이 0 이상인 임의의 정수값
- 주로 resource counting에 사용

Binary semaphore(=Mutax)
- 0 또는 1 값만 가질 수 있는 semaphore
- 주로 mutual exclusion(lock/unlock)에 사용

## Deadlock and Starvation
![image](https://user-images.githubusercontent.com/31722512/158402216-f8c4dbd1-1447-4221-bf39-d8cd8cff3e1e.png)

세마포어를 사용할 때 주의할 점이 있다. 원치 않는 결과가 발생하는 경우가 있기 때문에

예를 들어 S,Q 자원을 둘 다 가지고 있어야 CS에 들어갈 수 있다고 할 때, 서로가 각각 S와 Q 하나씩 가지고 있는 상태에서 남은 자원을 기다리는 상태 (영원히)

`둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상` => 데드락

`Starvation`
- `indefinite blocking` 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
- 나머지 자원을 얻지 못해서 실행하지 못하는 경우도 기아 현상이라고도 할 수 있다.

## Classical Problems of Synchronization
- Bounded-Buffer Problem (Producer-Consumer Problem)
- Readers and Writers Problem
- Dining-Philosophers Problem

=> 동기화와 관련된 고전적인 문제들

## Bounded-Buffer Problem
![image](https://user-images.githubusercontent.com/31722512/158405136-070edac5-d156-4f8c-929c-71d331395b6c.png)

Buffer의 크기가 유한한 환경에서 생산자-소비자 문제다.
- 프로세스가 생산자, 소비자가 있다.
- 공유하는 버퍼에 2개 이상의 생산자 혹은 소비자가 접근할 때 접근성에 대해서 문제가 발생한다.
- 생산자 프로세스 입장에서는 자원은 비어있는 버퍼의 개수
- 소비자 프로세스 입장에서는 자원은 채워져있는 버퍼의 개수

![image](https://user-images.githubusercontent.com/31722512/158407085-565dc5dc-77cc-4774-8f68-ccced926741d.png)
- mutex (공유 버퍼를 접근할 수 있는 자원)

`생산자 입장`
- 아이템 x 를 만든다.
- P(empty) 비어있는 버퍼의 개수를 확인 => 없다면 여기서 기다리게 되고, 있으면 하나 빼고 지나간다.
- P(mutex) 공유 버퍼에 접근하기 전에 현재 아무도 CS에 없다면 들어가게 되고, 있다면 여기서 기다린다.
- add x to buffer : 비어있는 버퍼에 만든 아이템 x를 넣어준다.
- V(mutex) 공유 버퍼에서 나오니까 자원을 반납한다.(다른 프로세스가 CS에 들어갈 수 있도록)
- V(full) 소비자 프로세스를 위해서 채워져있는 full의 수를 +1 증가시킨다.

`소비자 입장`
- P(full) 채워져있는 버퍼의 개수를 확인 => 없다면 여기서 기다리게 되고, 있다면 하나 빼고 지나간다.
- P(mutex) 공유 버퍼에 접근하기 전에 현재 아무도 CS에 없다면 들어가게 되고, 있다면 여기서 기다린다.
- remove an item from buffer to y : 채워져있는 버퍼에서 y라는 아이템을 빼먹는다.
- V(mutex) 공유 버퍼에서 나오니까 자원을 반납한다.
- V(empty) 채워진 버퍼를 비웠으니까 비워진 버퍼의 개수를 +1 증가시킨다.

## Readers and Writers Problem
![image](https://user-images.githubusercontent.com/31722512/158408670-17080dcc-39a4-4260-94f7-fba8ce42ab19.png)

- 프로세스는 읽는 프로세스와 쓰는 프로세스로 나눠진다.
- 공유 데이터에 여러 프로세스가 동시에 접근할때의 문제
- Reader 프로세스는 동시에 여러명이 읽어도 문제가 없지만, Writer 프로세스는 동시에 여러명이 쓰게 되면 일관성이 깨지게 된다.
- Writer 프로세스는 아무도 없을 때만 쓸 수 있다.

![image](https://user-images.githubusercontent.com/31722512/158409061-00965975-b43a-44e5-a78d-2fd884593a13.png)

`Writer` 입장
- P(db) db에 누가 접근했는지 확인한다.
- writing DB
- V(db)

`Reader` 입장 (db, readcount 공유하는 것)
- P(mutex) 공유 변수에 누가 접근했는지 체크
- readcount++ Reader 한 명 추가요~
- if (readcount == 1) 만약에 처음 고객이라면? P(db)로 db 접근을한다.
- V(mutex) 공유 변수(readcount) 수정 후 나오니까 자원을 반납
- reading DB
- P(mutex) 공유 변수에 누가 접근했는지 체크
- readcount-- 이제 나갈려고 하니까 하나 빼주고
- if (readcount == 0) 내가 마지막으로 나가면 불꺼주기 V(db)로 db 반납
- V(mutex) 공유 변수 수정 후 나오니까 자원을 반납

문제점 Starvation Writer 는 무조건 Reader 가 다 빠져나가야 쓸 수 있다. <br>
=> 해결방안 동시에 읽으려는 수를 제한하거나 우선순위를 부여하는 방식

## Dining-Philosophers Problem
![image](https://user-images.githubusercontent.com/31722512/158403029-0e84a44f-2d7b-4631-ada5-98c79616a921.png)
- 다섯명의 철학자가 하는 일
- `생각하기`, `배고프면 밥먹기`
- 밥을 먹으려면 자신의 왼쪽 젓가락과 오른쪽 젓가락이 필요하다.
- 밥을 먹는 주기는 철학자마다 다르다.

![image](https://user-images.githubusercontent.com/31722512/158411930-0fe11516-cd71-4eea-af63-44895ddce68f.png)
- 문제점 : 데드락 가능성이 있다는 것

![image](https://user-images.githubusercontent.com/31722512/158412485-6d140fe4-fbfc-4d55-8cfb-c53632bdf6b0.png)
- self[1] = 1 이면, 2번 철학자가는 왼쪽, 오른쪽 젓가락을 잡을 수 있는 권한이 있다는 뜻

`pickup(int i)`
- P(mutex) : lock을 걸자
- state[i] = 배고픔으로 설정
- test(i) => 젓가락 권한 받기
- V(mutex) : lock 해제
- P(self[i]) : 0 셋팅

`test(int i)`
- 왼쪽 철학자도 먹고 있지 않고, 오른쪽 철학자도 먹고 있지 않고, 내가 배고픈 상태라면?
- 나의 상태를 먹는 상태로 바꾸고,
- V(self[i]) : 1 셋팅

`putdown(int i)`
- P(mutex) : lock 설정
- state[i] = 생각하기 설정
- 왼쪽 철학자, 오른쪽 철학자에게 기회를 준다.
- V(mutex) : lock 해제

## Monitor
프로그래머 입장에서 생각해보자..

동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct를 제공

`공유 데이터` 접근 문제를 모니터가 자동으로 해결해줌으로써, 프로그래머의 부담을 줄여주는 방법이다.

![image](https://user-images.githubusercontent.com/31722512/158412601-e784b764-e732-4637-82de-95688df78515.png)
- 한 번의 실수로 망할 수 있다. (사람은 실수할 수 있다..)

![image](https://user-images.githubusercontent.com/31722512/158414292-219369c3-01a1-41da-8c9e-ab67f97a186b.png)

![image](https://user-images.githubusercontent.com/31722512/158414491-292bfc41-a8de-4d76-974f-d2aae9788a44.png)
- 모니터는 기본적으로 모니터에 대한 동시 접근을 허락하지 않는다.
- 세마포어와의 차이점 lock 을 걸 필요가 없다!

![image](https://user-images.githubusercontent.com/31722512/158414790-dc35a5a6-3dbe-4747-a032-9f26f368845e.png)
- condition variable : 어떤 프로세스를 잠들게 하고 줄세우기 위한 변수다.
- 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기위해서 `condition variable` 을 사용한다.
-  `wait`와 `signal` 연산에 의해서만 접근이 가능하다.
-  `x.wait()`을 invoke 한 프로세스는 다른 프로세스가 `x.signal()`을 invoke하기 전까지 suspend(잠들게)된다.
-  `x.signal()`은 정확하게 하나의 suspend된 프로세스를 resume한다.
-  suspend된 프로세스가 없다면 아무 일도 일어나지 않게 된다.

## Bounded-Buffer Problem (Monitor)
![image](https://user-images.githubusercontent.com/31722512/158415155-05a26121-2a63-4ed7-a6b7-79b78dc4e914.png)
- 세마포어 코드를 모니터 코드로 컨버팅한 것

`생산자 입장`
- 비어있는 버퍼가 없다면 기다린다. empty.wait()
- 있다면, 비어있는 버퍼에 아이템 x 를 넣어주고
- full.signal() 로 채워진 버퍼 + 1 (잠들어있는 프로세스가 있다면 깨워주기)

`소비자 입장`
- 채워진 버퍼가 없다면 기다린다. full.wait()
- 있다면, 채워진 버퍼의 아이템을 지우고(사용하고)
- empty.signal() 로 비워진 버퍼 + 1 (잠들어있는 프로세스가 있다면 깨워주기)

## Dining Philosophers Example (Monitor)
![image](https://user-images.githubusercontent.com/31722512/158621172-247a9c75-f903-4730-8203-c8b20454448d.png)
