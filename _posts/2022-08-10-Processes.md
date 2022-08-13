---
title: OS - 1. Processes (Kor)
categories: [Computer Science, OS]
date: 2022-08-09 12:00:00
tags: [cs, os]     # TAG names should always be lowercase
author: <author_id>
---

> Process를 주제로 OS 정리를 시작합니다. 이어서 threads, process scheduling, sync., memory 관리, 가상 메모리 등 다양한 주제가 이어집니다. 최대한 내용이 자연스럽게 흘러갈 수 있도록 정리하였으니 공부에 많은 도움이 되길 바랍니다. 질문과 피드백은 하단 댓글란에 남겨주세요. 감사합니다. 

<h2><span style = "color: #b6d7a8">Process 란? </span></h2>

Process는 한 마디로 an instance of a running program 이다.  
여기서 running이란 프로그램이 메모리 위에서 돌아가는 의미다.  
하나의 프로세스는 Images(code, data, stack, heap) 와 Process context(program context(data registers, PC, SP), kernel context(pid, gid..)) 를 포함한다. 

프로세스 이미지에는 code, data, heap, stack 이 있다. Stack 은 위에서 아래로 늘었다 줄어들었다 하며 데이터를 저장하는데, 지역변수나 함수의 return 주소 등을 저장한다. Heap은 dynamically allocated memory(malloc) 이며, data에는 initialized or uninitialized data가 있다.

![Screen Shot 2022-08-02 at 10.56.46 PM.png](/assets/img/Screen_Shot_2022-08-02_at_10.56.46_PM.png){: width="600" height="350"}

추후 나오겠지만 PCB(process control block)은 CPU에 의해 실행중인 특정 프로세스를 관리할 필요로 인해 만든 커널의 자료구조다. 이는 프로세스를 효율적으로 관리해줄 정보를 제공한다. 

Process의 상태(state)는 크게 5가지로 나눌 수 있다. New, ready, running, blocked, exit. New는 프로세스가 처음 생기는 과정, ready는 프로세스가 프로세서(CPU)에게 할당되기(assigned)를 기다리는 상태, running은 명령어가 실행되는 상태, blocked는 프로세스가 I/O 작업 같은 어떤 이벤트를 기다리는 상태, 마지막으로 exit은 프로세스가 실행을 마친 상태다.  
CPU는 그럼 어떤 프로세스를 선택해서 ready 에서 running state으로 옮기는가? 이 질문에 대한 답은 추후 CPU scheduling(or short-term scheduling)에서 나온다. 

![process state](/assets/img/OS_process_state.png){: width="600" height="350"}

<h2><span style = "color: #b6d7a8">Process switching & mode switching</span></h2>

Process switching이란 running process가 interrupted 되고 운영체제가 다른 프로세스를 running state로 올리고 바뀐 프로세스가 CPU 제어권을 갖는 것을 말한다. Multi processing을 하기 위해서 process switching이 필요하다. CPU는 타임 퀀텀을 각 프로세스에 주면서 run, block, ready 등의 state 변화를 거친다. 결국 특정 순간 하나의 process만 CPU에서 수행되고 scheduler는 현재 process를 멈추고 다른 걸 골라서 수행한다. 이런 상황이 바로 process switching이다.  
이것이 어려운 이유는 기존 프로세스의 수행이 끝난것이 아니고 잠시 멈춘 것이라는 점이다. 멈춘 프로세스가 멈춘 당시의 모든 state를 복원해야 한다.  
그렇다면 언제 switching이 일어나는가? Process switching에 앞서 Mode switching을 먼저 해야함(process switching과 다름). 그리고 여기서 OS가 어떤 역할을 하는지 알아보자.

Mode switching에 대해 간략하게 먼저 알아보면, user mode와 kernel mode간 스위칭이 바로 mode switching이다. 현대 OS는 유저 프로그램이 함부로 low-level 하드웨어를 조작하거나 임의의 메모리 주소에 접근할 수 없도록 설계되었다. CPU의 수행을 크게 두 가지 모드로 나눴는데, 보통의 유저 프로그램이 실행되는 user mode와 더 특별하고(privileged) 오로지 프로세서가 제어권을 갖는 kernel mode가 있다. 이 두가지 모드를 바꾸는 action이 mode switching이다. 보통 유저모드에선 프로세스가 수행하고 커널 모드는 민감한 레지스터가 수행한다.

<h2><span style = "color: #ff3eeb">운영체제가 동작하는 원리</span></h2>  

CPU는 user mode에서 돌 수 있고, 중요한 커널 코드를 수행하는 모드로 돌 수 있다. 크게 세 가지 경우 mode switching이 발생한다(두 가지로 나눈다면 HW or SW interrupt)
1. Hardware Interrupt(External) - Interrupt가 오면 모드를 바꾼다. Timer interrupt가 발생하거나 I/O interrupt가 발생하는 경우가 이에 해당한다. 
2. Software Interrupt(Internal trap) - 예상치 못한 상황이 발생하는 경우인데, 예를 들어 변수를 0으로 나누는 경우가 있다(invalid operation). 또한 page fault도 여기에 해당한다.  
3. System call - 의도적으로 커널 모드 부르는 것이다. 어떤 file을 읽거나, fork 하는 경우 유저 모드에서 커널 모드로 전환이 필요하다. 

다만, 무조건 mode 점프를 할 수 없다. 임의의 위치이기 때문에 현재 state를 잘 기록해야하고 (register, flag 등을 어딘가에 저장해야함), 특히 processor state을 반드시 저장해야 한다.
위 세가지 경우 발생시 mode switching이 일어난다. 프로세스 스위칭은 모드 스위칭보다는 무거운 작업이다. Mode switching은 빈번하게 일어나는데, mode 스위칭이 일어난다고 반드시 프로세스간 스위칭이 발생하는건 아니다. 

Process switching으로 다시 와서 현재 프로세스가 만약 I/O operation을 하게 되면 cpu가 이를 계속 기다리기엔 리소스가 아까우니 다른 프로세스가 수행되어야 마땅하다. 하드웨어 cpu의 중요 register를 저장하고, 프로세스 중요 자료구조인 PCB를 커널이 관리하는 큐에 다시 집어넣는다. 그리고 Scheduling을 통해 프로세스를 고른다. 5 mode operation(ready, running, block 그림) 을 생각하면서 순서를 되짚어보면,  

save the ‘program context’(registers) → update the state of the current process → move the PCB to the appropriate queue → select another process to execute → move its PCB from the appropriate queue → update the state of the selected process to running → update necessary memory management structures → restore the program context of the newly selected process 순서가 될 것이다. 

> The process switch, which involves a state change, requires considerably more effort than a mode switch. 

아래 그림은 두 개 프로세스가 스위칭되는 그림이다. Save state와 restore state를 포함한 과정이 process switching이다. 스위칭은 overhead가 되며 OS 성능과 연관이 있다.

![process state](/assets/img/OS_process_switch.png){: width="400" height="240"}

<h2><span style = "color: #ff3eeb">중요 - mode & process switching의 관계</span></h2>

아래 그림에서 점선 위가 user mode operation이고 점선 아래가 kernel code이다. Process1(이하 p1)이 수행을 하다가 의도적으로 system call을 날려 mode switching이 일어난다. 해당 system call handler를 마치고 다시 유저 모드로 복귀한다. P1 수행 도중 timer interrupt이 벌어졌다. 매 instruction cycle의 마지막에 interrupt를 확인해 현재 p1을 멈추고 무조건 커널 모드로 점프한다. 두 번째는 우리가 원하지 않았음에도 불구하고 time interrupt 때문에 kernel 이 cpu 권한을 가져간다. 커널 모드안에서 p1에서 p2로 스위칭한다. P1은 잠시 멈추고 ready queue의 p2에게 cpu 제어권을 넘긴다. 결국, 점선을 넘나드는것이 모드 스위칭이다. 또한 어떤 경우에는 커널 안에서 process가 통째로 바뀌는 process switching이 일어난다.

![process state](/assets/img/OS_process_mode.png){: width="600" height="350"}

<h2><span style = "color: #ff3eeb">중요 - OS의 수행</span></h2>

OS는 다른 컴퓨터 소프트웨어와 같은 방식으로 기능한다. 이는, 프로세서에 의해 실행되는 하나의 프로그램이라는 것이다. 그렇다면 OS는 다른 프로그램처럼 하나의 프로세스인가? 그렇다면 OS가 어디에 있으며, 어떻게 수행이 되는가에 대한 질문이다. 몇 가지 디자인 방법론이 있는데 그 중 하나인 Execution within user processes 방법은 OS가 프로세스로 존재하는게 아니라 user process안에 있는 방식이다.  
다시 말해 우리의 운영체제는 별개의 프로세스로 존재하는게 아니라 우리의 process(user process context) 안에서 수행한다. 마치 커널이 user process의 일부분처럼 보인다.  
아래 그림에서 n개의 프로세스가 있고 각각의 박스를 이미지(image)라고 하자. 박스는 User process가 가질 수 있는 공간이고, 하나의 프로세스안에 os가 들어있다. 밖에서 보면 실제로 os코드는 안보인다. P1을 수행하다 스스로 system call을 부르면 커널 모드로 갔다가 돌아온다. Time interrupt가 들어가면 kernel mode 판단에 ready queue에 있는 것중 적절한 프로세스를 골라 p1은 보존하고, p2를 running state로 살린다. (p1, os-functions 넘는건 mode switching).

![process state](/assets/img/OS_process_1.png){: width="400" height="240"}

아래 그림은 32비트 머신에서 리눅스 운영체제를 수행할 때 프로세스 이미지를 보여준다. 1기가바이트를 커널이 독점해서 쓰는 주소 공간으로 할당하고, 유저 코드가 돌 수 있는 주소는 3기가 정도다 밖에서 볼 땐 초록색칸이 kernel address space인지 구분이 안된다. 커널은 내 address의 일부분을 사용하게 되는 셈이다. 예를 들어, 커널 어딘가에 원하는 특정 시스템 콜 구현 코드가 있을테고, 내 코드 수행 중 interrupt 들어올 때 커널 모드로 모드 스위칭이 일어난다. 그러면 해당 interrupt handler 수행 후 다시 user mode로 돌아온다.

![process state](/assets/img/OS_process_2.png){: width="400" height="240"}

위 그림을 아파트로 비유할 수 있다. 아파트의 한 층을 process라고 가정하고 방이 4개 방 하나하나가 1 기가바이트다. 커널은 유저가 방 하나를 rent해준셈이다. 방 세 개는 유저인 내가 사용힌디. 내가 만약 801호 주인이고 Kernel 방 실소유는 유저다. 모드 스위칭이 발생하게 되면 커널 모드로 가는데 이는 3개짜리 유저 방에서 놀다가 커널 방으로 들어가는 것이다. 아파트 밖에선 무슨 일이 있는지 보이지 않는다.  
32비트 머신에선 4기가 까지 쓸 수 있어야 하는데 공간의 제약이 있다. 이 방법은 빈번히 발생하는 모드 스위칭 overhead를 줄인다. 만약 커널과 유저를 별개의 프로세스로 하여 커널과 유저 모두 다 4기가로 만든다면 빈번하게 1초에 1000번 이상의 프로세스간 스위칭이 일어나 전체 속도가 느려진다.

<h2><span style = "color: #b6d7a8">Process creation, hierarchy </span></h2>  

여러 개의 프로세스를 운영체제 커널이 관리한다. 커널이 해야할 일은 새로운 프로세스를 만드는 일이다. 그렇다면 커널이 프로세스를 만들 때 어떤 과정을 거치는가?

우리가 한 프로그램을 수행하고 있고 다른 프로세스를 만들고 싶다. UNIX os의 경우 init이라고 하는 커널 프로세스가 shell process를 만든다. 프로세스에는 계층(Hierarchy)이 있다. 즉, 시스템의 모든 프로세스는 수직적인 관계를 가지고 있다(Parent, child). Process hierarchy에 따라서 parent, child의 관계는 어떤 모습일까? 소스를 따로 관리 할 것인지 등 많은 이슈가 존재한다. 대표적으로 address space가 있다. Child process는 parent의 이미지를 그대로 수행(duplication)하거나 완전히 다른 코드를 수행 할 수 있다. 
수행관점에서 보면 parent가 child를 만들었는데 수행순서는 parent first or child first인지, resource를 child와 100% 공유할 것인지 부분만 공유할것인지 아예 안할건지 등의 이슈도 정의해야한다.  이렇게 하나의 프로세스를 만든는 것은 많은 이슈가 있다. 정확히 이해해야 적절한 child process를 의도대로 사용 가능하다. 

언급한 바와 같이 UNIX OS 에선 반드시 어떤 process에 대한 parent가 존재한다. Parent없이 child는 존재할 수 없다. 유닉스 운영체제에서 shell이라고 하는 개념과 command를 사용해서 시스템을 써야하는데 이 관계를 parent-child로 보여주는 그림이 아래의 그림이다.  
Terminal 에 cat fileA라는 명령어 입력한다. (cat은 fileA의 내용을 보는 명령어). Shell은 그 다음에 $를 내보낸다. 이 과정이 수행되는 flow를 보면 우리가 터미널에 앉아있고 cat fileA를 입력한다. 여기서 shell은 우리와 대화하는 interactive한 프로세스다. Shell이 명령어를 받으면 shell이라는 프로세스가 우리의 명령어로 만들어진 cat 을 수행하는 프로세스를 만든다. Shell 프로세스가 자기와 똑같은 child를 만든 후 그것을 cat으로 바꾼다. Shell은 cat을 만들었기 때문에 next line에 $를 터미널에 보내고 임무 수행 종료한다. Cat은 fileA를 디스크에서 읽어서 화면에 뿌려준다. 이 때, 철저하게 cat은 shell의 child가 된다. 어떤 명령을 shell에 치더라도 그 명령어로 구성된 process가 만들어진다. 이렇게 Shell 프로세스가 우리와 어떻게 대화하는지 이해할 수 있다.  이 방식이 전통적인 UNIX OS에서 프로세스의 생성 틀이다.

![process state](/assets/img/OS_process_3.png){: width="400" height="240"}

<h2><span style = "color: #b6d7a8">UNIX system calls </span></h2>  

프로세스를 만들고 관리하기 위한 커널 서비스가 필요하다. 프로세스 생성, 관리에 관한 몇 개의 system call 중 대표적으로 프로세스를 만들고 죽이고 동기화 등과 관련한 핵심적인 4 system calls을 다루었다.  Fork는 새로운 프로세스를 생성한다. 똑같은 프로세스를 복제한다. exec라는 콜은 fork를 통해 만들어진 프로세스의 프로그램을 바꿀 수 있다. Fork만 있다면 parent, child가 똑같아진다. 다른 내용으로 바꾸려면 exec() 계열의 몇 개의 system call을 써야한다. Parent 와는 다른 프로세스 생성한다. Synchronization 동기화가 필요하면 wait()을 쓰고 exit()는 현재 프로세스를 끝내는 call이다.

- fork()
자신과 똑같은 프로세스를 복제하는 유일한 길이다. Identical 한 카피를 만든다. Parent와 똑 같은 프로세스를 cloning하지만 유일하게 다른건 pid이다. Pid는 프로세스가 서로를 구별하기 위한 프로세스 id다. 이것만 제외하고 100% 같다. Return value에 따라 parent 인지 child인지 알 수 있다. Pid가 0이면 child가 된다. 0이 아닌 integer가 나오면 parent이고  -1이 나오면 실패다. OS 관점에서 보게 되면 많은 유저 관리를 해야 해서 여러 개의 시스템 limit을 건다. 예를 들어 total process 개수를 제한하거나 사용자가 가질 수 있는 프로세스의 최대 개수를 제한할 수 있다. 시스템의 자원이 제한적이기 때문에 제한이 필요하다.  
Fork()를 부르면 커널 모드로 가서 나와 같은 child를 만든다. 시스템에는 추가적인 process가 생성되는데 둘은 pid가 다르므로 두 개는 별개의 process다. Fork 이후에 서로 다른 길을 가며 코드는 같지만 다르게 수행한다. 

- exec()
Parent와 child가 다른 코드 수행을 구현하기 위한 call이고 execve가 핵심이다. 새로운 프로그램을 수행하기 위함이다. Exec를 부르는 실제 프로세스는 새로운 프로그램으로 완전히 새롭게 교체된다.
이 시스템 콜을 써서 어떤 의미있는 작업을 할 수 있는가? fork와 exec call을 같이 써서 비로소 완벽한 새로운 프로세스 구현이 可하다. 새로운 child에게 pid=105를 줬다. Fork가 끝나고 exec을 수행하면 그 순간부터 105 프로세스안에 모든 이미지가 bin/ls 코드로 바뀐다. 결론적으로 두 개의 프로세스가 존재하게 되고 pid, 프로세스 이미지가 전혀 다른 두 개의 프로세스 생성이 완료되었다.

![process state](/assets/img/OS_process_4.png){: width="400" height="240"}

- exit()
Exit은 깨끗하게 process를 정리한다. Child가 끝나면 exit 상태를 parent한테 전달한다. 

<h2><span style = "color: #b6d7a8">Processes' communication, IPC </span></h2>  

프로세스간 협력은 어떤 방식으로 이뤄질까? 첫 번째로 서로 통신이 가능해야한다. 데이터를 교환하거나 어떤 순서로 서로가 동기화를 하는지. 결국 process가 coordination한다는건 통신과 동기화를 잘 지원한다는 의미다.  
Interprocess communication 통신은 자연스럽게 동기화까지 구현한다. IPC라고 하는데, 하나의 컴퓨터 안에서 두 개 프로세스가 IPC를 하거나, 서로 다른 컴퓨터에서 IPC를 할 수 있다. 일반적으로는 web client server model이 있다.

- **PIPE**  
Pipe는 단일 컴퓨터에서만 사용한다. 우리가 어떤 파이프를 설정하는데, 파이프는 커널에 만들어지고 (관처럼 생각) 유저 프로세스에서 파이프에 데이터를 쓰거나 파이프의 데이터를 읽는다. 실제로는 parent가 child를 만들고 동기화를 할 경우에 파이프를 쓴다. Parent가 파이프를 설정하고 fork후 child 생성한다. Parent, child가 쓸 수 있는 파이프가 있고 parent가 child에게 데이터를 주려면 pipe 에 write을 하고 child는 read한다. 반대도 마찬가지다. 이는 Parent, child간의 통신을 나타낸다.

![process state](/assets/img/OS_process_5.png){: width="400" height="240"}

- **Named pipe**  
위 방식의 가장 큰 문제는 parent와 child에서만 쓸 수 있다는 점이다. 또 pipe가 permanent하지 않다. 이런 문제를 해결하기 위해 FIFO(named pipe)가 등장했다. 커널에 FIFO 파이프를 항상 유지하여 프로세스가 죽어도 존재하는 고정된 pipe가 있다. 이걸 통해 process 두개가 데이터 주고받는다. 임의의 두개의 프로세스가 named pipe를 통해 데이터 주고받고 임의의 두개의 프로세스(부모 자식간이 아님)간 데이터 교환한다. Basic pipe 보다 generic하다. 하지만 이는 단일 호스트 프로세스간 사용할 수 밖에 없다.

![process state](/assets/img/OS_process_5.png){: width="400" height="240"}

- **System V IPC**  
더 고급기능을 가진 IPC는 ATnT에서 만든 system V IPC 기법이 있다. IPC facilities인데 이는 message passing, shared memory, semaphores를 통해 더 유연하게 데이터 교환, 동기화를 한다.

- **Client Server IPC**  
컴퓨터망에 연결된 두 개의 컴퓨터가 서로를 인지해 데이터를 교환하기 위해 Client server IPC가 등장했다. 한 쪽 컴퓨터는 서버 역할을 하고 다른 쪽은 서비스 요청한다. Client server가 pair up하여통신이 가능해야 한다. 다른 프로세스가 통신해서 데이터 교환한다. OS, 하드웨어가 달라도 socket api를 쓰면 데이터 교환 可하다.  

> `네트워크 지식`  OSI 7 layer 중 network layer를 구성하는 기술 중 하나가 Internet Protocol이다. 하드웨어 컴퓨터는 하나의 고정 ip를 할당받고 이거 위에 여러 개의 transport layer를 구성한다. 어떤 경우는 TCP or UDP위에 app이 돌아간다. 우리가 쓰는 통신은 physical link layers, 위에 IP, transport layer, app 순서다. TCP/IP는 거의 모든 컴퓨터에 구현되어 있다. 내 프로세스가 바다를 건너서 통신하고 싶다면 서로 인지해야하는데, 해당 컴퓨터의 IP를 알아야 하고 그 프로세스를 port number로 정한다. 해당 컴퓨터의 IP와 port number를 알면 특정이 가능하다. TCP를 설명하자면, 우선 서버에 parent, child server가 있다. 임의의 클라이언트가 child server랑 1대1로 연결된다. 어떤 서버에 들어가면 parent 서버를 fork해서 child 서버를 만든다. 이 child 서버는 1번쨰 클라이언트만 전담한다. 두 번째 클라이언트가 요청하면 또 fork하여 다른 child 서버를 만든다. 천 명이 요청하면 천 개의 child프로세스 만든다. UDP와 달리 전담하여 클라이언트와 서버가 연결된다. 완벽히 reliable하며 순서도 지켜진다. 
{: .prompt-tip }


사진 출처 &copy; 연세대학교 차호정 교수님 강의자료 
 
<br /><br /><br /><br /><br /><br />
<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://yoontaeung-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>