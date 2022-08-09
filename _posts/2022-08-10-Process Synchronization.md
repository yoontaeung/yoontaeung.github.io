---
title: Process Synchronization
categories: [Computer Science, OS]
date: 2022-08-09 12:00:03
tags: [cs, os]     # TAG names should always be lowercase
author: <author_id>
---

# L6. Process Synchronization

OS 0511 Synchronization

[프로세스 동기화와 상호배제](https://hee96-story.tistory.com/83)

CPU가 여러 개 있을 때 프로세스간 콜라보

2/ 동기화

멀티스레딩 remind. 코드, 데이터 리소스들을 공유하는게 핵심. Conflict가 발생할 수 있음. 동기화를 잘 해야함. 운체 관점에서 공유 리소스에 대한 적절한 제어를 해서 여러 concurrent 프로세스가 일관성 있게 작동. Concurrent 프로그래밍언어에서도 직접 user 프로그래머가 해결해야함. 대표적으로 multi threading programming.

3/

두 개 producer, consumer가 공유하는 변수들. Counter를 보면 프로듀서, 컨슈머가 적절히 잘 활용 해야하는데,,,

4/

두 프로세스가 다른 cpu에서 돈다고 하면…? 두 cpu에서 동시에 돌면?

5/

시간축으로 보면 섞일 수 있다.

6/

**Race condition(two or more concurrent processes or threads access a shared resource w/o any synchronization)**이라는 현상을 만듦. “경주”. Non-deterministic한게 문제다. 그래서 여러 동기화 매커니즘 필요하다. Semantic이 깨지지 않게.

7/

공유 문제를 어떻게 이해할 수 있는가. 멀티 스레딩 프로그램을 생각해보면 유저에서 프로그램이 돌 때, 한 번에 한 번만 전역변수를 쓰자. 동기화 primitive. 이게 잘 돼야 함. 전형적인 운체 커널에서의 동기화 이슈. 운체가 듀얼 모드 오퍼레이션으로 도는데, 녹색 박스가 커널의 전역변수. 하드웨어 interrupt가 걸리면 interrupt handler는 돌고 있는 코드를 멈추고 interrupt handler를 돌린다. 시스템 콜 핸들러와 인터럽트 핸들러가 같은 커널의 전역변수를 쓰려한다면 문제가 발생. 또한 두 개의 시스템 콜 혹은 두 개의 인터럽트 핸들러 때문에 race condition 발생 가능. 동기화 문제는 운체 커널이 나올 때부터 존재한 문제. 즉, 유저에서의 멀티스레딩 혹은 커널 자체에서든 동기화 이슈는 중요하다.

**8/ Critical sections**

각 프로세스 코드 일부분이 공유 데이터를 access 하면 **critical section**이라고 부른다. Critical section 사이에 entry, exit을 잘 구현.

9/ 솔루션

1. mutual exclusion

단 하나의 프로세스만 하나의 critical section만 수행되도록 보장

1. progress

1번이 보장되고 아무도 공유데이터를 안쓴다면 아무나 쓸 수 있게. 리소스 낭비 방지

1. bounded waiting(no starvation)

특정 프로세스가 너무 오래 기다리지 않게.

10/

평가. 임의의 프로세스는 임의의 속도로 돈다는 가정. 자기 원하는 대로 수행한다. 위 가정하에 entry, exit 잘 구현.

11/

Low level, high level primitive. 로우 레벨은 lock. Lock은 100% 소프트웨어, CPU 명령어, interrupt disabling or abling 기법으로 나뉨. 하이 레벨 동기화 기법은 운체나 병렬 언어에서 제공하는 솔루션. 사용자가 쓰기 편한 기법.

12/Locks

공유 데이터 쓰기 전에 lock하고 쓰고 나서 unlock. Spinlock이 특징. 계속 밖에서 돌면서 체크하는걸 spinlock이라 함

13/

100% 소프트웨어적으로 lock, unlock구현. exclusive하게 돌 수 있다. 2번째 requirement를 고민해보면 progress 이슈인데 리소스가 비어있고 쓰고 싶은 process가 있을 때 쓸 수 있나? 여기서 문제 발생. P0가 쓰고 나면 turn을 P1으로 넘긴다. P1이 없으면 P0이 계속 쓸 수 있는데 위 논리는 턴이 한 번 바뀌어야 내가 쓰기 때문에 progress 만족 안됨. Pingpong 문제.

14/

두 개의 깃발로 구성된 flag 사용. 상대방이 flag를 안올리면 내가 쓴다. 내 깃발 올리고 상대방 깃발 본다. 아까 있었던 핑퐁 문제도 없다. 하지만 각각 cpu에서 Po, P1이 동시에 수행. P0, P1은 저마다의 깃발 올림. 상대방 깃발 보니까 둘 다 올라와 있다. 간발의 차로 동시에 발생하기 때문에 각각 프로세스는 상대방 flag가 올라온걸 본다. 내릴때까지 기다려야하는데 내리질 않음. 계속 빙글 돈다. 시간차 이슈 때문에 progress requirement 만족 안됨.

15/ Peterson

1,2를 잘 섞음. Entry 코드가 핵심. 프로그레스 이슈 해결 可. Turn이 i가 되는 경우? 내가 먼저 하면 내 critical section먼저 수행.

Bounded waiting은? 운 좋게 Pi가 먼저 수행했다. Pj가 기다리고 있으니 Pi가 아니라 Pj가 써야한다. 이것도 만족

16/

다시 정리.

17/

하드웨어로 하는 방법은 없을까? CPU instruction으로. 특정 cpu 명령어를 만들고 atomic하게. Test- and- set, swap. 명령어들은 atomic하게 수행. Mutual exclusion을 하드웨어로 제공하겠다.

18/

Test and set. 함수 리턴값을 target으로 읽고 true 세팅. 쪼개서 수행되지 않는다. 처음 tands의 리턴 밸류는 0. Pj가 리턴받은 리턴값은 1. Lock이 0이 될 때 까지 계속 돈다. Cpu 명령어로 수행된다는 차이점이 있다. No bounded waiting 발생.

19/ swap

Xchag(a,b)는 아무도 못끼게 하면서 a,b를 바꾸는 명령어. Entry, exit을 다음과 같이 구현. Lock, key가 둘 다 trune면 첫 번째 프로세스 끝날 때까지 loop에 계속 걸린다.

20/ problems with spinlocks.

계속 lock 체크하느라 도는게 자원 낭비. Spinning lock이 간단하지만 비효율적. 빌딩 블록으로 해서 고급스러운 동기화 구현.

21/

커널 동기화의 강력한 기법. Interrupt를 가지고 동기화. 다른 interrupt를 허용하기 때문에 발생하는 문제. 오직 커널에서만. 다중 cpu 에서 문제도 있다.

22/

Primitive 활용을 잘 해서 high level 동기화 primitive. 두 가지 추가적인 requirements. Process가 block되게 하자. 하드웨어적으로 interrupt를 받게 하자.

23/ **Semaphore**

Locking을 해야하는데 primitive lock은 busy waiting을 해서 낭비가 심하다. Wait, signal operator를 만듦. Wait 함수는 atomic 하게 수행. Signal은 다른 프로세스가 수행하도록 허가하는 역할. S가 0이나 0보다 작으면 block. Signal 은 increment하고 빠짐. Mutex를 세마포어 변수라고 하고 1로 초기화.

24/ semaphore implementation

Busy waiting안하는 세마포어. 어떻게 코드화 시켜서 OS안에 집어넣을까? Record 형태로. Block 함수, wakeup(=signal) 함수 구현.

25/

누군가 쓰고 있다면 wait 프로세스는 blocked. 시그널 내용을 보게 되면 value값 +하고 wakeup함수를 수행해 blocked된 프로세스를 깨운다. Block, wakeup 과 같은 새로운 스케줄리을 야기하는 내용을 담고 있음. 두 함수 모두 s.value값을 쓰고 있다. 두 개의 프로세스가 s.value라는 공유 변수를 쓰고 있음. Counter 조절이랑 유사. 어떻게 wait, signal을 atomic하게 구현할 것인가?

26/

Signal, wait 함수가 동시에 수행되면 안되는게 핵심. Atomicity를 보장해야 함. 이 때 interrupt를 막고 끝나면 품. Uniprocessor. 만약 멀티프로세스라면 busy waiting lock.

27/

초기 mutex 값 1로 초기화. 혹은 counting semaphore.

28/

특정 순서로 ordering.

29/

식사하는 철학자 문제. 5명의 철학자가 원형 테이블에 앉아서 식사. 각자 자리 앞에 플레이트와 사이에 젓가락이 있음. 젓가락 두 개 확보해야 식사 가능. 모두 왼쪽 젓가락 확보하면? 교착상태.

31/

Semaphores 전역변수 다루기 때문에

Programming 관점에서 좋지만은 않다

Process ordering까지 제공해서 powerful.

Wait, signal 함수를 잘못쓰면 deadlock야기. 2,3 개 세마포어를 쓰다보면 잘못될 여지가 있다. Deadlock 가능성 높아짐. 동기화 primptive에 대한 연구 활발.

32/ **Critical Region**

Region을 정의. Region V(공유 데이터) when B do S (statement 수행)

다른 프로세스가 region V를 쓰는지 확인. If not -> B check

33/ (2)

Buffer 공유데이터. 결국 high level의 region 신택스를 잘 translate -> OS와 컴파일러를 잘 짜야함. OS와 컴파일러가 동기화 이슈를 처리해야 한다.

34/ Monitors (1)

Compiler와 OS의 깊숙한 관여가 필요하다.

(*) A monitor is a software module that encapsulates.

Structure 제공함으로써 동기화이슈 간단히 정리. Low level primptive로 high level 동기화 기법 해결.

35/ (2)

- Mutual exclusion: 하나의 모니터에는 하나의 프로세스만
- condition var: 모니터안에서 기다리는.

36/ (3)

한 모니터에 한 프로세스만. 프로세스 큐에서 entry로 들어가고 condition으로 가서 기다림.

37/ (4)

Condition 변수 -> 2개의 operation(wait, signal) 조건이 만족될 때까지 기다려야 함(rendezvous point) signal wait 된 걸 깨우는(대상 없으면 ignore) <-> semaphore는 wait, signal 에 완전히 pair up

38/ (5)

2개의 condition 변수, buffer, 2개의 function. Add entry -> 꽉 차면 x, remove -> 비면 x

Wait(not_full) – signal(not_full) 랑데부

39/ Synch. In pthreads (1)

Condition 변수 + mutex 로 monitor 역할 over

40/

빨간 부분이 다름

41/ message passing

IPC 만든다. Send, receive 의 랑데부를 통해 두 프로세스간 동기화

42/ (2)

Send 함수의 semantic? 메시지 받은 걸 확인 안하면 non blocking

Receive: non blocking receive: receive 반응 여부x. 그냥 진행

Blocking receive: 확인

Send는 non-blocking, receive는 blocking이 자연스러움.

43/ (3)

Direct or indirect

44/ SLW threads sync.

User level에서 multi threading 제공

45/ Linux kernel sync.

Kernel 자료구조 깨지는걸 방지하기 위해. Global kernel 자료구조 보호.

커널 코드 race condition 발생 가능. 내부에서 critical region을 보호해서 race condition 발생 안하도록. Kernel 은 적절한 동기화 primitive 제공

46/ (2)

여러 atomic kernel 함수 존재. Interrupt disabling 잠시동안 interrupt 막고 푸는. Locking

Why? 동기화 이슈가 필요한가? Race condition, critical region 어떤 형태의 동기화 primitive(low, high) 각 primitive 의 장단점. 커널안에서 동기화 이슈가 존재하는 이유.