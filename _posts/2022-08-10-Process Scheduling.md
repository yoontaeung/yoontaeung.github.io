---
title: Process Scheduling
categories: [Computer Science, OS]
date: 2022-08-09 12:00:02
tags: [cs, os]     # TAG names should always be lowercase
author: <author_id>
---

# L5. Process Scheduling

Lecture 5: Processor Scheduling
어떤 프로세스를 선택해 돌릴 것인가. 실제 어떤 정책으로 여러 프로세스를 효율적으로 돌릴 것인가. 알고리즘. Real OS에선 어떤 형태로 스케줄러를 구현하는가.
2/ basic concepts in scheduling
서버는 queue에 있는 job중 하나를 선택. Nonpreemptible은 서버가 디스크, 프린터 같은 경우. 한 번 server에 job이 할당되면 중간에 멈추지 않는다. 뺏기지 않음. 디바이스 특성에 따라 nonpreemptible하게만 돌아가는 경우. Preemptible은 중간에 서버에 job이 할당되어도 멈추고 다른걸 시행하고 resume 가능. CPU is the example for it.
3/
Cpu 스케줄링에 대해서만 얘기할거임. Ready queue에 있는 job을 어떻게 고르는지.
4/
Cpu 스케줄러 용어로 dispatcher를 썼음. 새로운 프로세스를 dispatch. 디스패쳐는 역사적으로 많이 쓰였더 이름이고 커널의 스케줄링을 담당했다. 이런 디스패칭을 할 떄 얼마나 효율적으로 빠르게 하는게 문제다.

OS 0427
5/
OS 전체가 효율적으로. 어떤 스케줄링 폴리시를 사용할 것인가. 어떤 정책이 좋은건지 알아보는 criteria 필요. 시스템 관점에서 또는 유저 관점에서. CPU utilization은 얼마나 CPU가 바쁘게 움직이는가. 유틸라이제이션을 최소화해서 CPU가 노는 시간을 줄인다. Throughput은 단위시간에 얼마나 많은 프로세스를 끝낼 수 있는가? utilization and throughput은 다르다. 전자는 일하는 비율, 후자는 얼마나 빨리 끝내는가. 

유저 관점에선 turnaround time, waiting time, response time. 

Turnaround time: 내 프로세스를 시작해 끝날 때까지 시간. Waiting time: 레디큐에서 기다렸던 모든 시간 합. Response time: 처음으로 cpu가 내 일을 시작한 시점. 커널은 cpu의 자원을 놀게 두고 싶지 않고 유저는 커널이 내것을 빠르게 하길 원함. 둘을 동시에 만족못함. 결국 system, user 모두를 좋게 만드는 cpu 스케줄링이 어려움.
6/
백그라운드 얘기 좀 한다. Cpu burst, I/O burst. IO 작업하면 block이 되어 스위칭. Burst는 갑자기 촥 밀려나가는 것. CPU, 아이오 버스트가 번갈아가며. 어떤 프로그램은 cpu burst가 길고 많고, 어떤건 IO가 主가 됨. Cpu bound, IO bound 모두 해피하게.
7/
스케줄링은 preemptive or nonpreemptive. 우선순위 기반의 스케줄링 정책이 타당해보이지만 여러 문제가 있다. 레디 큐 프로세스를 우선순위만 갖고 선택하면 낮은 우선순위 프로세스는 계속 선택이 안될 듯. 결국 그 프로세스는 cpu 선택 못받음(starvation). 그래서 해결방법으로 기다리는 시간을 관찰해서 너무 오래 기다리면 우선순위 인위적으로 올려주는 방법있음.
8/
기술적인 스케줄링이 커버하는 영역은? 레디큐에 있는 프로세스를 어떻게 고를지 정하는 것. Selection function은 어떻게 프로세스를 고를까? 이건 반쪽 얘기. 나머진 언제when, decision 고를것인가에 대한 얘기도 해야함. 하나의 방법은 nonpreemptive, 다른 하나는 preemptive. Nonpreemptive는 수행하는 프로세스가 끝나거나 IO로 인해 block될 때만 다른 프로세스 부른다. 반면  preemptive는 타임 퀀텀이 expired될 때. 현재 프로세스가 계속 쓸 수 있음에도 불구하고 time quantum이 다 되어서 뺏김. 계속 수행할 수 있음에도 불구하고 커널이 cpu를 뺏음. 수행중이던 프로세스는 레디 큐로 가서 쉬어라. 정리하면 cpu 스케줄링은 preemptive or nonpreemtpive로 나뉨. 어떤 것을 고를 건지(which)는 국지적인 local문제가 됨.

9/
꼭 이해해야 함. Nonpreemptive 정책 설명가능. 1,4번이 그에 해당된다. 1,4번일때만 레디 큐에서 프로세스를 고른다. 2번은 타임 퀀텀이 끝난경우. 3번은 blocked 프로세스가 IO 작업이 다 마쳐져서 다시 깨어나 레디 큐로 들어가는 상황. 만약 현재 수행중인 프로세스보다 더 위급하면 그게 먼저 수행. 우리가 쓰는 대부분은 preemptive한 스케줄링 정책 사용
10/ **FCFS**
몇 가지 cpu 스케줄링 정책 소개. FCFS는 selection이 간단. 먼저 들어온걸 선택. Decision은 nonpreemptive 정책. 타임 퀀텀 없음. FCFS는 CPU bound 프로세스를 선호한다. CPU bound 프로세스는 원할만큼 cpu를 쓴다. Cpu usage 관점에서 보면 훨씬 cpu를 많이 사용. IO bound는 레디큐로 꺠어났을 때도 앞의 프로세스를 계속 기다려야함.
11/
세 프로세스가 있다. Gantt chart. P2는 24 기다리고 p3는 27 기다려야 수행. 평균 17이라는 시간을 기다려야 다 끝남.
12/
순서 바뀜. Waiting time이 많이 줄어듦.
13/ **Round Robin**
일반적인 정책. selection함수는 fcfs와 같음. 항상 레디큐 앞에 있는걸 고름. 타임 퀀텀을 계속 관찰해야함. 

타임 sharing 정책에 적합. 아주 빈번하게 process간 스위칭 발생. 타임 퀀텀을 얼만큼 정할건지가 어려움. 타임 퀀텀이 매우 커지면 FCFS와 같음. 작으면 프로세스 반응성은 좋지만 스위칭이 자주 발생해 overhead 발생.
14/
타임 퀀텀을 적절하게 잡아야 함. 너무 작거나 크면 안된다. 아직까지 cpu bound프로세스 선호방식.
15/
16/ **SJF 스케줄링**
IO 바운드 프로세스가 앞 두 스케줄링 정책으로는 불리함. Shortest job first. 먼저 온 순서대로가 아니고 ready queue를 훑어보고 일의 양을 예측해 가장 짧은것부터 수행하자. Selection 함수가 달라짐. 예상 cpu burst time이 짧을 것 같은것부터 수행. Non or preemptive 가능. Preemptive는 다른말로 srtf. 최적의 알고리즘이다. 숨겨진 문제가 전제다. Shortest “expected” 예상되는 타임이 실제와 같은 최적이다. IO bound process를 cpu bound로부터 보호. Cpu burst를 예측하기가 어렵고 굶는 문제도 있다. Long cpu burst는 계속 선택이 안됨.
17/
프로세스마다 4만큼 평균적으로 기다림
18/ **Preemptive SJF**
평균 웨이팅 타임은 3. P1은 내 프로세스가 레디큐에서 기다린 시간이 9. Preemtpive가 좋아보이지만 switching이 더 많다. OS가 스위칭을 효율적으로 못한다면 그 overhead 때문에 안좋아질수 있음. 이런 방법이 IO bound를 위한 policy다.
19/estimating cpu burst
Cpu burst 예측이 어렵다. 어떻게? 여러가지 예측 기법. 유일한 힌트는 내 지금까지 내 프로세스가 사용한 cpu burst. 현재 시점 T에서 그 다음 T+1의 cpu burst를 예측하기 위해 과거의 모든 cpu burst의 평균을 내보자. Simple averaging. CPU burst의 순서를 무시하고1/n? 지역성 문제. 그 위치와 근접한 시간. 옛날보단 최근의 behavior 가 더 가깝다. Weight를 최근걸로 바꾼다. A를 1/n보다 큰 수로 설정하면 최근 시간에 더 무게를 둔다. Exponential
20/
a값이 0.2, 0.5, 0.8일 경우로 나눠 볼 때 클수록 최근 값이 더 많이 반영. exponential하게 weight. 파를 적절히 조절해야 함. 오른쪽 그래프 실제 값은 observation. Exponential average의 효율성
21/ **Multilevel queue scheduling**
Ready queue에서 고른다 했는데 실제 OS에선 ready queue가 여러 개 있을 수 있다. 시스템 커널에 따라 queue마다 다른 스케줄링 정책을 정할 수 있음. 여러 개의 레디큐를 대상으로 스케줄러를 구성하는걸 multilevel queue 스케줄링. 이게 real OS가 구현하는 스케줄링 정책의 baseline.
커널이 다루고 있는 프로세스를 여러 개로 나눔. 설계 근거를 바탕으로 그룹으로 나눠 그룹 당 특정 스케줄링 정책을 사용한다. 그룹마다 레디큐가 있을거고 그에 맞는 스케줄링. 몇 개의 멀티 큐를 가지고 있을 것인가? 어떤 근거로 개수를 정할 것인가? 각 그룹에 따른 queue에서 어떤 정책을 펼것인가. FIFO, roung robin, sjf? 어떤 큐부터 먼저 스케줄링 할 것인가? 운영체제 설계 취지에 따라 조금씩 다르게. 멀티레벨 큐.
22/ **MLFQ**
큐가 멀티개. 앞 그림과 다르게 첫 번 쨰 큐의 아웃풋이 끝나지 않고 그 다음 레벨의 레디 큐로 들어가서 마지막까지 도달. 어떤 일이 시스템에 들어올 때 맨 처음 들어가는 것은 RQ0. RQ0에 대한 스케줄링 정책이 있을 텐데 일이 안끝난 프로세스는 현재 큐가 아니라 next level로 넘긴다. 어떤 시나리오가 나오냐면 cpu server에 타임 퀀텀을 작게 설정하다가 나중엔 크게 설정한다. 어떤 일이 들어올 때 io bound process라고 한다면 cpu를 조금 쓰고 IO를 빈번히. 아마 첫 번째 레벨에서 수행될 것이다. 한 번에 끝나서 나감. 반면 어떤 프로세스는 cpu bound. CPU를 오래 쓰는 프로세스는 선택되어 첫 번째 큐에서 수행하다가 그 타임 퀀텀보다 cpu burst 시간이 길기 때문에 수행이 전부 끝나지 않음. 그럼 그 다음 레벨로 넘어감. 두 번째 큐 스케줄링 정책에 의해 진행이 되는데 그 레벨은 time quantum을 조금 길게 준다. 그럼 끝나거나 다음 레벨로 가거나. 마지막 레벨에선 수행될 때 못 끝날 땐 round robin으로 빙글뱅글. 

Cpu burst가 짧을수록 윗 레벨에서 끝날 확률이 높고 길수록 아래까지 내려가서 수행이 된다. 이런 상황에서 mlfq가 정한 정책 중 중요한 것은 i번 째 레벨 스케줄러가 동작하려면 그 위 레벨 스케줄러의 레디 큐가 비어야 함. i번째 레벨에 있는 큐의 job이 처리 되기 위해선 i-1번째 레디 큐에 프로세스가 없어야 함. 이게 preemtpive scheduling with dynamic priorities.
23/ File share scheduling (FSS)
Unix 운영체제를 보면 멀티 유저. 공평성. 모든 유저는 가능하면 똑 같은 cpu시간을 써야한다는 정책. 특정 사용자가 fork를 많이 해 많은 프로세스를 만들고 그 프로세스를 수행해 cpu 를 독점하는 것을 막을 수 있음. 유저 당 쓸 수 있는 cpu 시간을 제한할 수 있다. 이런 스케줄링 정책을 fair share scheduling. 프로세스 별 완벽한 fairness를 제공할 것인가? CFS.


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