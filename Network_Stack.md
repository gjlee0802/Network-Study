# 참고 자료
Linux Networking Documentation   
https://www.kernel.org/doc/html/latest/networking/index.html   
TCP/IP 네트워크 스택 이해하기   
https://d2.naver.com/helloworld/47667   
Path of a packet in the Linux Kernel Stack   
http://pigbrain.github.io/network/2016/05/29/PathOfAPacketInTheLinuxKernelStack_on_Network    
The Linux Kernel - Network    
https://movefast.tistory.com/350   
리눅스 커널 네트워킹: 커널 코드로 배우는 리눅스 네트워킹의 구현과 이론   
http://www.yes24.com/Product/Goods/31918354   
리눅스 네트워크의 이해   
http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960778719   


# 1. 들어가며
## 1.1 네트워크 디바이스
네트워크 디바이스 드라이버는  2계층(L2)인 데이터 링크 계층에 있다.   
net_device 구조체와 이와 관련된 일부 개념만 간단히 살펴보자.   
네트워크 스택을 잘 이해하려면 네트워크 장치 구조체와 관련된 기본적인 사항에 익숙해져야 한다.   
장치 매개변수(보통 이더넷에 1,500 Bytes로 설정돼 있는 MTU 크기 같은)는 패킷을 분할해야 하는지 결정한다.   
net_device는 매우 큰 구조체이고, 다음과 같은 장치 매개변수로 구성돼 있다.   
- IRQ 번호
- MTU
- MAC 주소
- 장치의 이름(ex. eth0, eth1 ...)
- flag 값(ex. 활성/비활성)
- 연관된 멀티캐스트 주소 목록
- promiscuity(무차별) 카운터
- 장치가 지원하는 기능
- 네트워크 장치 콜백 객체
- ethtool 콜백 객체
- 멀티 큐를 지원하는 장치일 경우 Tx, Rx 큐의 개수
- 패킷의 마지막 송신 타임스탬프
- 패킷의 마지막 수신 타임스탬프   

다음은 net_device 구조체의 일부 항목이 정의된 모습이다.   
[include/linux/netdevice.h](https://github.com/torvalds/linux/blob/master/include/linux/netdevice.h#L1932)   
   
### 1.1.1 promiscuity 카운터
promiscuity 카운터가 0보다 크면 네트워크 스택은 로컬 호스트를 목적지로 하지 않는 패킷도 버리지 않는다.   
예를 들면, 이것은 사용자 공간(userspace)의 미가공 소켓(raw socket)을 열어 이러한 유형의 트래픽도 수집하고자 할 때 사용하는 tcpdump나 wireshark 같은 패킷 분석기에서 사용된다.   
패킷 분석기를 시작할 때마다 카운터가 1 증가, 종료하면 1 감소한다. 그리고 값이 0이 되면 더는 패킷 분석기가 실행되지 않는 것이며, 장치는 무차별 모드를 종료한다.   

### 1.1.2 네트워크 장치의 NAPI(New API)
NAPI는 오늘날 대부분의 네트워크 디바이스 드라이버에서 구현하고 있는 기능이다.   
오래된 네트워크 디바이스 드라이버는 인터럽트 구동 모드로 동작하는데 이는 패킷을 수신할 때마다 인터럽트가 발생하는 방식이고 이는 트래픽 부하가 심할 때 성능 측면에서 비효율적이다.   
그리하여 New API(NAPI)라고 하는 새로운 소프트웨어 기법이 개발됐고, 현재 대부분의 네트워크 장치 드라이버에서 지원한다.   
NAPI는 부하가 높은 상태에서 네트워크 디바이스 드라이버가 폴링 방식(polling mode)으로 동작한다.   
이는 각 수신 패킷이 인터럽트를 발생시키지 않음을 의미한다. 그 대신 패킷은 드라이버에 버퍼링되고, 커널이 이따금 패킷을 가져오기 위해 드라이버를 대상으로 폴링한다.   
NAPI를 이용하면 부하가 높은 상황에서 성능이 향상된다.   
지연 시간을 최대한 낮춰야 하면서 높은 CPU 사용률은 기꺼이 감수하는 소켓 어플리케이션을 위해 커널 3.11 이후로 소켓에 대한 바쁜 폴링(Busy Polling on Sockets) 기능을 추가했다.   

### 1.1.3 패킷의 수신과 송신
**네트워크 디바이스 드라이버의 주요 작업**은 다음과 같다.   
- 로컬 호스트를 목적지로 하는 패킷을 수신하고, L3(Network Layer)을 거쳐 L4(Transport Layer)로 전달.
- 로컬 호스트에서 생성되어 외부로 나가는 패킷을 전송하거나, 로컬 호스트에서 수신된 패킷을 포워딩.   

다음은 네트워크 스택 내에서의 **패킷의 이동에 영향을 주는 요소**에 대해 알아본다.   
#### 1. routing subsystem
패킷을 어떤 인터페이스로 보낼지에 관한 결정은 라우팅 서브시스템에서 수행된 탐색에 기초한다.   
그러나 라우팅 서브시스템에서의 탐색만으로 네트워크 스택 내의 패킷 이동이 결정되는 것은 아니다.   
#### 2. netfilter hook
예를 들어, 네트워크 스택에는 넷필터(netfilter) 서브시스템의 콜백(=netfilter hook)을 등록할 수 있는 5개의 지점이 있다.   
수신된 패킷에 대한 첫 번째 netfilter hook 지점은 라우팅 탐색이 수행되기 이전의 `NF_INET_PRE_ROUTING`이다.   
패킷이 등록한 콜백으로 처리되면(콜백은 `NF_HOOK()`라는 매크로에서 호출된다) 해당 콜백의 결과(verdict)에 따라 네트워킹 스택 내에서 계속 이동할 것이다.   
verdict가 `NF_DROP`이면 패킷은 버려지고 `NF_ACCREPT`면 평소처럼 계속 이동한다.   
netfilter hook 콜백은 `nf_register_hook()` 함수나 `nf_register_hooks()` 함수로 등록하며, 다양한 넷필터 커널 모듈에서 이러한 함수를 보게 될 것이다.   
커널 netfilter 서브시스템은 잘 알려진 iptables 사용자 공간(user space) 패키지의 기반구조다.   
#### 3. IPsec subsystem
패킷의 이동은 netfilter hook 말고도 IPsec 서브시스템의 영향을 받을 수도 있다.   
IPsec는 3L(Network Layer) 보안을 제공하고 ESP와 AH 프로토콜을 사용한다.   
IPsec는 전송 모드(transport mode)와 터널 모드(tunnel mode)라는 두 가지 동작 모드가 있다.   
대부분의 가상 사설 네트워크(VPN)에서는 IPsec가 기본적으로 사용된다.   
#### 4. 패킷의 IPv4 헤더에 포함된 ttl 필드값
ttl은 포워딩 장치마다 1씩 감소한다. ttl이 0이 되면 버려지고 "TTL Count Exceeded" 코드가 포함된 "Time Exceeded"라는 ICMPv4 메시지가 반환된다.   
이것은 어떤 오류 때문에 패킷이 끝없이 포워딩되는 것을 방지한다(Loop 경로 방지).   
패킷이 성공적으로 포워딩되고 ttl이 1 감소할 때마다 헤더의 체크섬을 다시 계산해야 하는데, 체크섬은 IPv4 헤더에 따라 달라지고 ttl은 IPv4 헤더의 일부이기 때문이다.   

-------------------------------------------------
패킷의 이동을 더 잘 이해하려면 리눅스 커널에서 패킷이 어떻게 표현되는지 알아야 한다.   
sk_buff 구조체는 헤더[(include/linux/skbuff.h)](https://github.com/torvalds/linux/blob/master/include/linux/skbuff.h#L717)를 포함해서 유입/유출 패킷을 나타낸다.   
sk_buff 객체를 흔히 SKB로 표기한다(socket buffer을 나타낸다).   
sk_buff 구조체는 매우 큰 구조체이므로 우선 이 구조체의 몇 항목만 살펴본다.   

### 1.1.4 소켓 버퍼
> 참고 : https://hand-over.tistory.com/2   
> https://www.kernel.org/doc/html/latest/networking/kapi.html?highlight=sk_buff#c.sk_buff   

SKB(sk_buff)를 이용할 때는 다음과 같은 SKB API를 준수해야 한다.   
가령 `skb->data` 포인터에 접근하고 싶을 경우 직접 접근하지 않고 `skb_pull_inline()` 혹은 `skb_pull()` 함수를 이용한다.   

- alloc_skb(size)
- skb_reserve(len)
- skb_put(len)
- skb_push(len)
- skb_pull(len)
- skb_transport_header(skb) : SKB에서 L4 헤더(Transport HDR)을 가져온다.
- skb_network_header(skb) : SKB에서 L3 헤더(Network HDR)을 가져온다.
- skb_mac_header(skb) : SKB에서 L2 헤더(MAC HDR)을 가져온다.   
--------------------------------------------
패킷이 회선에서 수신되면 네트워크 디바이스 드라이버에 의해 SKB가 할당되며, 일반적으로 `netdev_alloc_skb()` 함수를 호출해 할당된다.   
패킷이 이동하는 과정에서 버려지는 경우에는 `kfree_skb()` 또는 `dev_kfree_skb()` 함수를 호출하여 이루어진다.   
SKB의 일부 항목은 링크 계층(L2)에서 결정된다. 예를 들어, `pkt_type`은 목적지 이더넷 주소에 따라 `eth_type_trans()` 함수에 의해 정해진다.
이 주소가 멀티캐스트 주소면 `pkt_type`은 `PACKET_MULTICAST`로 설정되고, 브로드캐스트 주소면 `PACKET_BROADCAST`로 설정되며, 로컬 호스트 주소면 `PACKET_HOST`로 설정된다.   
`eth_type_trans()` 함수에서 이더넷 헤더의 `ethertype`에 따라 SKB의 `protocol` 필드를 설정한다.   
`eth_type_trans()` 함수에서 14 바이트(ETH_HLEN)만큼 SKB의 data 포인터를 이동시킨다. 이렇게 포인터를 이동시키는 이유는 `skb->data`가 현재 상주하는 계층의 헤더를 가리키도록 하기 위함이다.   
패킷이 네트워크 디바이스 드라이버의 Rx 경로인 L2에 있다면 `skb->data`는 L2(이더넷) 헤더를 가리킨다. 
이제 패킷은 `eth_type_trans()` 호출 즉시 3계층으로 이동하고 `skb->data`는 L3(네트워크) 헤더를 가리킨다.   
![IPv4_Packet](https://user-images.githubusercontent.com/49184890/122650256-c4998280-d16c-11eb-8027-e3ab36fae79a.png)   
위 그림은 TCP/UDP Payload를 제외한 패킷의 구조다.   
> IPv4 패킷구조 참고 : https://hack-cracker.tistory.com/112   
SKB는 패킷 헤더(L2,L3,L4 헤더)와 패킷 페이로드를 포함한다. 네트워크 스택 내에서 패킷이 이동하면서 헤더가 추가되거나 제거된다. 
> 예를 들어, 소켓에 의해 로컬에서 생성되어 바깥으로 전송되는 IPv4 패킷에 대해 L3(Network Layer)은 SKB에 IPv4 헤더를 추가한다.   
> IPv4 헤더의 길이는 최소 20바이트이며, IP option 필드가 추가되면 IPv4 헤더의 길이가 최대 60바이트까지 늘어날 수 있다.   
   
각 SKB는 `net_device` 구조체의 인스턴스인 `dev` 항목을 포함한다.   
수신 패킷의 경우 `dev` 항목은 수신 네트워크 장치를 의미하고, 송신 패킷의 경우 `dev` 항목은 송신 네트워크 장치를 의미한다.   
SKB에 연결된 네트워크 장치는 이따금 리눅스 커널 네트워킹 스택에서 SKB의 이동에 영향을 줄 수 있는 정보를 가져오는 데 필요하다.   
> 예를 들어, 앞에서 설명한 것처럼 네트워크 장치의 MTU(최대로 전송할 수 있는 크기) 때문에 패킷이 조각으로 나뉠 수 있다. 전송된 각 SKB는 해당 SKB와 연관된 sock 객체를 포함한다. 패킷이 포워딩된 패킷이면 로컬 호스트 상에서 생성된 것이 아니므로 sk는 NULL이 된다.   
   
수신된 각 패킷은 적절한 네트워크 계층 프로토콜 핸들러로 처리돼야 한다. 
> 예를 들어, IPv4 패킷은 ip_rcv() 함수로 처리되고, IPv6 패킷은 `ipv6_rcv()` 함수로 처리해야 한다. 이후 4장과 8장에서 IPv4와 IPv6 프로토콜 핸들러 등록에 관해 배울 것이다.   
   
IPv4에서는 ICMP 프로토콜을 오류 보고나 정보성 메시지로만 사용한다. 그러나 IPv6에서는 ICMP 프로토콜을 이웃 탐색(ND), 멀티캐스트 리스너 탐색(MLD) 등 다양한 용도로 사용한다.   
   
이웃 탐색(ND)은 IPv4에서는 ARP 프로토콜로, IPv6에서는 NDISC 프로토콜로 처리된다.   
이러한 프로토콜은 차이점이 있다. ARP 프로토콜은 브로드캐스트 요청을 사용하는 반면, NDISC 프로토콜은 ICMPv6 요청을 사용하며, 이때 ICMPv6의 요청은 사실 멀티캐스트 패킷에 해당한다. 이 부분에 대한 자세한 설명은 7장에서 다룰 것이다.   
   
네트워크 스택은 작업을 수행하기 위해 사용자 공간과 통신해야 하며, 이러한 작업으로 경로의 추가/삭제, 이웃 테이블 설정, IPsec 정책과 상태 설정 등이 있다. 이때 사용자 공간과 커널 간의 통신에는 2장에서 다룰 넷링크 소켓을 사용한다.   
   

## 1.2 리눅스 커널 네트워킹 개발 모델

# 2. 넷링크 소켓