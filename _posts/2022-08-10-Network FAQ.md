---
title: Computer Network FAQ (Kor)
categories: [Computer Science, Network]
tags: [cs, network]     # TAG names should always be lowercase
author: <author_id>
comments: true
---

# 컴퓨터 네트워크 FAQ

- <span style = "color: #b6d7a8">**TCP와 같이 protocol을 reliable하게 설계 하려면 무엇이 필요한가?**</span>
    
    congestion control, flow control, connection setup
    
- <span style = "color: #b6d7a8">**Flow control이란 무엇인가? Congestion control과 flow control의 차이점은?**</span>
    
    flow control은 송신측과 수신측의 데이터처리 속도 차이를 해결하기 위한 기법
    
    congestion control은 송신측의 데이터 전달과 네트워크 처리속도 차이를 해결하는 기법
    
    flow control은 수신측 처리 속도가 느려 제한된 큐 용량을 초과해 도착하는 데이터 손실을 가져옴. 강제로 송신측의 데이터 전송을 줄인다. stop and wait 방식 혹은 슬라이딩 윈도우 기법. 
    
    congestion control은 네트워크의 혼잡을 피하기 위해 송신측의 데이터 전송 속도를 강제로 줄인다.
    
    TCP congestion control의 핵심은 cwnd(congestion window)값을 잘 조절하는 것. 
    
- <span style = "color: #b6d7a8">**congestion control 기본 원리 Additive increase & multiplicative decrease(AIMD)**</span>
    
    패킷 loss 가 발생하지 않는다면, 점진적으로 cwnd값을 증가하다가 loss 감지하면 cwnd값을 절반으로 줄임. 
    
    ![Untitled](/assets/img/CN.png)
    
- <span style = "color: #b6d7a8">**TCP slow start**</span>
    
    매 RTT마자 cwnd값을 두 배로 증가.
    
    ![Untitled](/assets/img/CN1.png)
    
- <span style = "color: #b6d7a8">**loss 대응 방식**</span>
    
    1) 3 duplicate ACKs - 특정 패킷 loss. cwnd를 loss가 발생할 때 절반으로 줄임
    
    2) timeout으로 인한 loss - 전송한 모든 패킷이 loss. cwnd를 1로 확 줄임. 
    
    TCP Taho, TCP Reno 방식
    
- <span style = "color: #b6d7a8">**OSI 7계층에 대해 설명하시오.**</span>
    
    네트워크 흐름을 알아보기 쉽고, 특정 layer에 문제가 생기면 해당 layer에서만 디버깅을 하면 됨.
    
    “어디"에서 “어디" 목적지와 도착지 정보는 3계층 IP 프로토콜(패킷 전달 역할)
    
    패킷 손실, 순서대로 도착 여부 등 “어떻게" 도착했는지 - TCP, UDP
    
    **L7 Application layer** - <span style = "color: #ff3eeb">**사용자와 인터페이스 기능 제공**</span>
    
    **L4 Transport layer** - <span style = "color: #ff3eeb">**신뢰성 있는 데이터 전송**</span>
    
    송/수신자간에 신뢰성있는 데이터를 주고 받을 수 있도록 해주어, 상위 계층들이 데이터 전달의 유효성이나 효율성을 생각하지 않도록 해줍니다. 전송계층은 효율적인 데이터과 오류 검출 및 복구와 흐름제어 등을 수행합니다.
    
    **L3 Network layer** - <span style = "color: #ff3eeb">**패킷의 경로지정(IP, 외부 네트워크)**</span>
    
    네트워크 계층은 다른 네트워크나 인터넷으로 데이터 전송을 담당하는 부분. 데이터 링크 계층은 같은 네트워크 상의 데이터 전송을 담당.
    
    IP주소로 수신지 컴퓨터 지정 + 어떤 경로로 전송할지 결정 - 라우팅
    
    데이터를 목적지까지 가장 안전하고 빠르게 전달하는 기능을 담당. 라우터를 통해 이동할 경로를 선택하여 IP 주소를 지정하고, 해당 경로에 따라 패킷을 전달해주며 라우팅, 흐름 제어, 오류 제어 등을 수행합니다
    
    **L2 Data link layer** - <span style = "color: #ff3eeb">**프레임에 주소 부여(MAC, 내부 네트워크)**</span>
    
    물리 계층으로 송수신 되는 정보를 관리하여 안전하게 전달하는 역할
    
    MAC 주소로 통신
    
- <span style = "color: #b6d7a8">**Transport, network, link 계층이 모두 연결에 관한 것인데, 무엇이 차이가 나나요?**</span>
    
    Transport - 출발지부터 도착지까지 패킷이 제대로 전달될 수 있게. Application layer에서 만든 데이터를 일정한 크기로 자름
    
    TCP - reliable, in-order, congestion & flow control, connection setup
    
    app이 transport layer 프로토콜 이용하기 위해선 프로세스마다 socket 이용. 여러 socket 데이터가 쏟아지는 형태 → multiplexing, demultiplexing
    
    ![Untitled](/assets/img/CN2.png)
    
    정확한 application의 socket으로 전달하기 위해 port number 활용
    
    Network - 핵심 기능 Forwarding, Routing
    
    Forwarding은 data plane이 담당. 패킷이 들어왔을 때 내보내는 액션
    
    Routing은 control plane이 담당. 들어온 패킷을 어디로 보낼지 판단. 라우터가 길을 판단하기 위해 라우터끼리 메시지로 길을 그림 (routing protocol)
    
    Data plane - 적용 범위가 라우터 내부 Local. input으로 도착하는 datagram이 어떻게 output으로 forwarding 될 지 결정
    
    control plane - 적용 범위가 네트워크 전체. forwarding table 표를 이용해 결정하는 전통 방식 & 중앙 서버를 이용하는 SDN 방식
    
- <span style = "color: #b6d7a8">**Network에서 checksum을 위해 사용하는 hash function과 security에서 data integrity를 위해 사용하는 hash function이 서로 interchangable해요?**</span>
- <span style = "color: #b6d7a8">**TCP와 UDP의 차이점은?**</span>
    
    TCP는 cconnection-oriented로 통신이 시작되기 전 송수신측 논리적 연결이 설정되어 있음. 패킷 손실, 중복이 일어나지 않게 하는 안전한 전송. 
    
    UDP는 connection less로 IP 주소와 port 번호만 있으면 패킷을 보내며 패킷을 받았는지는 신경쓰지 않음. 
    
- <span style = "color: #b6d7a8">**Congestion이 발생했다는 것을 어떻게 할 수 있는가?**</span>
- <span style = "color: #b6d7a8">**3-way handshaking이란?**</span>
    
    TCP는 장치들 사이 논리적인 접속을 성립하기 위해 3 way handshaking을 사용한다. 즉, TCP/IP 프로토콜을 이용해서 통신을 프로그램이 데이터를 전송하기 전 정확한 전송을 보장하기 위해 상대방과 세션을 수립하는 과정을 말한다. 
    
    ![Untitled](/assets/img/CN3.png)
    
    반면, 4 way handshaking은 세션을 종료하기 위해 수행하는 절차. Server가 FIN을 전송하기 전에 전송한 패킷이 routing 지연이나 패킷 유실로 인한 재전송 등으로 FIN 패킷보다 늦게 도착하는 상황에는 그 채킷이 drop되고 유실될 것. 이를 막기 위해 client 가 server로부터 FIN을 수신하더라도 일정시간 동안 세션을 남겨 놓고 잉여패킷을 기다리기도 함. 
    
    ![Untitled](/assets/img/CN4.png)
    

- <span style = "color: #b6d7a8">**CSMA/CD란?**</span>
- <span style = "color: #b6d7a8">**MAC address란? IP address가 MAC address에 비해 갖는 장점은?**</span>
- <span style = "color: #b6d7a8">**CDN 이란?**</span>
    
    ![Untitled](/assets/img/CN5.png)
    
    전 세계에 여러 서버를 만들고 각 서버에 비디오 파일 복사본을 저장. 사용자가 컨텐츠를 요청하면 해당 사용자와 가장 가까운 위치의 서버에서 비디오 컨텐츠를 받아온다.

<br/><br/><br/><br/><br/><br/>
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