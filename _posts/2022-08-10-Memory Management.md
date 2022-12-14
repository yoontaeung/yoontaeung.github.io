---
title: Memory Management
categories: [Computer Science, OS]
date: 2022-08-09 12:00:05
tags: [cs, os]     # TAG names should always be lowercase
author: <author_id>
comments: true
---
<style>
    .background {
        background-color: aquamarine;
    }
    .font {
        color: red;
    }
</style>
# L8. Memory management

OS Lecture 8 memory management

프로세스가 ram에 존재하는데 ram을 어떻게 관리할 것인가? Lecture 8,9에서 다룸. 메모리 관리에 대한 기초적인 지식, 관리 정책을 배울 것이다. Simple paging, segmentation 그리고 modern OS의 virtual memory를 다룸.

**Summary**

<span style = "color: red">**Processes use logical address**  
**“OS+hardware” translates logical address into physical addresses**  
**Various techniques**  
**Fixed partitioning: easy to use, but internal fragmentation**  
**Dynamic partitioning: more efficient, but external fragmentation**  
**Paging: use small, fixed size chunks, efficient for OS**  
**Segmentation: manage in chunks from the user’s perspective**</span>

Combine paging and segmentation is better. 

<span style = "color: red">**3/ memory management**</span>

왜 메모리 관리가 필요한가? 스케줄링, 타임 정책을 통해 여러 프로그램을 concurrent 하게 수행시켜야 한다. ram이라는 공간에 여러 개의 프로세스를 수행해야한다. 프로세스간 protection을 지켜야 한다. 내가 다른 프로세스 혹은 다른 프로세스가 내 주소 공간을 침범하는 것을 막는다. 동시에 여러 프로세스가 돌다 보니 메모리 하드웨어를 적절히 업데이트 해야 한다. 주소 변환, 보호이슈 등. 이 과정이 아주 효율적으로 수행되는 메모리 정책을 고안해야 함.

메모리 관리의 정의는 운영체제와 하드웨어가 같이 하는 것. 여러 프로세스를 메인 메모리에 갖고 와서 돌리기 위해선 하드웨어와 OS가 같이 일을 하는데 이 과정을 memory management라고 한다. 

<span style = "color: red">**프로세스 간 주소 공간의 독립성을 확보**</span>해야 하고, <span style = "color: red">**여러 프로세스가 경쟁하며 주기억 장치의 리소스를 쓰기 때문**</span>에 allocation, 재할당, <span style = "color: red">**최소한의 overhead**</span>로.

4/ M M 2

여러 프로세스를 concurrent하게. 연속적인 주소 공간을 보장. 근데 주소 공간이 다 다르다. 어떤 경우는 피지컬 메모리보다 더 큰 주소 공간을 갖는 프로세스가 존재 가능. 이런 경우 

<span style = "color: red">**어떻게 큰 프로세스를 적은 물리 메모리 상에서 돌릴까?** </span>

CPU가 어느 시점에 특정 프로그램 카운터를 load, fetch, store등. 피지컬 메모리가 전부 다 있을 필요는 없다. 공간상, 시간상 지역성이 있는데 이는 9장에서. 이론적으로 피지컬 메모리보다 더 큰 프로세스를 수행할 수 있다. 하나의 프로세스가 코드 이미지, 힙, 스택 등 다양한 이미지를 갖는데 효율적으로 각각 관리. 라이브러리는 여러 프로세스가 공유할 수 있기 때문에 이런 모든게 효율적으로 빨리 도는 성능 이슈.

<span style = "color: red">**5/ address space**</span>

물리 어드레스 공간, 논리 주소 공간. 물리 주소 공간은 하드웨어가 갖고 있는 물리 메모리의 공간. 실제 physical memory의 크기. 논리 주소 공간은 프로세스 관점의 메모리. 우리가 짠 프로그램이 컴파일 되어서 힙, 스택, 이미지로 존재하고 이미지에 대한 공간을 논리 주소 공간이라고 함. 그림 상 녹색 표시된 이미지가 우리가 짠 거고 논리 주소 공간이 된다. 상대적인 주소 0, Max program. 철저하게 프로세스 이미지 관점. 혼동하면 안되는게 CPU가 PC를 증가시켜 가져오고 저장하고 뭐 그런 것들을 통해 이미지를 액세스하는데 우리가 수행하는 Process의 PC나 Stack pointer가 논리 주소 공간에서 움직일까? 물리 주소 공간에서 움직일까? 유저 프로그램은 논리 주소 공간을 다루는 것. 유저 프로그램은 물리 주소를 볼 수 없다. CPU내 MMU라는 하드웨어가 변환시키는 것. 물리 메모리 주소로 변환. 변환을 도와주는 기능이 커널의 memory management. <span style = "color: red">**CPU에 의해 수행되는 명령어는 물리 메모리가 아니라 논리 주소**</span>. 프로세스를 구성하는 이미지가 아키텍쳐에 의해 최대 사이즈가 정해진다. 프로세스가 ram에 올라가야 동작할텐데 변환해야 함. 가상메모리나 간단한 매니지먼트를 통해 논리 주소를 물리 주소로 변환. 프로세스가 돌아갈 때는 모두 논리 주소임을 염두.

The user program deals with logical address. 

It never sees the real physical address. Instructions executed by the CPU issue “logical address”

6/ address generation

이런 코드가 있을 때 컴파일, 어셈블러 거쳐 linking 작업 통해 코드와 기존 라이브러리를 엮음. 그 후 논리 주소를 만들어내고 이미지를 물리 메모리에 loading. CPU가 만드는 PC는 논리 주소고 실제로 메모리에 액세스 할 땐 액세스 실제 주소 공간은 MMU와 하드웨어가 같이 일해서 물리 주소를 알게 된다.

![Screen Shot 2022-08-02 at 12.01.40 PM.png](/assets/img/Screen_Shot_2022-08-02_at_12.01.40_PM.png)

7/ Hardware for address translation

여러 프로세스가 존재하기 때문에 protection이 중요하다. 모든 CPU 아키텍쳐는 몇 개의 레지스터를 갖는다. 런타임에 프로세스가 액세스 하는 논리 주소를 물리 메모리로 변환해서 로딩된 위치를 찾는다. 논리 주소를 벗어나는지 물리 메모리에 할당된 위치를 벗어나는지를 runtime에 확인. 크기가 500을 넘어가면 안되는데 크기를 벗어나면 exception,

로지컬 주소가 물리 메모리로 로드 되어서 액세스 될 때 시스템에 존재하는 레지스터의 확인한다 정도로 이해.

![Screen Shot 2022-08-02 at 12.01.07 PM.png](/assets/img/Screen_Shot_2022-08-02_at_12.01.07_PM.png)

<span style = "color: red">**8/ swapping**</span>

메모리 관리할 때 흔한 개념. Ram 메인 메모리와 storage. 램은 cpu가 액세스하는 공간이고 storage는 데이터, 프로그램을 저장하는 비휘발성 공간. Ram을 보면 OS가 자리잡거나 유저 프로세스가 로드된 상황인데 메인 메모리가 여유가 없으면 멀티프로세싱하기 위해서는 특정 프로세스를 임시로 storage로 뺄 수 있음. 녹색 부분 프로세스를 잠시 멈춘 후에 그 이미지를 전체 backing store, hard disk로 뺄 수 있음. 메인메모리가 비게 됨. 빼는 과정을 swap out, 들어오면 swap in. 이 기법을 swapping. 초창기 운영체제를 만들 때 워낙 메인 메모리가 부족해서 swapping을 자주 활용. 요새는 메인메모리 용량이 커져서 빈도가 줄었다.

9/ Simple memory management

다시 해석하면 간단하다는건 <span style = "color: red">**가상 메모리를 쓰지 않는 관리를 simple management**</span>. 수행중인 프로세스는 전체 RAM에 통째로 로드되는 매니지먼트를 얘기한다. 프로세스 이미지가 전체로 로딩이 되는 메모리 관리를 어떻게 할 것인가. 이런 기법을 바탕으로 high level의 고급management를 구현하는데 이는 microprocessor firmware 같은 저사양은 가상메모리 쓰는건 부담스럽고 기본적 형태의 메모리 관리 기법을 많이 활용한다.

<span style = "color: red">
**Methods**  
1. **Fixed partitioning**  
2. **Dynamic partitioning**  
3. **Simple paging**  
4. **Simple segmentation**  
</span>

10/ <span style = "color: red">**Fixed partitioning 1**</span>

고정 파티션. 물리 메모리를 어떻게 쪼갤 것인가? 어떻게 공유할지 보니까 고정방을 만들어서 각각의 방에 특정 프로세스를 수행하면 되지 않겠는가. 물리 메모리를 8mb단위로 쪼개면 8개의 8메가 방. 아니면 크기를 달리해서 고정해서 방을 나눔. “고정”이 핵심.

11/

방을 미리 만들어두고 runtime의 어떤 프로세스가 main memory를 쓴다 하면 프로세스 메모리 크기보다 큰 방을 할당해주면 된다. 파티션보다 워낙 내 프로세스가 크기 때문에 어떠한 파티션도 할당 못 받을 수 있음. Overlays 활용. 쉽게 구현하는 장점이 있지만 방을 고정했기 때문에 단점 有. <span style = "color: red">**Internal fragmentation**</span>을 피할 수 없다. 100% 메모리 낭비. 방의 크기를 다르게 쪼개도 빈도야 줄겠지만 내부 fragmentation이 존재한다.

12/

13/

unequal하게 방이 쪼개질 땐 고민 있음. 프로세스 방을 할당할 때 각각의 방마다 queue를 둔다. 프로세스 이미지 크기를 기초로 해서 가장 적절한 방의 큐에 넣는다. 내 프로세스 이미지 보다 살짝 큰 방을 택한다. 내부 fragmentation이 가장 적은 방을 선택한다. 한 번 큐가 되면 그 프로세스는 그 방에서 수행. 여러 큐를 유지해서 가급적 메모리 활용도를 높이는 그런 철학. 최악의 경우 작은방의 큐에만 길게 있을 수 있다. 큰 방이 노는 경우.

14/

모든 방을 관장하는 하나의 큐를 둔다. 싱글 큐를 둬서 프로세스를 특정 방에 할당하게 되면 앞 슬라이드에서 봤던 메모리 소스를 극단적으로 활용하는 것을 피할 수 있다.

![Screen Shot 2022-08-02 at 3.54.45 PM.png](/assets/img/Screen_Shot_2022-08-02_at_3.54.45_PM.png)

<span style = "color: red">**15/ dynamic partitioning**</span>

Each process is allocated exactly as much memory as it requires

Internal fragmentation이 나오는 고정 파티셔닝. 이걸 없애는 방법? Dynamic partition. 하지만 작은 방들이 많이 생기는 <span style = "color: red">**external fragmentation**</span> 발생 가능. 한쪽으로 쫙 밀어서 가운데 공간을 만드는 기법인 compaction도 있는데 미는 작업이 많은 overhead가 있다.

16/

P4가 들어왔는데 128K를 원한다. P4를 할당할 공간이 없다. 기존 프로세스를 swap시키고 p4 들여보냄. P2를 하드디스크로 swap out. 96K, 64K가 남음. P1이 수행이 끝나고 P2를 다시 들여옴. 이 다음에 들어오는 프로세스가 64K보다 크면 절대 마지막 공간(64K)는 사용되지 않음. 이런 상황 피해가지 못한다.

![Screen Shot 2022-08-02 at 3.54.22 PM.png](/assets/img/Screen_Shot_2022-08-02_at_3.54.22_PM.png)

17/

커널 관점의 고민거리가 있다. 프로세스를 할당할 수 있는 여러 공간 중 어느 공간에 할당하는게 제일 좋을까? 예측의 문제. 정답은 모름. Placement algorithm이라고 한다. 어떤 메모리 공간에 할당할 것인가. 여러 개의 알고리즘 등이 있다. 직관적인 알고리즘. before에서 흰공간이 비어있는 메모리 영역. 화살표 포인트 바로 전에 커널이 할당한 마지막 블록이라 할 때 16K의 이미지를 갖는 프로세스를 시스템에 들인다. 어떤 하얀 공간에 place할 것인가? 4가지 정도의 알고리즘. 이 상황에서 best fit은 빈 공간 중에 가장 꽉 끼는 할당 후 extra fragmentation이 가장 적은. First fit은 가장 처음에 걸리는 것. Next fit은 이전에 할당된 곳에서부터 시작해서 내려감. Worst fit도 있다. 할당하고 나서 공간이 가장 많이 남는 것을 선택하는게 뒤 프로세스가 수행할 공간의 여지를 주기 때문에 worst fit도 생각해볼 수 있다. 상황마다 적합하게 알고리즘 활용.

<span style = "color: red">**18/ Fragmentation**</span>

Fragmentation은 피할 수 없다. Fixed, dynamic 모두 fixed는 내부 쪼가리 fragmentation이슈, dynamic 파티션은 external fragmentation인데 전체 피지컬 메모리에 각각 색깔이 각 프로그램을 나타낸다. P5는 연속된 공간 3개가 필요한데 들어갈 수가 없다. P5가 만약 1.9의 이미지를 갖고 있다면 2 공간에 할당하고 0.1은 활용못하는데 이걸 internal fragmentation.

external - total memory space exists to satisfy a request, but it is not contiguous

internal - allocated memory may be slightly larger than requested memory

<span style = "color: red">**19/ compaction**</span>

한 쪽으로 밀면 공간 확보 가능. P2를 쭉 위로, P3를 쭉 아래로 밀면 한 가운데 5라는 빈 공간이 생긴다. OS가 구현하기에는 overhead가 크다.

<span style = "color: red">**20/ overlays**</span>

메모리 공간이 없을 때 프로그래머가 의도적으로 여러 모듈로 쪼개 넣었다 뺏다. 서브루틴 function A, B가 있고 B내부에는 다시 C,D. 이걸 call graph로 나타내면 오른쪽 그래프. 물리 메모리가 아주 협소하다. P, a, b, c, d를 다 담으면 좋은데 그러기엔 물리 메모리가 부족. 처음 P이미지를 로드하고 B는 A다음 수행되니까 동시엔 수행 안됨. A 다하면 B 넣고 C 끝나면 D 넣고. 이 기법을 overlay. 60,70년대에 사용.

<span style = "color: red">**21/ Buddy system 1**</span>

Fragmentation이슈. 간단하지만 괜찮은 메모리 관리 기법인 buddy system. 커널이 활용하는 메모리 관리 기법을 KMA. 이것의 기본이 버디 시스템. 고정, 가변 파티션은 이론적인 이야기이고, buddy system은 리눅스 커널에서도 쓰이는 기법이다. 고정, 가변 파티션의 단점을 줄여보자.

<span style = "color: red">**버디 시스템 좋긴 하지만 현대 OS의 VM based paging, segmentation이 훨씬 좋음**</span>

2^n. 메모리 크기가 2의n승 사이에 있는가? <span style = "color: red">**메모리 블락은 항상 2의 k승 단위로 할당하겠다**</span>

22/ buddy 2

알고리즘. 가용 가능한 메모리 크기가 2의 u승. 공간의 요청과 해제가 들어올 때 어떻게 진행? 프로세스가 s 크기의 메모리를 원한다. 2^(u-1)보다 크고 2^u 보다 작거나 같은. S가 1000이면 512 초과 1024 이하. 이럴 경우 S에게 1024를 통째로 준다. 이런 경우 24의 internal frag. 생김. 그거 보다 작으면 U를 반으로 쪼개서 재귀적으로 이 꼴을 만족할 때까지 split. 프로세스가 return 하면 return 후에도 조정이 된다. 연속된 조그만 두 개의 빈 블록을 그냥 두는게 아니라 merge해서 한 단계 큰 블록을 만드는 알고리즘도 있다. 예제로 설명.

23/ 3

1메가바이트의 큰 블록. 어떤 프로세스 A가 100k의 메모리를 원한다. 버디 시스템에 따르면 128k 하나 주고 3개의 빈 블록 존재. 100을 넣기 위해 1메가를 두 개로 쪼개고 범위안에 들어오는지 확인. 512를 두 개의 256으로 쪼개 보고 하나의 256이랑 또 비교. 첫 번째 128k를 가지고 또 고민. 100k를 더 이상 쪼개지 않고 줘도 되겠다. 물론 28k는 internal frag. 이 상황에서 b가 240k를 요청했다고 가정. 세 개의 공간 중에 240k를 할당할 때 2^n-1 2^n 범위안에 들어오게. 256k를 그대로 할당해준다. 거기에 b블록 할당해준다. 64k를 요청 받았을 때도 똑같이. 그 후에 release b, a 하면 놓아주고, e가 75k를 또 요청함. 그 후 C가 release되면 똑 같은 크기의 64k 두 개가 비었다. 이럴 땐 64k 두 개로 두는 것이 아니라 128k로 merge. 시스템 관점에서는 큰 블록으로 유지하는 것이 좋으니까 기회가 되면 merge해서 큰 블록 만든다. 버디 시스템의 또 다른 알고리즘. Release E 하면 merge 두 번 연속으로 해서 512k 만들었다. 마지막으로 release D도 같은 케이스.

실제 버디가 정확히 정의가 되어야 함. 어떤 프로세스가 release 될 때 오른쪽도 왼쪽도 똑 같은 크기라면 누가 buddy인지 정확히 정의 되어야 함. 쭉 binary로 스플릿 될 때 같은 parent끼리 버디. 옆에 있다고 다 buddy가 아니라 split될 때 buddy가 된다.

<span style = "color: red">**24/ Paging (1)**</span>

가상 메모리 기법과 관련된 paging. 로지컬 주소 공간이 물리 주소 공간에 연속적으로 mapping 되는 것을 가정. 페이징 기법의 핵심적인 특징은 물리 주소 공간이 noncontiguous. 지금까지는 물리 공간이 연속적으로 맵핑이 되어야 하는데 이 제약이 사라짐. 여러 장점이 생긴다. 앞서 얘기 한 external frag 등 여러 이슈 해결. 물리 메모리 공간을 똑 같은 크기의 frame으로 쪼갠다. 물리 주소 공간을 크기 512, 1000,,, 바이트로 나누고 이를 <span style = "color: red">**frames**</span>. 유사하게 논리 공간도 똑 같은 크기로 자르는데 이 단위를 <span style = "color: red">**page**</span>로. 어떤 페이지가 어떤 프레임에 매핑 되었는지 tracking. 어디에 빈 프레임이 있는지 알고 특정 페이지를 매핑. n개의 페이지로 구성된 프로그램이 있을 때 물리적으로 n개의 빈 프레임이 있어야 한다. 물리적으로 확보된 n개의 프레임들이 연속적일 필요는 없다. 논리 주소를 물리 주소로 바꾸는데 필요한 <span style = "color: red">**page table**</span> 메타 정보를 만들어야 한다. 이 과정에서 모든 논리, 물리 공간을 동일한 크기로 자르기 때문에 특정 페이지 안에서 <span style = "color: red">**내부 fragmentation은 존재 가능**</span>.

25/ paging 2

프로세스의 가상 주소 공간을 page 단위로 나눔. 이 그림은 5개의 page로 쪼개져 있음. 핑크색 부분이 특정 주소로 access 할 내용을 의미. 이런 경우 핑크 부분 주소를 일차원으로 표시할 수도 있고 다르게 하면 (p, o)로 표시 가능. 첫 번째 인자는 아래에서 몇 번째인지 o는 offset처럼. 아래 그림이 virtual address의 개념. (p, o)이고 몇 번째 주소인지 얘기함.

26/

물리 메모리를 frame으로 쪼갠다. Base 주소로부터 3번째, 6번째 offset 이런식으로. (f,o)

27/

Paging은 연속적인 주소 공간으로 된 page들을 물리적으로는 불연속적으로 배치할 수 있다. 왼쪽에 두 개의 page가 있다. (p1, o1)과 (p2, o2)가 물리 메모리에 각각 맵핑. 로지컬한 모든 process 주소 공간에 속한 모든 페이지가 피지컬 메모리에 전부 매핑이 되지 않아도 괜찮다. 가상 메모리의 기본.

![Screen Shot 2022-08-02 at 4.34.20 PM.png](/assets/img/Screen_Shot_2022-08-02_at_4.34.20_PM.png)

28/ VA translation

특정 페이지를 특정 프레임에 매핑하고 그 매핑 정보를 page table에 저장. 20비트의 주소 공간을 갖는 process이 있는데 하위 10비트를 페이지 크기(1024바이트), 상위 10비트를 페이지의 넘버(index). 상위 p 10개 비트 내용을 가지고 page table의 인덱스로 쓴다. p번쨰 entry안에 f라는 넘버가 있다. P page가 어느 피지컬 메모리에 매핑 되었는지에 대한 정보. (p,o)가 (f,o)로 변환. 몇 개의 페이지 테이블의 엔트리가 있을까? 페이지의 개수가 2의 10승 1024개. 하나의 페이지 크기는 1024 바이트. 이 상황에서 페이지 테이블의 엔트리 개수는 1024개. 페이지 테이블의 개수는 프로세스가 갖고 있는 페이지 개수와 같다.

29/ ex

16비트로 표시된 로지컬 주소 공간을 움직이는 프로세스. 물리 공간은 15비트로 표현된다. 로지컬은 2의 16승 64k, 물리 메모리는 2의 15승, 32k. 5개의 page에 대한 왼쪽 그림. 연속된 두 개의 주소(핑크)는 (3, 1023)과 (4,0)은 로지컬하게 연속적인 공간이지만 다른 페이지에 속함. 물리적으로는 완전히 다른 공간에 매핑될 수 있는데 이 것이 page table의 내용. (3, 1023)은 3번째 페이지의 1023번쨰 offset이다. 3번째 위치 녹색 점선은 f 넘버. 그대로 물리 메모리로. 실제 프레임은 4번째에 있다. 비슷하게 (4,0)은 f넘버 0. 0번째 페이지의 0번쨰 위치가 두 번째 핑크색. 녹색 외 10이라는 부분은 다음 슬라이드에서.

30/ page table structure

Paging을 쓸 때 반드시 지켜야 할 것은 프로세스 당 하나의 페이지 테이블을 가져야 한다는 점. 1000개의 프로세스가 있다면 운영체제는 1000개의 다른 페이지 테이블을 유지. 페이지 테이블의 구체적인 내용은 뭐가 있을까?  핵심적인 정보가 f 넘버. 또 bit flag가 있다. 비트 마다 의미를 부여한다. 어떤 비트는 present, dirty, reference, protection 등. Page의 state를 표시한다. 오른쪽 그림은 추상적인 개념이 실제 하드웨어에서 어떻게 구현되는지 예시. 인텔 아키텍쳐를 쓰는 경우 하드웨어가 제공하는 page table이 있고 page table entry의 실제 내용. 상위 20비트는 f 넘버로 쓴다(base address). 하위 12비트는 이러저러한 용도로 사용. Dirty 비트는 페이지의 내용이 수정되었는지 여부.

<span style = "color: red">**31/ page sharing**</span>

특정 이미지(shared library 등)를 공유할 수 있다. 각각 프로세스 페이지 테이블에 libc.a에 있는 page를 넣어준다(?).

32/ Page table implementation

페이징의 가장 큰 특징은? <span style = "color: red">**논리적으로 연속적인 주소 공간이 물리적으로는 반드시 연속적일 필요가 없다.**</span>  External frag 를 없앨 수 있다. 임의의 페이지를 임의의 빈 프레임에 매핑하면 되기 때문에. Internal frag는 존재할 수 있음. 아주 큰 물리 메모리에 프로세스가 갖는 모든 페이지를 로드할 필요도 없다. 이런 장점 때문에 page table를 만들고 translation 과정을 거쳐야 함. 감수할 희생이 있다. 

Overhead가 있는데 크게 두 가지. 

첫 번째는 성능 이슈. 어떤 성능의 이슈? 페이징을 쓰지 않으면 logical 주소가 물리 메모리로 매핑 되어 있어서 cpu가 특정 주소에 있는 코드, 데이터를 한 번의 메모리 access로 가져올 수 있음. 페이징을 쓰면 논리 주소를 물리 주소로 바꿔야 하는 선행이 필요. (f, o)로 실제 메모리 읽어냄. 두 번의 메모리 액세스. 한 번은 page table 액세스 해서 f넘버 얻고 물리 메모리로 실제 데이터나 명령을 access 해야 하는 두 번의 과정을 거쳐야 한다. 프로세스가 돌아가는 속도가 두 배로 느려짐. 성능이 감소될 우려.

어떻게 페이징을 통한 overhead를 줄일 수 있을까. 방법이 아주 빠른 메모리를 두는 것. **TLB**라는 개념. TLB를 associative메모리로 구현. 특별한 하드웨어를 써서 아주 빠르게 논리에서 물리로 변환.

<span style = "color: red">**33/ Associative memory**</span>

병렬 서치를 가능하게 하는 메모리 구조. Content-addressable 메모리(cam) 키 값을 갖는 여러 entry. 해당 key를 다 search해야 함. Associate 메모리로 구현하면 key만 준다면 동시에 여러 key 값을 검색해서 value값 도출. 주소 변환을 목적으로 하는데 논리 p넘버를 주면 p에 해당하는 f 넘버를 한 번에 제공받겠다. TLB 개념의 핵심. 이런 associate 메모리를 만드는데 비용이 든다. Register 구현, 서킷,,, 등등 비쌈. 논리 주소를 물리 주소로 바꾸는 변환을 아주 빠르게 하자는 것이 핵심 아이디어.

34/ TLB

Page table은 그대로 있고 프로세스의 모든 페이지에 대해 각 엔트리를 갖고 있음. 이 앞에 TLB는 (p, f)에 대한 pair. TLB에 몇 개의 엔트리가 있는지는 가격에 따라 다름. 많이는 없음. Key가 주어지면 한 번에 f 넘버를 얻을 수 있음. P가 어떤 f에 있는지를 찾아야 하는데 TLB를 들여다 본다. 내 p로 시작되는 key가 있는지 검색. TLB안에 p,f 페어가 없다면 page table를 조사해서 f를 얻어서 이전처럼 f,o를 구성한다. 이 내용을 다시 TLB에 업데이트. TLB lookup overhead가 생길 수 있음.

35/ Effective Access Time

성능을 고민. TLB loopup time을 e time unit. 엡실론 타임은 1ms보다 훨씬 작음. Hit ratio는 p라는 페이지 넘버가 TLB안에 있을 확률을 의미. 매번 p를 만들어낼 때 p가 TLB안에 있는 확률 a. EAT를 계산할 수 있음. 평균적인 논리 주소가 있을 때 실제 메모리를 통해 얻을 수 있는 성능. 모든 경우를 다 고민. EAT가 hit 할 때 와 없을 때 1-a. a일 경우 엡실론 타임과 1을 더한 값.(hit 할 때 메모리 액세스 타임). 그렇다면 반대의 경우 2+e? 원래 두 개의 메모리 액세스를 통해 명령을 가져와야 하기 때문에 2+e. 엡실론과 알파의 게임. 엡실론을 줄이기 위해서는? 하드웨어 기술. 알파를 높이는 방법은 TLB의 크기를 늘리는 방법.

36/ Handling Large Page table (1)

성능 감소의 문제가 paging에 있는데 해결을 위해 TLB 하드웨어 도입, a값 올리는 노력. 또 다른 이슈는 페이징 기법을 쓰는 경우 프로세스 마다 하나의 페이지 테이블이 있다. 하나의 프로세스에 대한 크기를 생각해보자. 가령 32비트 아키텍쳐에서 페이지 크기를 4k 2의12승으로 설정할 떄 프로세스가 가질 수 있는 페이지의 maximum이 얼마나 될까요? 페이지 크기를 4K로 할 때 프로세스가 가질 수 있는 페이지가 몇 개? 100만개. 2의 32승 즉 4기가 나누기 4k, 한페이지 당 보통 4바이트를 할당하기 때문에 페이지 크기는 하나 당 4메가 바이트. 이런 프로세스가 1000개 있다. 물리 메모리가 1기가, 2기가, 512메가 밖에 없는데 페이지 테이블을 유지하기 위해 필요한 이론적인 메모리가 4기가바이트까지. 현실적이지 않다. 이 문제 해결을 위해 엄청난 크기의 페이지 테이블을 줄이는 방법 마련. 페이지 테이블의 전체 크기는 크지만 ram에 존재하는 페이지 테이블은 다 있을 필요가 없다. 전체 페이지의 내용을 다 ram에 넣지 않고 일부분만 ram에 적절히 놓으면 돌아갈 수는 있다. 핵심적인 아이디어. 여러 approach가 있다. Multi-level paging, hashed page table, inverted page table 등.

37/ HLPT (2)

Multi-level 페이징 기법. 단일 페이지 테이블을 갖지 말고 페이지 테이블의 레벨을 여러 개로 두자. 현재 필요한 엔트리만 램에 적재하는 기법.

38/ Two level paging (1)

두 단계 레벨 페이징. 오른쪽 박스가 물리 메모리. 현재 프로세스 당 두 단계의 페이지 테이블. Outer, page table. 페이지 테이블의 엔트리 개수를 적게 유지하는게 핵심 생각. Outer가 두 번쨰 페이지 테이블의 베이스 주소를 갖는다. 두 번의 translation을 거친다. 예를 들어, 책을 한 권 갖고 있다. 페이지 넘버가 있는데, 페이징은 특정 페이지의 특정 line을 얘기한다. 38페이지의 10번째 줄에 있다. 이는 1단계 페이징 방법. 전공 서적의 chapter 예시, ch1, 2, 3,,, 가 있는데 아까 말한 38쪽 10째 줄을 챕터의 개념으로 다시 표시 가능. 3번째 챕터의 8번째 페이지의 10번째 줄 이런식으로 표시. 챕터와 챕터의 offset으로 표시. 이 구조가 두 단계 페이징. Outer paging은 챕터의 시작에 대한 정보가 있음. Outer page table를 갖고 있고 해당 정해진 챕터에 대한 page table만 ram에 놓으면 된다.

39/

예제. Virtual 주소가 32비트. 페이지 크기를 4k로 가정. 맨 끝 12비트는 offset. 32비트 중 12비트 빼면 20비트. 두 단계 페이징을 쓰기 때문에 10비트 두개로 쪼개. 10, 10, 12비트로 쪼개짐. 어떤 translation 과정을 거치는지. 1단계 페이지 테이블의 인덱스로 쓴다.

40/

페이지 크기를 4K로 하고 첫 번째 10비트로 첫 페이지의 offset. Storage overhead를 줄이는 장점은 추가의 memory access를 동반한다. 페이지 테이블의 양을 줄이는 대신에 성능 이슈가 더 발생 가능. 특정 책의 특정 페이지 라인을 Vol. 6, 3번째 챕터, 7페이지, 10번째 줄. 3단계 페이징으로 비유.

41/ Hashed Page Table

64비트 아키텍쳐 같은 큰 주소 공간을 갖는 곳에 활용. Hashing 은 hash 함수를 만들고 key 받고 value 얻는. 64비트 아키텍쳐는 주소 공간이 크기 때문에 빈 공간이 많다. 빈 공간에 대해 큰 페이지 테이블을 갖는 것이 비효율적. 여러 해시 함수의 값으로 공유하는 해시 테이블. 해시 테이블이 있고 논리 주소가 p,d인데 이것을 해시 함수에 넣고 엔트리에 p,r에 해당하는 정보를 담고 있다. R을 f값으로 바꿔서 물리 메모리로 만든다. 여러 주소 공간의 hash 값이 conflict 가능. 이 경우 linear search해서 해당 값 존재 확인.

42/ inverted page table (1)

프로세스 당 하나의 페이지 테이블이다 보니 프로세스가 많으면 페이지 테이블도 많아진다. 멀티 레벨, 해싱 등을 쓰지만 또 다른 방법은 시스템 전체에 하나의 페이지 테이블만 갖는 발상.

43/ inverted page table (2)

시스템 차원에서 하나의 페이지 테이블을 유지한다. 페이지 테이블의 내용을 보면 지금까지와는 다르다. Pid, p의 pair로 들어간다. i번째 엔트리의 내용이 pid, p. i는 피지컬 프레임의 인덱스. 페이지 테이블의 내용은 물리 메모리의 주인이 누군지. 물리 frame i번째 주인. i번째 프레임의 주인은 pid, p. CPU가 어떤 프로세스를 수행하고 그 프로세스의 pid가 있고 몇 번째 페이지, 몇 번째 offset을 알고 변화하는 상황. 이 상황에서 이 기법은 pid와 p의 pair를 갖고 page table을 서치. 서치 과정을 통해 피지컬 프레임의 i위치를 찾는 방법을 inverted page table이라고 한다. 프레임에 대한 정보를 기억하겠다는 것이 핵심 내용. 서치 프로세스도 이미 상당히 비싼 작업. 해싱 기법을 추가해서 키를 한 번에 빨리 찾는 기법도 추가하고 또 다른 이슈는 페이지 테이블 안에 pid, p가 없다면? Page fault가 발생.

<span style = "color: red">**44/ segmentation**</span>

페이징 기법과 더불어 segmentation 기법도 메모리 관리 기법이다. 페이징은 특정 페이지가 반은 코드, 반은 global data 일수 있다. 물리적으로 잘랐기 때문에. 다른 메모리 관리 기법인 segmentation이 있다. 프로세스 관점에서 보면 여러 이미지가 있고 하나 하나의 segmentation이라고 부른다. 여러 세그멘테이션이 합쳐져서 이미지가 된다.

45/

이 예제가 seg의 케이스 설명. Seg를 하나의 단위로 메모리에 적재. 왼쪽 그림 오른쪽 칸이 다섯개 seg가 매핑. 매핑 정보를 갖는 메타 자료 구조가 seg table이 있다. 오른쪽 그림은 editor는 시스템 라이브러리. 공유하는 코드이기 때문에 물리적으로 하나의 메모리, 논리적으로는 두 개의 다른 프로세스가 공유. 프로세스가 공유하는 데이터 메모리 영역도 잘 표시한다.

46/ Seg Arch.

Seg 기법을 실제 아키텍쳐에서 지원하는데 지원하는 아키텍쳐를 보면 큰 틀이 그림. 프로세스가 10개 세그멘트가 있으면 테이블 엔트리가 10개가 있고 각 엔트리는 해당 세그멘트가 물리 메모리의 시작과 끝을 알려준다. 런타임의 CPU가 논리 주소를 s,d로 표시. s번쨰 세그의 d번째 옵셋. 세그 시스템이 s를 바탕으로 세그 테이블의 s번쨰 엔트리를 읽고 읽힌 데이터는 base 데이터와 max limit 주소. 몇 개의 레지스터가 필요한데 Limit과 base를 담는 cpu 레지스터가 필요하고 이를 STBR이라 하고, 세그의 크기를 지정한 STLR이 있다.

47/ Seg Arch. 2

주어진 프로세스 이미지를 다이나믹하게 어디에 할당할지. 차이는 seg table을 가지고 있다. 결국 relocation이슈가 있다. 어떤 세그를 어디에 할당할 지 dynamic relocation 이슈. Sharing은 장점으로 작용한다. 동일한 세그를 여러 프로세스가 공유하는 장점도 있고 추가로 세그 별로 프로텍션을 적절히 할 수 있다. 코드 세그 경우 절대 수정이 되면 안되고 수행 가능 읽기만 가능한 액세스 권한이 있고 데이터 세그는 읽기/쓰기, 특정 경우 읽기만. 세그의 특수한 protection을 적절히 설정 가능하다. allocation문제가 있는데 특정 세그를 물리 메모리 어디에 할당할것인지, dynamic relocation과 유사. 그렇지 않으면 External frag 발생 가능

48/ <span style = "color: red">**seg vs paging**</span>

Seg는 역동 메모리 할당의 케이스이므로 external frag의 예. 다른 크기의 세그별로 할당과 리턴을 반복하면 메모리의 낭비가 됨. 

페이징 기법은 external frag 발생 안함. 모든 메모리를 동일한 크기로 정확히 매핑. 외부 frag는 없지만 대신 페이지 안에서 내부 frag가 있을 수는 있다. 

따라서 세그 기법과 페이징 기법을 잘 활용해야 하는데 페이징의 약점인 이미지의 특성에 따른 protection이 애매모호 하다는 점을 seg는 잘 지원하기 때문에 이런 상황을 잘 관찰해서 적절하게 기법을 활용하는 생각.

49/ seg with paging 1

Seg별 access를 제안하는 좋은 특성. 두 개의 기법을 적절히 활용. 기본적인 아이디어는 세그의 논리 단위성을 활용해 프로세스에 있는 여러 세그의 코드, 모듈, 스택으로 구분하고 각 세그를 따로 처리하자. 액세스 권한을 잘 제어하자. 코드, 스택 세그멘트의 크기가 다 다를텐데 세그멘트안에서 페이징을 한다. 첫 단계에서 세그멘팅을 하고 하나의 세그멘트에서 페이징을 하겠다. 세그멘트가 pageable. 여러 개의 똑 같은 page로 세그멘트를 쪼개고 세그멘트의 내용이 페이지가 되도록. 세그와 페이징이 합쳐진 방법을 실제 하드웨어가 지원한다.

50/ seg with paging 2

프로세스의 논리 주소. 하나의 세그멘트를 보면 하나의 선이 페이지로 분리된 것. Program text는 3개의 페이지로, program data는 4개… 하나의 세그멘트는 페이징을 할 수 있고 페이징 기법으로 여러 페이징 특성을 활용한 메모리 관리 기법 사용.

51/ 3

결국 세그와 페이징을 같이 쓸 때 프로세스의 논리 주소가 어떻게 물리 주소로 변환되는지 알아야 한다. 주소 변환을 설명하는데, 하나의 프로세스의 논리 주소는 세 개의 인자로 표시된다. 몇 번째 세그에, 몇 번째 페이지에, 몇 번째 offset이냐. 하나의 논리 주소가 세 개로 쪼개진다. 논리 주소가 일차원의 virtual address로 바뀌면 아래 식처럼.

52/ 4

변환해서 얻어야 할 건 f,o의 물리 주소. 시스템이 세그 테이블에 대한 베이스 주소를 갖고 있어야 한다. STBR이라는 cpu register가 갖고 있고 s값을 더해서 세그의 위치를 찾아서 얻어내는 값이 페이지 테이블의 베이스 주소가 된다. p값을 인덱스로 해서 실제 프레임 넘버를 갖고 온다. 최종적으로 얻을 피지컬 메모리의 f파트. 세그와 페이징을 같이 쓰는 방법 주소 변환에 대한 플로우.

53/ 5

이런 세그와 페이징을 쓰면 세그의 공유와 액세스 write을 잘 구현. 두 개의 프로세스가 있고 하나의 세그를 공유할 수 있고 세그에 대한 페이지 테이블을 갖고 있고 이것은 여기저기 흩어져있다.

54/ seg with paging intel x86

아키텍쳐 관점에서 어떻게 세그와 페이징을 활용하는지. 우선 메모리 관리와 관련해서 인텔 아키텍쳐를 볼 땐 용어에 대한 이해가 필요하다. 세 가지 용어를 쓴다. Logical(virtual), linear, physical은 인텔 사람들 용어. 아래 그림에서 보면 프로세스 관점에서는 logical 주소고 seg unit을 거쳐 linear로 바뀌고 paging 을 거쳐 physical로 바뀐다. 인텔 아키텍쳐에서는 세그먼트를 특정화 시켜야 하고 논리 주소를 얘기할 때 세그와 세그 안에서의 옵셋을 표현. 세그를 거치면 결국 linear 주소로 바뀌고 우리가 아는 페이징을 통해 physical로

55/ 2

인텔 아키텍쳐에서 주소 변환에 대한 그림. 앞 부분이 인텔에서 제공하는 세그에 대한 내용, 이걸 거치면 linear로. 두 단계 페이징을 거쳐서 physical 주소로 바뀐다. 인텔 아키텍쳐가 지정한 메모리 상의 테이블이 있는데 이것이 seg 테이블이 된다. 세그 아이디와 offset을 통해 테이블 변환으로 linear 주소에 내 세그가 어디에 있는지에 대한 정보를 앞 seg과정을 통해 얻어낸다.

Linear 주소 중 앞 10번째는 테이블의 인덱스로, 두 번째 10비트는 두 번쨰 페이지 테이블의 인덱스로 쓴다. 프로세스가 정한 세그멘트 단위의 주소를 하드웨어적으로 거쳐서 특정 물리 주소로 바꾸는 기능을 제공. MMU.

56/

2학기 커널 강의 슬라이드. 인텔에서는 16비트 레지스터를 갖는다. 코드 세그를 지정하는 cs, 데이터 ds 등등. 기본 철학은 프로세스를 구성하는 이미지의 조합. 인텔에서는 각각의 이미지를 따로 지정. 하드웨어 지원을 운영체제 커널이 활용하는건 또 다른 이야기.

57/  paging in hardware

Linear address가 만들어진다. 이것을 2단계 페이징을 통해 물리 주소로 바꾼다.

58/ 2

구체적인 하드웨어의 내용을 이해하면 인텔 프로세서안에 있는 메모리 관리와 관련한 레지스터의 내용. CR3 레지스터가 상당히 중요. 상위 20비트가 페이지 디렉토리 베이스 주소. 프로세스에 속한 페이지 테이블이 두 단계인데 그 첫 번째 페이지 테이블의 베이스 어드레스. 모든 프로세스는 자기 자신의 페이지 테이블을 가져야 하는데 프로세스 스위칭 발생 시 레지스터, 컨텍스트만 바뀌는게 아니라 프로세스가 메모리 변환을 하기 위한 페이지 테이블도 바뀌어야 한다. 모든 프로세스는 자기 pcb안에 내 프로세스가 속한 페이지 테이블이 어디 있는지에 대한 정보가 있어야 함. 내 프로세스가 스케줄이 되면 내 페이지 테이블을 찾는 정보가 page directory base address. 프로세스 테이블 정보를 그 레지스터에 쓰면 하드웨어는 인지한다. 이 프로세스의 페이지 테이블이구나 하드웨어가 알아냄.

59/ regular paging

인텔 아키텍쳐의 경우 4kb의 페이지 사이즈를 활용한다. 페이지 크기를 담당하는건 12비트. 20비트 남고 최상위 10비트는 첫 테이블의 인덱스, 그 다음 10비트는 그 다음 테이블.

60/ regular paging

세그를 거쳐서 만들어진 32bit의 linear 주소. 물리 메모리로 페이징을 통해 바뀌어야 하는데 cr3,테이블을 통해 바뀌는 과정을 그림으로. 상위 10비트가 첫 단계 인덱스로 쓰이고, 내 페이지 테이블이 어디서 시작하는지 cr3 상위 20비트에 있다.

61/ page directory and page table

더 깊이. 패이지 테이블의 엔트리가 4바이트. 여러 페이지 테이블이 존재할 것이다. 페이지 테이블의 엔트리의 모습을 보자.

62/ 2

인텔 아키텍쳐에서 페이지 테이블, 페이지 디렉토리 테이블의 내부적인 내용. 두 단계의 4kb 페이지를 활용하는 두 단계 페이징 입체 모습. 32비트 리니어 주소를 3영역으로 쪼개고 상위 10비트를 첫 페이지 디렉토리의 인덱스. 왼쪽 그림을 잘 봐라. 페이지 디렉토리 안에 하나의 엔트리. 페이지 테이블의 특정 엔트리의 모습을 zoom in. 페이지 테이블 엔트리의 내용이 아래 그림. 32비트니까 4바이트. (페이지 디렉토리, 페이지 테이블은 10비트를 지정해서 2의 10승개의 엔트리를 갖는다) 하나의 엔트리에 대한 상위 20비트를 base address. F 넘버. 나머지 12비트가 비트 flag. 0번째 비트는 present bit. 실제 내 페이지가 물리 메모리에 있으면 1. w비트는 read/write이면 1. u비트는 유저에 속하느냐 커널에 속하느냐. a비트는 해당 페이지가 한 번이라도 읽기/쓰기를 했느냐 했으면 1. d비트는 페이지의 내용이 수정 되었는지에 따라. MMU 하드웨어는 매 페이지가 활용될 때마다 이런 정보를 하드웨어 적으로 파악해서 적절하게 설정. 페이지 테이블 정보를 OS가 읽어가서 의사결정에 활용. 소프트웨어적으로 비트를 리셋 가능. 적절하게 비트를 세팅하고 리셋. 하드웨어는 MMU, OS에서는 MMU를 관리하는 커널 코드라고 한다. 이런 내용이 실제 메모리에 담겨 있다. OS와 하드웨어는 뗄 수 없다.

63/
4mb 크기로 페이지 운영. Extended paging. 페이지의 offset을 유지하기 위해 22비트 필요. 페이지 디렉토리의 개수도 1024개로 고정(2^10). 메모리 시스템을 바꿔서 운영 가능.
64/
4메가바이트 1단계로 운영되는 페이지. 상황에 따라 다르게. 언제 4메가바이트 페이징을 쓰는지.
65/ paging in Linux
리눅스 운영체제가 인텔 하드웨어에서 돌 때 메모리를 어떻게 관리하는지. 페이징 관점에서. 커널 자료구조의 이름들. Page global dir. Page upper dir. Page middle… page table 자료구조. 리눅스 커널이 다단계의 페이징을 지원하도록 만들어져있다. 커널은 4단계 페이징을 가정하고 짠다. 리눅스의 stability 때문이다. 가능하면 하드웨어 의존도를 줄여서 커널 소스 변경을 최소화하자. 커널 소스가 상당히 generic해야한다. 새로운 하드웨어가 나와 새로운 커널이 필요하면 큰일. 이식성에 대한 큰 철학이 있다. 메모리 management가
커널 4단계의 페이지 테이블이 인텔 아키텍쳐의 두 단계 테이블과 맵핑. 커널의 4단계 페이지 테이블을 줄여서 두 개의 피지컬 하드웨어 페이지로 mapping을 할까? 간단하다. 가운데 두 개를 안쓴다. 커널 자료구조의 가운데 두 개를 스킵해서 global dir.와 page table 자료구조를 쓴다.
66/
커널과 mmu 하드웨어의 관계를 보여주는 그림. 빨간 박스가 mmu라고 하는 하드웨어. Cpu 내 하드웨어로 돌아가는 메모리 변환에 대한 내용. 인텔 같은 경우 CR3가 정해지면 page dir.으로 가고 뭐 그런 과정을 mmu가 한다. 커널이 involve 하지 않고 철저하게 cpu안에 있는 mmu가 처리. OS입장에서는 걱정할 필요가 없다. 하위 20비트에 데이터만 로드해주면 끝난다. 빨간색 밖으로 커널의 도메인. Linux의 PCB안에 mm이라는 엘리먼트(포인터)가 있다. 따라가면 mm_struct 커널 자료구조가 있는데 pgd라 부르는 엔트리가 있는데 이게 내 프로세스의 첫 번쨰 페이지 테이블의 베이스 어드레스. 프로세스는 항상 pgd를 가지고 있다. 내 프로세스가 스케줄이 되면 그 내용을 cr3레지스터에 로드. Mmu 하드웨어가 translation을 한다. 하드웨어하고 os가 잘 코디네이트해서 고급스러운 메모리 매니지먼트를 지원.
67/ summary
메모리 변환 과정은 운영체제와 하드웨어가 적절히 협력해서 수행. 아주 기본적인 메모리 관리 기법이 있다. 고정 파티션, 프로세스가 원하는만큼 자르기, 모든 image를 세만틱 특성을 고려하지 않고 일정 크기로 잘라서 페이지 맵핑, 이미지의 특성을 반영해서 하는 segmentation. 다음 렉쳐에서는 adv 메모리 매니지먼트를 얘기한다. 가상 메모리.
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