# Process Management (프로세스 생성)
`부모 프로세스(Parent Process)`가 `자식 프로세스(Children Process)`를 생성 => Copy-On-Write(COW, Write가 발생했을 때 Copy를 하겟다.) <br>
프로세스의 트리(계층 구조) 형성 <br>
프로세스는 자원을 필요로 한다.
- 운영체제로부터 받는다.
- 부모와 공유한다.

자원의 공유
- 부모와 자식이 모든 자원을 공유하는 모델
- 일부를 공유하는 모델
- 전혀 공유하지 않는 모델

수행(Execution)
- 부모와 자식은 공존하며 수행되는 모델
- 자식이 종료(terminate)될 때까지 부모가 기다리는(wait)모델

주소 공간(Address space)
- 자식은 부모의 공간을 복사한다. (binary and OS data)
- 자식은 그 공간에 새로운 프로그램을 올린다.

유닉스의 예시
-`fork()` 시스템 콜이 새로운 프로세스를 생성한다.
  * 부모를 그대로 복사 (OS data except PID + binary)
  * 주소 공간 할당
- fork 다음에 이어지는 `exec()` 시스템 콜을 통해 새로운 프로그램을 메모리에 올린다.

## 프로세스 종료(Process Termination)
프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (`exit`)
- 자식이 부모에게 output data를 보냄 (via wait)
- 프로세스의 각종 자원들이 운영체제에게 반납됨

부모 프로세스가 자식의 수행을 종료시킴 (`abort(강제종료)`)
- 자식이 할당 자원의 한계치를 넘어섬
- 자식에게 할당된 태스크가 더 이상 필요하지 않음
- 부모가 종료(exit)하는 경우
  * 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다.
  * 단계적인 종료

## fork() 시스템 콜
![8](https://user-images.githubusercontent.com/31722512/158056785-2994dbc3-aef2-4f65-a08f-a2a3888bc706.png)

똑같은 코드를 옆에 하나 두고 생각하면 쉽게 확인할 수 있다.

1. fork()를 만나면 새로운 프로세스를 하나 만들게 된다.(자식 프로세스가 만들어짐)
2. fork()가 끝나게 되면 부모 프로세스는 아래의 남은 코드도 마저 실행한다.
3. 자식 프로세스는 새로운게 만들어졌고, fork() 이후부터 자식 프로세스의 코드를 시작한다. (이유: 부모 프로세스(문맥)을 복제할 때 부모가 다음으로 실행될 코드가 fork() 이후이기 때문에)

부모 프로세스의 pid 는 양수이고, 자식 프로세스의 pid 는 0을 갖게 된다. (누가 원본이고 복제본인지 구분할 수 있다.)

## exec() 시스템 콜
프로세스를 다른 프로그램 실행으로 만드는 방법

exec() 을 실행하게 되면 main 함수 처음부터 실행하게 된다! (다시 태어나는)

![9](https://user-images.githubusercontent.com/31722512/158057072-0dede2c1-c7bc-482f-a5ba-0945e1a781fb.png)

자식 프로세스는 문자열을 출력한 다음에 execlp로 인해 새로운 프로그램으로 덮혀씌워지게 된다. (한번 덮혀씌워지면 되돌아올 수 없다.) 

## wait() 시스템 콜
프로세스 A가 wait() 시스템 콜을 호출하면
- 커널은 child가 종료될 때까지 프로세스 A를 sleep 시킨다. (block 상태)
- Child process가 종료되면 커널은 프로세스 A를 깨운다. (ready 상태)

![10](https://user-images.githubusercontent.com/31722512/158057291-7371a05e-5e93-4a42-9b29-12516bc86231.png)

한 마디로 자식이 종료될 때까지 기다리는 시스템 콜이다.

## exit() 시스템 콜
프로세스의 종료
- 자발적 종료
  * 마지막 statement 수행 후 exit() 시스템 콜을 통해 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어준다.

비자발적 종료
- 부모 프로세스가 자식 프로세스를 강제 종료시킴
  * 자식 프로세스가 한계치를 넘어서는 자원 요청
  * 자식에게 할당된 태스크가 더 이상 필요하지 않음
- 키보드로 kill, break 등을 친 경우
- 부모가 종료하는 경우
  * 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

## 프로세스와 관련한 시스템 콜
- fork() create a child (copy)
- exec() overlay new image
- wait() sleep until child is done
- exit() frees all the resources, notify parent

## 프로세스 간 협력
`독립적 프로세스(Independent process)`
- 프로세스는 각자의 주소 공간을 가지고 수행되므로 원친적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

`협력 프로세스(Coporationg process)`
- 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

프로세스 간 협력 메커니즘(IPC: Interprocess Communication)
- 메시지를 전달하는 방법
  * `message passing` : 커널을 통해 메시지 전달
- 주소 공간을 공유하는 방법
  * `shared memory` : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
  * `thread`: 스레드는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들간에는 주소 공간을 공유하므로 협력이 가능하다.

## Message Passing
`Message System` : 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템

운영체제 커널을 통해서 메시지 전달을 하게 된다.

`Direct Communication(직접 통신)` :  통신하려는 프로세스의 이름을 명시적으로 표시

`Indirect Communication(간접 통신)` : mailbox (또는 port)를 통해 메시지를 간접 전달

![11](https://user-images.githubusercontent.com/31722512/158057824-8f405a5f-19ef-404c-8040-6d6c128a4363.png)

## Interprocess Communication
![12](https://user-images.githubusercontent.com/31722512/158057925-5f470ba7-b92d-4f2d-86e0-090dcf4d8410.png)

사용자 프로세스 간의 통신은 서로 직접하는게 아닌 중간에 `커널`이 반드시 개입해야 한다.
