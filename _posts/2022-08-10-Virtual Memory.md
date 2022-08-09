---
title: Virtual Memory
categories: [Computer Science, OS]
date: 2022-08-09 12:00:06
tags: [cs, os]     # TAG names should always be lowercase
author: <author_id>
---

# L9. Virtual Memory

OS lecture 9 VM
많은 cpu에서 가상 메모리를 지원하고 있다. 이런 하드웨어 지원을 바탕으로 운영체제가 어떻게 이 기법을 구현하고 어떤 이슈가 있는지 얘기한다. 가상 메모리의 필요성, 개념, 핵심적인 기법, 후반부에는 리눅스에서 가상메모리가 어떻게 구현되는지 소개
2/ motivating ex
가상메모리가 왜 중요한지 어떤 특징을 갖는지 프로그래머 관점에서 예제. 이런 프로그램을 두 사람이 서로 다른 시간에 수행한다면? 똑 같은 결과가 나올 것. 이 프로그램은 n의 주소를 프린트하는건데 **이 주소가 물리 메모리 주소가 아니라 프로세스의 logical address**가 된다. 코드, 전역변수, 힙, 스택이 있는데 n은 전역변수 값. 누가 언제 프로그램 실행하든 똑 같은 값이 나온다. 내가 갖는 물리 메모리와 상관없이 내 프로그램을 로지컬하게. 프로그램의 피지컬 메모리를 숨기고 로지컬 메모리로.
**3/ virtual address(logical)**
프로세스의 주소 공간은 철저하게 logical. 물리 메모리와 상관없는 아키텍쳐에 의해 정해짐. 2^16, 64kb가 logical address. 내 컴퓨터의 몇기가 ram과 상관없이 철저하게 cpu 아키텍쳐에 의해 정해지고 counter가 움직일 수 있는 공간이 virtual address. Cpu가 만들어내는 instruction은 virtual address. 코드와 데이터는 physical ram이 있는데 누군가는 이 virtual address를 physical하게 바꿔줘야 한다. Translation을 하드웨어가 운영체제가 한다. 메모리에 여러 개 프로세스가 concurrent하게 수행되고 프로세스 메모리 관리를 쉽게 할 수 있다.

Instructions executed by the CPU issue virtual addresses
**4/ VM techniques**

**“Separation of user logical memory from physical memory”**
Virtual memory 기법. 핵심 컨셉은 철저하게 logical memory를 physical memory로부터 분리 시키자. 분리를 잘하면 여러 advantage있음. 동시에 멀티플 프로세스가 돌아간다. 여러 개의 프로세스를 돌리면서 switching도 하고… indiv. 프로세스 관점에서 보면 다른 주소 공간과 철저하게 분리. 다른 프로세스의 주소 공간을 침해하면 안된다. 메모리 관리 기법이 철저하게 프로세스를 isolate. 이런 isolation이 VM을 통해서 자연스럽게 이어진다. 가장 중요한 특징이 바로 빨간 줄.

**VM enables programs to execute without requiring their entire address space to be resident in physical memory**

 가상 메모리 기법을 쓰면 피지컬 메모리가 작더라도 피지컬 메모리보다 훨씬 큰 logical address를 갖는 프로세스 실행가능. 결국 프로세스의 전체 어드레스 베이스를 피지컬 메모리에 다 담지 않고 수행가능. 로지컬 어드레스 모든 공간을 굳이 물리 메모리에 다 load 할 필요 없다. 이미지는 있지만 어떤 상황에서 그 이미지가 필요한 것은 아니다. **프로세스가 수행될 때 그 순간 순간에 피지컬 메모리에 갖다 놓으면 된다**. 이게 그 VM 기법의 기본 철학. Paging을 근본으로 해서 페이징의 고급 버전. Demand paging을 통해서 가상메모리 여러 기능 구현.
**5/ demand paging**

**“Bring a page into memory only when it is needed”**
결국 demand paging은 심플 페이징을 기초로 해서 더 효율적으로 메모리 관리를 하자. logical하게 구성된 페이지를 굳이 물리 메모리에 할당할 필요 없다. 논리적으로는 연속되어 있지만 물리적으로는 흩어져 있을 수 있다. 내 프로세스의 모든 페이지를 물리 프레임에 갖다 놓지 말고 PC에 access하는 페이지만 그 때 그 때 피지컬 페이지에 할당하자. Page-level swapping 된다. 특정 페이지 단위로 나갔다 들어오는 시스템을 page level swapping이라 하고 페이지가 요청될 때 페이지 기법을 쓰겠다는게 핵심이다. 결국은 운영체제가 볼 때 메인메모리는 cache. 어떤 프로세스의 페이지를 담을 수 있는.
물리 메모리가 금방 꽉 차는데 특정 프로세스가 수행할 때 해당 페이지를 로딩해야 하는데 로딩할 공간이 없다. 이렇게 되면 특정 페이지를 out 시킴. Demand paging은 런타임의 특정 페이지가 쫓겨나야 한다. 빈번하게 ram하고 storage간 페이지 단위의 데이터 이동이 있다. Overhead가 된다.
6/ demand paging 2
기본 컨셉. 로지컬 주소 공간이 있고 하드웨어의 램이 오른쪽에. Logical page를 피지컬 페이지로 매핑하기 위한 table이 있다. 컴파일된 프로그램은 하드 디스크에 있는데 지금 보여주는 그림은 세 개(a,c,f) 만 물리 메모리에 할당. 이 상황을 표시한 테이블이 중간 page table. 일부분만 로딩이 되어 있다. B가 swap in이 될 때 누가 쓰고 있다면 out 시키고… 포인트는 전체 프로세스에 속한 모든 페이지가 피지컬 메모리에 로드 되어 있는게 아님.

![Screen Shot 2022-08-02 at 7.55.37 PM.png](/assets/img/Screen_Shot_2022-08-02_at_7.55.37_PM.png)

7/ demand paging 3
이런 문제를 해결하기 위해 page table을 잘 활용해야 하는데 page table을 flag에 넣을 수 있음. Page table entry는 크게 두 부분으로 구성. Frame number, flag(비트/현재 페이지의 status). 비트 중에 present bit(=valid bit)이 있는데 1이 되면 실제 물리 메모리에 있다는 거고 0이면 없다는 것. Page fault가 무엇인지 정확히 알아야 한다.
8/ demand paging 4
핵심 내용이 page fault를 어떻게 handling? 필연적으로 발생하는데 왜냐면 logical page를 물리 메모리에 할당하는것도 아니고 동시에 physical page를 공유하기 때문에 필연적으로 물리 메모리가 부족하다. 해당 프로세스의 특정 페이지를 액세스 해야 하는데 없다면 kernel 이 개입.
일반적인 **page fault handling(**references to non-mapped pages generate **a page fault)**. 정의는 피지컬 프레임에 매핑이 안된 페이지를 cpu가 액세스 할 때 page fault. 기본이 이런 page fault가 나면 무슨 상황이 발생할까? 해당 페이지가 물리 메모리에 없고 kernel이 갖고 와야 함. Cpu가 수행할 때 프로세스 페이지가 PTE가 0이 되면 하드웨어 적으로 fault가 난다. 해당 페이지를 스토리지에 있는 어딘가에서 가져와서 피지컬 메모리에 적재. 내가 원하는 페이지가 스토리지 어디에 있는지 의문. 문제가 살짝 발생. 만약 빈 프레임이 있다고 하면 그 페이지를 빈 프레임에 갖다 놓으면 되지만 아마 물리 메모리가 적기 때문에 page fault가 발생시 빈 frame이 없을 확률이 있다. 결국은 해당 프로세스의 해당 페이지를 로드하기 위해선 기존에 사용하고 있던 특정 프레임을 빼앗아야 한다. 아마 어떤 프로세스가 쓰고 있을텐데 해당 frame을 redirection. 이게 나중에 페이지 교체 정책의 핵심. 내가 쓰기 위해선 누군가를 희생.
9/
Demand paging 사용시 page fault 플로우. Cpu 내에 있는 mmu가 상황 인지 후 exception 발생. 유저 모드에서 커널 모드로 갈 수 있는 방법 중 하나. User process를 멈추고. 프로세스가 fault가 났으니 프로세스가 원하는 페이지를 물리 메모리에 갖다 놓고. 해당 페이지가 어디에 있는지. 핸들러가 어디를 액세스하면 하드 디스크 위치를 알고 해당 페이지를 가져오고 피지컬 프레임이 비어 있다면 갖다놓고 page table entry를 i에서 v로 수정. 첫 번째 엔트리는 해당 프레임의 넘버. 내 프로세스가 수행하기 위한 모든 주소 정보는 갖고 있다. 커널 모드에서 유저 모드로 다시 돌아간다. Demand paging으로 수행할 때 page fault 발생 그림.

![Screen Shot 2022-08-02 at 8.07.00 PM.png](/assets/img/Screen_Shot_2022-08-02_at_8.07.00_PM.png)

10/
이런 page fault를 프로세스 수행 중에 겪을 수 밖에 없고 page fault가 한 번 발생하면 시스템 관점에서 많은 overhead. 한 번에 페이지 폴트가 발생하게 되면 많은 퍼포먼스에 문제가 생긴다. 그럼에도 불구하고 페이지 폴트를 피할 수 없는 가상메모리 기법이 왜 계속 쓰일까? 생각해봐. 가상 메모리가 동작하는 이유는 **locality** 때문이다. 지역성. 프로그램을 짤 때 프로그램안에 지역성. 빈번하게 while, for loop를 쓰는데 우리가 프로그램을 짤 때 프로그램의 flow를 보면 적절하게 localize. Loop 도는 상황이 꽤 지속. Temporal, spatial locality. 프로그램에 내재된 특성인데 temporal은 시간적, spatial은 공간적. 시간적으로 조금 전에 액세스 된 프로그램 부분이 곧바로 다시 쓰이는 내용. Spatial은 현재 프로그램 위치에서 인접한 현재 위치를 액세스한다. 지역성이 왜 중요하냐? 페이징을 우리가 지금 쓰는데 for loop로 쓰는 코드를 한페이지에 담을 수 있다면 그 페이지에 있는 순간은 fault 없이 수행 가능. 페이지 단위 이기 때문에. 지역성을 담고 있다 하면 꽤 오랫동안 페이지 폴트 없이 피지컬 프레임을 쓸 수 있다. 프로그램의 inherent 특성이 locality 가 있어 page fault가 많이 발생안함. Locality means paging can be infrequent. 지역성은 프로그램의 특성이고 우리가 의도적으로 지역성을 정해서 짜긴 어렵고 페이지 교체 정책에 대한 영향도 있고 피지컬한 메모리가 얼만큼 있냐도.
11/ VM performance
페이지 폴트가 발생하면 exception. 유저 모드에서 커널 모드로 바뀌는걸 야기. 모드 체인징을 오버헤드가 있고 페이지 폴트 익셉션 핸들러가 해당 프로세스에 요청하는 페이지를 하드디스크에 가져와서 피지컬 메모리에 올리고 page table 업데이트. 시간이 꽤 걸림. 다 준비되면 다시 유저모드로 간다. 이런 형태의 서비스가 필요하고 이 오버헤드를 줄여야한다. 예를 들어 cpu가 ram을 액세스 하는 시간을 100ns. 디스크 액세스 타임은 디스크 요청을 해서 데이터가 읽힐 때까지 시간 25ms. Page fault가 발생하면 disk operation이 필요. 이런 인자 설정을 바탕으로 계산하면 p를 page fault의 확률로 두면 1-p는 페이지가 프레임에 있는 경우. 폴트가 발생하면 p확률로 나고 한 번 폴트가 발생하면 25,000,000ns가 발생한다. 간단한 EAT의 계산인데 이런 상황에서 demand paging을 기초로 한 가상 메모리. 최대 참을 수 있는 시스템이 느려지는 한계는 10%라고 할 때 얼마의 확률로 페이지 폴트로 발생하는지 역산하면 페이지 폴트의 확률이 2,500,000번 중 한 번 정도만 페이지 폴트가 발생할 정도가 되면 전체 시스템이 10%정도 느려짐. OS의 역할이 아주 크다. 어떤 페이지를 교체할까? 현명한 정책이 필요.
12/
운체 관점에서 페이지 교체를 잘 해야 하는데. Dirty는 한 번 수정된거. 커널이 어떤 프레임을 고칠까 할 때 dirty flag가 1이면 수정이 되었기 때문에 업데이트를 해줘야 함. OS 관점에서는 수정이 안된 페이지를 선택할 것이고, 여러 정책을 살펴야. 많은 교체 정책이 있고 **페이지 교체 알고리즘은 시스템 관점에서 페이지 폴트를 최대한 줄이는**. 간단하지는 않다. 정책을 잘못 쓰면 조금 전에 쫓았던 페이지가 다시 오는 경우도 있고 optimal 한 정책을 펼 수 없다. 모든 경험과 지식을 동원해서 현재  어떤 페이지를 쫓아야 페이지 폴트 수가 줄었구나를 알텐데 그 정책을 지금 편다는게 어렵다. 페이지 교체 정책이 가상 메모리 구현을 하는데 핵심적인 내용이 되고 교체 정책을 통해 로지컬 메모리가 물리 메모리로 변환된다. 적은 피지컬 메모리에서 아주 큰 가상 메모리를 갖는 프로세스가 돌 수 있다.
13/ page replacement 1
현재 두개의 프로세스가 있고 유저 1,2은 4개의 페이지 구성. 피지컬 메모리는 8개의 프레임. 첫 번째 테이블은 4개의 페이지 중에 3개만이 파란색 위치에 할당. B,M은 할당안되고 하드디스크에 존재. 빈 프레임이 없다. 유저1이 load M 페이지를 수행하려고 한다. 1번째 페이지는 4번째 프레임에 있는데 M 로드를 하고 싶은데 M들어갈 데가 없다. 결국 기존 피지컬 프레임을 쫓아내야 한다. 이 상황이 페이지 교체가 필요한 상황을 설명.
14/
교체 정책의 큰 틀은 간단하다. 해당 페이징이 없을 적에 present bit은 0이 되고 피지컬 램이 다 쓰고 있다면 free frame이 없으니 확보해야 함. 누군가는 희생 victim frame. 희생 프레임을 잘 골라야. 정해지면 그 위치에 내가 지금 원하는 페이지를 갖다 놓고 해당 프로세스 page table을 업데이트. Page fault 핸들링 큰 틀이다.
실제 OS에서는 살짝 다르다. 페이지 폴트 오버헤드가 크기 때문에 이게 발생할 때마다 빈프레임을 확보하는 것이 아니라 유사시에 항상 빈 프레임을 확보하고 있다. 항상 어느 순간에 물리 메모리에는 일정 빈 프레임이 있다. 즉 무조건 빈 프레임이 있다.
15/
프로세스 페이지 폴트가 발생할 상황인데 페이지 폴트 핸들링이 수행되서 after 상황. Page table이 바뀜. F 번째 프레임을 쫓아내고 원하는 페이지를 갖다 놓겠다고 하면 f 프레임에 있는 이전 페이지가 victim이 되고 내 위 엔트리의 값은 그 위치에 대한 포인터가 된다. 위치를 저장하고 그 엔트리도 i로 바뀌고. 3(a) 따라서 그 페이지를 f번째에 적재하고 page table을 업데이트. Flag를 i에서 v로.
16/
OS 입장에서 Best victim page를 골라야 한다. 페이지 폴트 확률을 줄이는 선택을 best victim page. 현시점에서 가장 적합한 victim page는 뭐냐면 앞으로도 안 쓸 페이지. 그게 이론적으로 가장 best.
Belady는 현재 시점에서 가장 나중에 액세스가 된 페이지를 쫓으면 된다. 가장 나중에 활용될 페이지를 쫓으면 폴트를 줄일 수 있음.

The goal of the replacement algorithm is to reduce the fault rate by selecting the “best” victim page to remove
17/ page replacement algo.
전체 페이지 폴트의 rate를 줄이는게 목표. 최적 optimal은 만들 수 없고 그불구 다른 현실적 알고리즘들이 이론적인 정답에 얼마나 근접하는가에 대한 기준 제시. 알고리즘에 대해 일련의 input string을 넣는다. Reference string이라 하는데 프로그램이 진행함에 따라 몇 번째 페이지를 액세스 하는지에 대한 로그.
18/ Optimal Algorithm
현실적으로 구현 불가. 미래를 안다고 전제. 현실적인 알고리즘을 평가하기 위함. 현재 기준으로 가장 나중에 쓰일 페이지를 고른다. 4개의 프레임. 첫 번째 타임에 c page를 request. 근데 4번째 타임 슬롯에서 현재 a,b,c,d가 있는데 그 다음 e를 요청. A,b,c,d 중 하나를 쫓아서 e를 넣어야 하는데 가장 나중에 쓰일 것을 내쫓는다(d). 총 두 번의 페이지 폴트가 있다.
19/ FIFO
똑 같은 시퀀스 적용. 가정이 a,b,c,d가 있는데 a가 제일 먼저 d가 마지막이라고 가정. E를 액세스 하고 싶은데 들어온 순서에 의해 가장 처음 들어온 것을 쫓는다. 무려 5번의 페이지 폴트.
20/ FIFO 2
아주 치명적인 약점. Belady’s anomaly. 상식적으로 어떤 프로세스한테 어떤 프레임의 개수를 많이 할당하면 페이지 폴트가 줄 것이라 예상한다. 일반적인 알고리즘이 어떤 프로세스에게 페이지를 많이 할당하면 폴트를 줄일 수 있고 그 전제하에 많이 할당해주려 하는데 belady anomaly에 의하면 그렇지만은 않다. 프레임 하나 더 주고 똑 같은 시퀀스로 진행해보니 폴트가 더 발생.
21/ LRU
좀 더 현실적 알고리즘. 특정 알고리즘이 정설. 최근에 사용되지 않은 것. 처음 4개의 프레임.
3개로 폴트가 줄어들었다. 과거를 바탕으로 선택.

22/ LRU issues
어떻게 구현? Counter나 stack 이용. 액세스 시간을 기록한다. 하드웨어 카운터를 두고 카운터에 특정 페이지가 참조된 시간을 기록. 다른건 stack 개념. 최근 액세스 된걸 top으로 쌓는다. 젠가 블록처럼 중간에 빼서 위로 올림. Refer된 페이지가 항상 위에 있고 맨 아래 있는게 least recently used one.
23/ Stack
제일 아래에 있는 c빼고 e를 맨 위에 넣음.
24/ hardware support for replacement
LRU 구현. 페이지 테이블의 reference bit가 있다. 한 번 refer가 되면 비트가 1로. 이 비트를 활용하자. OS가 reset 할 수도 있음. LRU를 구현. 가장 이전에 사용한 페이지를 쫓아내는게 사실 최선일지 아닐지는 모름. 정확한 정답을 찾기 위해 많은 overhead를 생기게 하느니 그냥 하는게 낫다는게 approximate LRU. 현실적인 approach. Sample, clock 알고리즘 등이 있다.
25/ sampled LRU
이 기법의 특징은 reference byte를 모든 페이지마다 갖는다. R비트만 보여주는데 1이면 한 번이라도 참고가 되었다는 의미. 주기적으로 R비트를 검사한다. 그 R비트를 그대로 reference byte 앞으로 보냄. 페이지 테이블의 R비트를 그대로 MSB(맨 앞)으로 붙임. 한 비트 씩 밀린다. 그리고 R비트는 reset. 이 때 page fault가 발생하면 reference byte가 가장 작은 것(가장 이전에 쓰였다)을 내쫓음. LRU를 일정 기간 interval 동안 r 비트를 확인해서 reference byte의 값으로 정한다.
26/ clock algorithm
Second chance algorithm. R 비트를 기본으로 한다. 기본적으로 FIFO로 하는데 refer가 한 번 되면 기회를 한 번 더 준다. 시침이 한 번 돌 때까지 쓰였으면 안 내쫓음. Refer 되면 r비트가 1이 되고 page fault가 되면 쭉 돌면서 r 비트가 0인 것을 고른다. 쭉 스캔하면서 r비트가 0인게 나올 때까지 진행하고 스캔하면서 r비트가 1인 것을 0으로 리셋. 한 번 도는 와중에 한 번이라도 refer 되었으면 1로 바뀌어서 선택 당하지 않아.
27/ clock algo
다섯개의 페이지가 있고 각각의 박스가 엔트리. 두 번째 비트가 r 비트. Clock hand가 시계 방향으로 돈다. Page0은 r비트가 1이라서 active하니까 skip한다. skip하면서 이번엔 살려줄 테니 r비트는 0으로 바꿈.

28/ two handed clock algorithm solaris 2
같은 방향으로 도는 두 개의 clock hand. Front hand와 back hand의 간격은 고정. Front hand는 스캔하면서 r비트를 0으로 만든다. 뒤따라 가는 back hand가 r비트를 본다. r비트가 0인 것을 찾는다. Front와 back 사이에 페이지가 access 되면 r비트가 1. 그 친구는 살아남는다. Front와 back hand 가 계속 움직이면서 확인한다. Lotsfree는 시스템 변수인데 이 변수의 값에 따라 free frame을 확보하는 절차를 구할 것인가 아닐 것인가. 간격이 작으면 상황이 급박한거고 반대면 여유로움.
29/ counting algorithms
LRU 계열의 replacement 정책도 있지만 counting algorithm도 있다. Page당 카운터를 두고 카운터를 증가하는 것은 페이지가 참조된 경우. Page fault 발생시 어느 페이지를 희생시킬지 기준이 되는 것이 counter 값이다. 이 값이 가장 작은 페이지를 희생시킨다는 것이 LFU algorithm. 그 페이지가 적게 refer되었다는 것이고 반대는 MFU. 카운터 값이 큰 것을 희생시킨다. counter값에 의해서 victim page를 구하는 것. MFU는 왜?? 프로그램이 진행됨에 따라 초기 부분은 주로 초기화하는데 초기 counter값이 많이 증가한다. 초기엔 많이 refer되었지만 진행됨에 따라 안쓰이는게 많다. MFU를 쓰면 됨.
30/ allocation of frames
지금까지는 하나의 프로세스가 여러 페이지를 쓰고 page fault가 나왔을 때 어떤 페이지를 victimize 해야하는지. 지금부터는 프로세스에게 몇 개의 페이지를 할당하는지에 대한 이슈. 몇 개의 프레임을 할당하는 것이 타당할까. 기본적으로 가상 메모리를 쓰는 경우 논리적으로 프로세스의 모든 이미지가 물리 메모리에 할당이 다 안되고 부분적으로 페이지가 프레임에 로딩이 되면 동작할 수 있다는 것이 demand paging. 현실에서 프로세스가 진행하기 위해서는 최소한의 frame개수가 필요하다. Local과 global replacement 정책. Local은 어떤 프로세스가 일정량의 프레임을 고정으로 할당받고 고정받은 페이지 안에서 자체 해결한다. 내가 가진 프레임에서 교체하고 희생. 전제조건이 프로세스 당 정해진 개수의 프레임을 고정 할당 받는 것. 반면 global replacement는 어떤 프로세스가 page fault를 발생시켰으면 빈 frame을 할당 받을 때 심지어 다른 프로세스가 갖고 있는 frame을 뺏을 수 있는 정책. 이런 방법은 프로세스 당 고정 프레임을 fix하지 않고 프로세스 당 할당하는 frame을 가변으로 한다.
31/ local fixed allocation
아주 간단. 프로세스의 개수에 따라서 프레임 수를 나눔. Equal allocation. 프로세스 크기 상관없이 n분의 1. 프로세스 마다 이미지의 크기가 다를 수 있으니 그거에 비례해서 할당. 전체 프레임의 개수가 64개라 할 때 두 개의 프로세스(10, 127개 페이지로 구성)가 있으면 5와 59의 비율로 고정 할당. 이미지의 크기에 비례해서 전체의 physical frame을 나눈다. 할당 받으면 fixed. 페이지 폴트가 발생하면 local 자체적으로 해결.

32/ global allocation
우선적으로 자체 해결. 해결 안되면 다른 프로세스가 쓰는 frame도 뺏는 global 정책의 큰 틀. 결국은 runtime 프로세스에 몇 개의 프레임을 줄 것이냐 중에 global. 다이나믹하게 프레임의 개수를 조정.
**33/ thrashing 1**
알아야 할 현상 스레싱. 스레싱의 정의는 물리 메모리가 부족한 상황에서 각 프로세스가 유의미한 일을 하기 보다 page fault 처리하는 시간이 더 긴 경우. OS는 throughput을 높여야 되는데 CPU의 utilization을 봐야 하는데 cpu의 활용도가 낮으면 메인 메모리에 더 많은 프로세스를 부를텐데, 운영체제는 멀티프로그램의 정도, 동시에 concurrent하게 수행하는 프로세스를 많이 가지려고 한다. 어느 순간에 보니까 cpu의 활용도가 낮다. 너무 낮으면 하드디스크 프로그램을 프로세스화 시키면 concurrency를 높여 활용도가 높아지겠구나. 어? 근데 활용도가 더 낮아짐. 스레싱 현상의 원인. Cpu 활용도가 낮은 이유가 할 일이 없기 때문이 아니라 메모리 사용이 너무 dense 해서 page fault가 빈번. Page fault를 처리 하기 위해 cpu를 유의미한 계산에 못쓰는 상황을 

**thrashing: each process is busy moving pages in and out** 
34/ thrashing
Cpu 활용도를 높이기 위해 ram 프로세스 개수를 높인다. 포화 상태를 넘으면 물리 메모리 대비 active한 프로세스가 많고 각 프로세스가 진행하기 위한 빈번한 page fault가 발생하고 cpu가 활용 안됨. IO operation이 빈번. 커널이 오해해서 프로세스기 없어서 그런가 하고 더 많은 프로세스들여옴. 상황 더 악화. 가상 메모리를 구현하고 실행시킬 때 신경 써야 함.
프로세스의 locality 때문에 paging이 동작. 페이지 작동의 기본 이유는 locality. 프로세스가 진행함에 따라 locality가 변함. 스레싱이 왜 일어났는지 보면 여러 프로세스가 ram에 적재되어 있고 물리 메모리는 한정. 각각의 프로세스가 locality가 포함한 그 페이지만 갖고 있으면 각각의 프로세스는 원활하게 돈다. active하게 활용되는 page(locality) 합이 물리 메모리보다 크다면 스레싱 발생 가능.
35/ pattern
Memory reference locality.
36/ working set
Working set 이 중요한 키워드. T1에서의 working set은 5개의 페이지로 구성. T1시점에서 과거 델타 동안 참조된 서로 다른 페이지의 아이디. 개수는 5개. 똑같은 프로세스지만 t2에서 보면 working set은 두개로 줄어든다. 3,4가 번갈아 가며 많이 쓰였기 때문. 동일한 프로세스이더라도 어느 시점에서 active하게 acces되는 페이지가 다르다. T1에서는 다섯개의 페이지 프레임을 주면 되고 t2에서는 2개만.
37/ working set model2
WSS는 델타 시간동안 reference된 총 페이지 개수. (집합 원소 개수). 델타를 어떻게 정할 것인가?
만약 델타가 너무 작다면 프로세스의 지역성을 완벽히 커버할 수 없다. 반면 델타가 너무 크면 여러 locality를 커버해서 프레임 낭비 예상.  n개의 프로세스가 필요한 실제 프레임의 개수가 D. 현실은 D가 물리 메모리의 프레임 개수보다 크다면 thrashing. 10개 프로세스가 필요한 locality 커버를 필요한 프레임의 개수가 80개라면 스레싱 발생. OS의 정책에 다라 스레싱 피한다. 각 프로세스의 working set을 잘 관찰하거나 swap out 시켜서 스레싱 예방. Throughput도 늘릴 수 있다.
38/  keep track of ws
델타를 10000이라고 가정. Working set을 구하기 위해서 인터벌 타이머를 5000타임 유닛마다 인터벌을 준다. 두 번의 인터벌 타이머가 동작. 5천번 메모리 액세스 동안 r비트에 기록. 5천번의 레퍼런스가 끝나면 인터벌 타이머가 들어오고 r비트를 그대로 카피해서 메인메모리에 기록한다. 페이지당 2비트. 1번째 비트는 1번째 5천번에 대한 r비트. 페이지 당 두 비트의 추가 메모리 필요.
두 비트 중 하나만 1이면 델타 10000인 동안 워킹 셋에 들어가 있다. 완벽하게 정확한 워킹 셋은 아니다. 메모리 액세스 마다 기록해야 하는데 이것은 overhead가 너무 크다. 정교한 워킹 셋을 구하려면 메모리 비트를 더 할당한다. 10비트로 할당해서 인터벌 타이머를 1000마다. 누적된 r비트를 메인메모리에 카피한다.
39/ page fault frequency allocation
시스템은 페이지 폴트 rate를 알 수 있다. 페이지 폴트가 너무 많으면 그 프로세스에게 할당된 프레임이 적다. 페이지 프레임을 더 주면 된다. 어떤 경우는 페이지 폴트 비율이 낮은 경우. 프레임을 다 활용 못하고 있다. 이런 상황에서는 프레임을 뺏는다. 내가 정한 threshold보다 크면 프레임을 더 할당, 적으면 프레임 수를 줄인다. 이런 기법을 MS windows가 채택.
40/ other issues
기본 가상 메모리 이슈와 연관된 자잘한 이슈.
41/ prepaging
미리 예측해서 내 프로세스가 앞으로 활용할 페이지를 물리 프레임에 갖다 놓겠다. 페이지 폴트가 발생하면 값비싼 harddisk op를 동반한다. 그래서 페이지 폴트를 줄여야 한다. 하지만 필연적으로 피할 수 없는데 내 프로세스가 쓰게 될 페이지를 예측한다면 페이지가 속한 프로세스 이미지를 물리 프레임에 미리 갖다놓는게 prepaging.
알파는 0에서 1. 알파곱하기에스 만큼 페이지 폴트 발생. 알파가 커질 수록 prepaging 확률이 커지면서 도움이 된다.

42/ page size
인텔 아키텍쳐는 4kb의 페이지 사이즈를 사용한다. 어떤 기준으로 페이지 크기를 정할 것인가? 페이지 크기에 따른 장단점. 페이지 크기를 작게 할 경우 장점은 페이지에 들어가 있는 internal frag가 작다. 작은 페이지 크기를 쓰면 상대적으로 쪼가리 메모리가 작은 장점. locality가 정해져 있을 때 작은 페이지 몇 개로 커버. 실제 메모리 활용도를 높이는 장점. 반면 페이지 테이블 entry 개수가 많다. 페이지 폴트가 날 확률이 크다. 큰 페이지 테이블을 쓰면 장단점 바뀜.
43/ TLB reach
Tlb size(entry 개수)와 page size의 곱이 tlb reach. reach를 크게 하면 빠른 시간에 많은 로지컬 메모리 커버. Ws을 tlb reach가 커버하면 좋다. 크게 하는 법? Page size를 크게 하거나 tlb size를 크게. 페이지 사이즈를 여러 개 섞어 쓴다. Ultrasparc는 네 개의 다른 페이지 사이즈를 쓴다. 4메가바이트의 페이지는 커널 코드를 담을 때 쓴다.
44/ program structure
우리가 짜는 프로그램 구조에 의해서도 페이지 폴트가 많거나 적어진다. Program1은 j안에 i가 있다. 매 루프마다 한 번씩 페이지 폴트. 반면 program2는 i안에 j.
45/ IO interlock
페이지는 IO 작업이 끝날 때가지 수행이 안되는데. Lockbit1이면 page out대상이 안된다. replacement대상 안되게. 근본적인 방법이 user memory에서 IO 수행하는 것이 아니라 kernel이 대행하는 시스템 콜을 사용.
46/ benefits of VM
Fork와 같은 시스템 콜을 쓸 때 VM을 쓰면 더 효율적이고 file을 VM과 연결시키면 효율적으로 구현 가능.
47/ copy on write
기본적으로 COW라는 semantic. fork를 통해 child가 만들어지는데 fork는 parent의 이미지를 그대로 copy. 오버헤드가 워낙 커서 이 것을 가볍게 하자는 의도. 기법 중 하나가 COW. child process에 대한 page table를 만들고 실제 child process에 속한 이미지를 아직 copy하지 않음. 만약 parent가 child를 fork한 뒤 만약 fork 한 후에 parent와 child의 프로세스가 global data를 access 하는데 둘 다 쓰지 않고 읽기만 한다고 가정. Parent가 갖고 있는 global data를 카피할 이유가 없다. Code도 카피 할 필요가 없다. Write op하는 순간에 실제 이미지를 copy.
48/ memory mapped files
하드디스크에 있는 파일이 물리 메모리에 매핑되어 있다. 파일 일부분을 메인메모리에 매핑하고 cpu가 액세스 할 때 메인메모리의 내용을 쓰겠다. 매핑의 단위가 물리 메모리가 쓰는 페이지. 파일을 페이지로 쪼개고 매핑하고 cpu가 액세스 하는 방식. 이렇게 하면 밑줄 부분 참고. File IO를 하면 system call을 쓰는데 시스템 콜을 쓰면 커널 모드로 가서 해당 파일을 access하고.
49/
곧바로 물리 메모리를 쓸 수 있기 때문에 바르게 데이터를 사용 가능
50/ VM Ds in Linux
리눅스에서 가상 메모리를 어떻게 구현하는가. Virtual address space 4휴 리니어 주소. 에리아 1, 2. 이미지다. 각각의 에리아가 프 커널 자료구조로 정의.
51/ page fault handing in linux
메모리 region 당 하나의 vm 구조. 1,2,3번에서 페이지 폴트가 발생하는 경우. 1번 상황은 프로그램이 돌아가는데 어떤 이유로 illegal. 회색 부분만이 legal 한데 1번은 흰색 부분을 읽겠다고 하는게 page fault. 모든 프로세스가 만들어내는 주소가 회색 부분에 있어야 하는데 없다는것을 발견하고 excaption 발생. 예를 들어 포인터를 잘못 활용할 때. 세그멘테이션 폴트 에러. 두 번째도 페이지 폴트인데 valid 주소 공간을 access 하는데 MMU가 page fault를 만들었다. 이 경우 access write violation. Read only인데 코드를 변경하려고 하니 페이지 폴트. 3번 케이스도 페이지 폴트. 정상적으로 페이지 폴트가 발생. 해당 페이지가 물리 메모리에 없는 경우. 해당 페이지를 프레임에 찾아야 하는데 없으면 exception handler가 해당 페이지를 갖고 와서 업데이트.
52/ page replacement
LRU lists. 실제 OS에서는 페이지 폴트가 발생시 빈 프레임을 확보하는게 아니라 항상 빈 프레임이 있다. 커널 판단하에 빈 프레임이 없다면 replacement를 해서 빈 프레임을 확보한다. 구체적인 절차가 진행. 빈 페이지 풀을 갖고 유지하고 있다.