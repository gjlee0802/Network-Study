# Network-Study   

# 패킷 지연과 손실
## 패킷 지연의 유형
 1) 전송 지연(transmission delay / store-and-forward delay)   
   ① 패킷의 모든 비트들을 링크로 밀어내는(전송)데 필요한 시간   
   ② 저장 후 전달 지연   
   ③ 전송 지연 = L(packet length, bits) / R(link bandwidth, bps)   
	    출력 링크의 Bandwidth가 R bps (1초 당 R bits)로 전송한다.   
    	이 때 한 패킷의 길이가 L bits라면 출력 링크에 전송하는데에 L/R 초 만큼 시간이 소요된다.   
 2) 전파 지연(propagation delay)   
   ① 출력 링크에서 다음 라우터까지 전파하는데 필요한 시간   
   ② 전파 속도는 링크의 물리 매체(광섬유, 꼬임쌍선 등)에 좌우   
   ③ 전파 지연 = d(두 라우터 간의 거리) / s(매체의 전파 속도(2*10^8 m/sec ~ 3*10^8 m/sec)   
 3) 노드 처리 지연(nodal processing delay)   
   ① 라우터에서의 처리 지연   
   ② 패킷의 헤더를 조사하고 어느 출력 링크로 보낼 지 결정하는 시간   
   ③ 패킷의 비트 수준 오류를 조사하는데 필요한 시간   
   ④ 고속 라우터에서 처리 지연은 수 밀리초   
 4) 큐잉 지연(queuing delay)   
   ① 패킷이 큐에서 출력 링크로 전송되기를 기다리는 시간   
   ② 라우터 혼잡 수준(congestion level)에 좌우(이미 큐에 저장된 패킷들 수에 의해 결정)   
   ③ 일반적으로 수 밀리초에서 수 마이크로초   

# Packet switching & Circuit switching
## Packet switching
- 네트워크 자원을 패킷 단위로 나누어 시간을 공유하므로 회선 효율성이 높다.   
- 트래픽이 많으면 Circuit switching은 네트워크 부하가 감소할 때까지 요청을 차단하나, Packet switching은 Store-and-Forward 방식을 사용하기 때문에 데이터가 들어오는 속도와 나가는 속도를 맞출 필요 없이 각 스테이션에 맞도록 속도를 조절할 수 있다. 이로써 전송 지연이 줄어들고 통신 안정성이 늘어난다.
- 가상 회선 방식: 관련된 패킷을 전부 같은 경로를 통해 전송하는 방법이다. 가상 번호를 기반으로 가상 회선을 구현한다. Call Setup 이 필요하다. 가상 회선의 Call Setup 은 라우팅 테이블에 등록하는 과정이다.
## Circuit switching
### Circuit Switching 개념
하나의 케이블을 여러 개의 Circuit으로 나눠놓는 것. 목적지에 따라 사용할 Circuit을 예약해놓음.
## Packet switching과 Circuit switching의 차이점
- Circuit Switching은 자원을 나누어서 전용으로 사용(No Sharing).    
  데이터를 끊임없이 전송하는 Streaming Data의 경우에는 괜찮지만 Burst(데이터를 가끔 전송)한 경우에 회선이 노는 경우가 많다.
- Burst한 데이터 전송의 경우에는 Packet Switching이 더 유리하다.
  그 이유는 Packet Switching은 나누어서 예약하여 사용하는 방식이 아니라 하나의 자원을 공유하여 사용하기 때문이다. 
  다른 데이터를 전송할 때도 이용할 수 있기 때문에 Circuit보다는 더 효율적으로 전송할 수 있다.

## 패킷 손실이 일어나는 경우
- 앞에서 큐(버퍼)가 무한대 패킷을 저장한다고 가정   
- 실제는 라우터 큐 용량이 유한하고, 큐가 차게되어 도착한 패킷을 저장할 수 없으면 패킷을 버리게 되어(drop), 패킷을 잃어버림(lost)   
- 잃어 버린 패킷은 이전 노드나 출발지 종단에서 재전송 될 수 있음(2계층에서 확인하고 다시 보내도록 함.)   

# OSI 7계층 & TCP/IP 4계층
## OSI 7계층 (A-Penguin-Said-That-Nobody-Drinks-Pepsi)
1. 응용계층 A pplication Layer
   - 응용 서비스를 지원
   - 사용자의 인터페이스를 제공

2. 표현계층 P 
   - 데이터의 부호화/복호화
   - 데이터의 암호화/복호화
   - 데이터 압축 방법

3. 세션계층 S ession Layer
   - 송신 단과 수신 단 사이의 세션 채널을 규정
   - 대화 채널

4. 전송계층 T ransport Layer
   - End-to-End 간의 전송 제어 방법을 규정
   - 연결형 서비스 및 비 연결형 서비스 제공
   - 메시지의 분할 및 재조립
   - 연결형의 경우 End-to-End 간의 흐름 제어, 오류 제어 기능 수행
   - 데이터 구조: 세그먼트(Segment)

5. 네트워크 계층 N etwork Layer
   - 수신 측 주소(번호)를 가지고 경로 설정
   - 송신 측에서 패킷 구성, 수신 측에서 패킷 분해
   - 송신과 수신측에서 논리적 주소의 설정 (IP주소)
   - 트래픽 제어
   - 네트워크 보안
   - 데이터 구조: 패킷 (데이터그램)

6. 데이터 링크 계층 D ata-link Layer
   - 링크 상에서의 프레임 구조, 오류 제어, 흐름 제어, 동기화 등을 규정
   - Error Correction Layer (에러 검출 및 Correction)
   - Broadcast 채널을 공유: Multiple access (여러 기기가 Access Point를 공유)
   - MAC(Multiple Access Control) Layer
   - Link Layer 어드레싱
   - Local area networks: Ethernet, VLANS
   - 브리지(네트워크 세그먼트 간의 연결)
   - 스위치(PC, 스위치, AP를 연결)
   - 데이터 구조: 프레임
   - 두가지 핵심 Sub-layer: MAC layer, Error correction layer

7. 물리 계층 P hysical Layer

## TCP/IP 4계층
1. 응용 계층 Application Layer 
	- 통신 단위: 메시지
	- TCP: HTTP, FTP, TELNET, SMTP
	- UDP: DNS, TFTP, SNMP

2. 전송 계층 Transport Layer
	- End-to-End 간의 전송 제어 방법
	- TCP, UDP
	- 통신 단위: TCP 세그먼트 또는 UDP 데이터그램
	- 서비스 포트

3. 네트워크 계층 Internet Layer
	- 목적지 IP 번호를 가지고 경로 설정
	- IP 주소 지정
	- 데이터구조: 패킷
	- 통신 단위: 데이터그램 ( = 패킷)
	- 프로토콜: IP, ICMP, ARP, RARP, RIP, OSPF, IGMP
	- 라우터
	- 경로 설정 기능 

4. 네트워크 액세스 계층 Network Access Layer
	- OSI 7 Layer: 물리계층, 데이터 링크 계층
	- 통신 단위: 프레임
	- MAC 주소의 지정
	- 허브, 중계기, 스위치, 브리지

# 데이터 링크 계층의 기능
- 데이터 링크 제어:   
 프레임, 오류 제어 및 흐름 제어와 같은 기술을 사용하여 전송 채널을 통해 메시지를 안정적으로 전송할 수 있습니다.   
 https://www.geeksforgeeks.org/stop-and-wait-arq/   
- 다중 액세스 제어:   
 전송과 수신 사이에 전용 링크가 있는 경우에는 데이터 링크 제어 계층으로 충분하지만, 전용 링크가 없는 경우에는 여러 스테이션이 동시에 채널에 액세스할 수 있습니다.    
 따라서 충돌을 줄이고 상호 토크를 피하기 위해 여러 액세스 프로토콜이 필요합니다.   

# Multiple Access Protocols   
https://www.geeksforgeeks.org/multiple-access-protocols-in-computer-network/   
https://inyongs.tistory.com/79   

## Random Access Protocol   
모든 스테이션은 매체의 상태 (유휴 또는 사용 중)에 따라 데이터를 보냅니다.    
다음 두 가지 특징이 있습니다.   
1. 데이터 전송에 대한 고정 된 시간이 없습니다.
2. 데이터를 전송하는 스테이션의 고정 된 순서가 없습니다.

- ALOHA   
https://www.geeksforgeeks.org/local-area-network-lan-technologies/   
무선랜용으로 설계됐지만 공유 매체에도 적용 가능합니다.   
이 경우 여러 스테이션이 동시에 데이터를 전송할 수 있으므로 충돌 및 데이터 왜곡이 발생할 수 있습니다.   
 - Pure ALOHA   
 스테이션이 데이터를 전송하면 확인을 기다립니다.   
 승인이 할당된 시간 내에 오지 않으면 스테이션은 백오프 시간(Tb)이라는 임의의 시간 동안 기다렸다가 데이터를 다시 보냅니다.   
 스테이션마다 대기 시간이 다르기 때문에 추가 충돌 확률은 줄어듭니다.   
 ~~~
 Vulnerable Time = 2* Frame transmission time
 Throughput =  G exp{-2*G}
 Maximum throughput = 0.184 for G=0.5
  ~~~
 - Slotted ALOHA   
 시간을 슬롯으로 나누고 데이터 전송은 이러한 슬롯의 시작 부분에만 허용된다는 점을 제외하면 Pure ALOHA와 유사합니다.   
 스테이션이 허용 시간을 놓칠 경우 다음 슬롯을 기다려야 합니다. 이렇게 하면 충돌 가능성이 줄어듭니다.   
 ~~~
 Vulnerable Time =  Frame transmission time
 Throughput =  G exp{-*G}
 Maximum throughput = 0.368 for G=1
 ~~~
- CSMA   
- CSMA/CD: CSMA/CD는 일단 충돌을 감지하고 충돌이 나면 다시 전송하는 방식.   
- CSMA/CA: CSMA/CA는 사전에 최대한 충돌을 회피하려는 방식.
### CSMA/CD
1. NIC(Network Interface Card)는 3계층인 Network Layer로부터 datagram을 수신하여 Frame을 만든다.
2. NIC는 먼저 CSMA를 수행한다. 채널을 대상으로 Carrier Sense를 한다. 만약 Carrier가 Sense되었으면(채널이 바쁘면) 채널이 사용되고 있지 않을 때까지 계속 기다렸다가 전송한다.
3. 다음으로 CD(Collision Detection)을 수행한다. 케이블에 다른 신호가 없으면 전송 성공.
4. CD를 수행하여 전송 중에 충돌이 인식되었다면 jam signal을 전송하여 전송을 무효화(중단) 한다.
5. 전송 중단을 마친 후, NIC는 binary(exponential) backoff를 수행한다.
	- 충돌이 많을수록 충돌을 줄이기 위해 대기시간의 선택지를 늘린다.
	- m번째 충돌 후 다음 전송까지 대기시간 선택지: {0, 1, 2, . . . , -1}
	- 위처럼 충돌횟수에 따라 기하급수적으로 선택지가 늘어난다.
	- NIC가 위의 선택지에서 랜덤하게 선택한 숫자를 K라 했을 때, 512bit * K 만큼 기다린다.
## Controlled Access   
- Reservation   
- Polling   
- Token Passing   
## Channelization   
- FDMA   
- TDMA   
- CDMA   

# ARP (Address Resolution Protocol)
**IP 주소를 물리적 네트워크 주소로 대응시키기 위해 사용되는 프로토콜이다.**

- One-Hop으로 이루어진 내부 네트워크(Local Area Network)에서만 작동하는 프로토콜이다.   
- ARP Query를 전송하여 Destination IP에 해당하는 MAC주소를 찾음.   
- ARP Query는 Target MAC주소에 FF-FF-FF-FF-FF-FF를 입력하여 전송한다. (모든 MAC주소가 수신.)   
- 수신하는 호스트는 ARP Query 메시지에 있는 Destination IP가 자신의 IP와 일치하면 Source에 자신의 MAC주소를 입력하여 ARP Query에 응답한다.   
- 각각의 호스트는 응답받은 MAC주소를 ARP Table에 기억한다. <IP 주소; MAC 주소; TTL>   
- TTL(Time To Live)은 주소 매핑을 잊어버리기 까지의 시간이다. (일반적으로 20분)   
## (ARP protocol: same LAN)

- A가 datagram을 B에게 보내고 싶을 때, A의 ARP table에 B의 MAC주소가 없다면,   
- A는 ARP query 패킷에 목적지 IP 주소로 B의 IP 주소를 넣어 Broadcast 한다.   
  (Dest MAC 주소는 FF-FF-FF-FF-FF-FF)   
- B는 ARP 패킷을 수신하고 B의 MAC주소를 담아 A에게 답장한다. (Unicast로 응답.)   
- ARP는 “plug and play”이다. ARP 프로토콜은 자동적으로 실행되어 ARP Table이 갱신된다.   

## (Addressing: routing to another LAN, 외부 네트워크의 장치에 송신의 경우)

- B가 외부의 네트워크(라우터를 통과해야하는 네트워크)에 있는 경우에는 B의 MAC 주소를 알 필요가 없다.   
- IP의 네트워크 주소가 일치하지 않으면 외부 네트워크의 IP이므로 게이트웨이(라우터)에 전달된다.   
- 2계층은 One-hop의 Peer-to-Peer에만 신경쓰기 때문에 외부로 향하는 라우터의 MAC주소를 Dest MAC 주소로 한다.   

# Ethernet (1L&2L : Physical Layer&Data-link Layer)

## 이더넷 특징
- OSI 모델의 **물리 계층**에서 신호와 배선, 데이터 링크 계층에서 MAC(media access control) 패킷과 프로토콜의 형식을 정의한다.    
- 이더넷 기술은 대부분 **IEEE 802.3 규약**으로 표준화되었다.   
- 네트워크에 연결된 각 기기들이 **48비트**의 고유의 **MAC 주소**를 가지고 이 주소를 이용해 상호간에 데이터를 주고 받을 수 있도록 만들어졌다.   
- **CSMA/CD**라는 multiple access protocol을 이용하여 통신한다.   

# Network Layer (3L)
## IPv4 주소체계
- IP 주소: 32비트의 이진수   
- 사용 가능한 IP주소 개수는 2의 32승 개다.   
- IP주소는 네트워크 주소(네트워크 ID)와 호스트 주소(호스트 ID)로 구성된다.   

### IP주소의 클래스 구분법
- IP 주소의 앞부분 4비트로 클래스를 식별한다.   
- 0이면 A 클래스, 10이면 B 클래스, 110이면 C 클래스.   
- 클래스 식별의 이유: IP에서 네트워크 주소가 어디까지인지 구분하기 위함.   

A 클래스: 0........... 첫 번째 바이트까지 네트워크 ID, 나머지 호스트 ID.   
B 클래스: 10......... 두 번째 바이트까지 네트워크 ID, 나머지 호스트 ID.   
C 클래스: 110...... 세 번째 바이트까지 네트워크 ID, 네 번째 바이트는 호스트 ID.   
D 클래스: 1110... 멀티캐스트 주소   
E 클래스: 1111.... 차후 사용을 위해 예약됨.   

규모가 큰 네트워크일수록 A 클래스에 가까운 IP를 받는다.   
또는 C 클래스 주소를 여러개 받는다.   

호스트 ID의 자리에 모든 비트가 0이면 특정 호스트가 아니라 네트워크 자체를 의미한다.   
호스트 ID의 자리에 모든 비트가 1이면 내부의 모든 호스트를 의미한다.   

### A 클래스
앞의 첫 번째 바이트(8비트)가 네트워크 ID를 나타낸다.   
네트워크 주소의 범위는 0~127.   
각 네트워크에서 호스트에 할당 가능한 주소의 개수는 ((2^24) – 2) 개.   
(-2는 호스트 ID가 전부 0비트이거나 1비트인 것.)   
전체 IP주소의 50%를 차지한다. (첫 번째 비트가 0으로 시작하는 주소는 A 클래스이기 때문. 나머지는 0으로 시작)   
A 클래스의 네트워크 ID는 전세계에서 128개 기관만 가질 수 있다.   

### B 클래스
네트워크 주소의 범위는 128.0 ~ 191.255   
전체 IP주소의 25%를 차지한다.   
각 네트워크에서 호스트에 할당 가능한 주소의 개수는 ((2^16) –2)개   

### C 클래스
전체 IP주소의 12.5%를 차지한다.   

### 서브넷
- 서브넷 개념 정의: 하나의 IP 네트워크 주소(A Class 혹은 B Class의 주소)를 네트워크 내부에서 적절히 분배하여 실제로는 여러개의 서로 연결된 지역 네트워크로 사용하는 것.   
- 필요성: 하나의 네트워크로 수많은 호스트들을 한꺼번에 관리하기는 힘들기 때문에 필요.   

### 서브넷 마스크

클래스에 대한 표준 네트워크 마스크는 아래와 같다.   
Class A: 255.0.0.0 (11111111 00000000 00000000 00000000)   
Class B: 255.255.0.0 (11111111 11111111 00000000 00000000)   
Class C: 255.255.255.0 (11111111 11111111 11111111 00000000)   
   
#### Class C의 주소에서 서브넷 마스크를 255.255.255.224로 설정했을 경우.   
255.255.255.224 ( = 11111111 11111111 11111111 11100000)   
앞의 255는 2진수로 110..이므로 Class C이다.   
Class C는 앞의 3바이트로 네트워크ID를 식별하여 라우팅하고   
마지막 바이트 앞의 3비트로 서브넷을 구분한다.   
마지막 바이트의 3비트를 마스킹하는 것은 네트워크 내부의 서브넷을 최대 8개 사용한다는 의미.   

### 사설 주소
- IP 주소 중 인터넷에서 사용하지 않는 주소.   
- IP 주소 부족을 해결하는 방안.   

Class A: 10.0.0.0 ~ 10.255.255.255   
Class B: 172.16.0.0 ~ 172.31.255.255   
Class C: 192.168.0.0 ~ 192.168.255.255   

### data plane과 control plane 개념

## Network layer의 역할

- Segment를 호스트에게 전송(Segment에 추가 데이터를 붙여서 Packet으로 전송).   
- 전송부에서는 segment들을 datagram(혹은 Packet)으로 담아서 전송한다.   
- 수신부에서는 Segment부분을 Transport layer로 올려줌.   
- Network layer 프로토콜은 모든 호스트와 라우터에 구현된다.   
- 라우터는 IP 패킷(datagram)의 헤더를 보고 어느 경로로 보낼지 결정한다.   

## Network layer의 두가지 핵심 기능

- **forwarding**: 라우터에서 다음 라우터에게 패킷을 전달해주는 것.   
- **routing**: 최적의 경로를 찾는 것.   

패킷 헤더의 dest IP주소를 보고 routing algorithm을 통해 업데이트한 local forwarding table에서   
적절한 출력링크를 찾아 전송한다.   

## Network layer service models

3계층의 Service Model은 Best-effort 방식이다(제대로 전송하려고 최선을 다할 뿐 보장은 하지 않는다). -> 신뢰성을 보장하는 것은 4계층에서 한다.   
3계층에는 Connection이 필요하지 않다(Connection-less service).   

## Longest prefix matching
IP 주소 앞 부분이 가장 길게 일치하는 항목을 찾는 방법.   

## The Internet network layer 구성요소
- routing protocol (path selection; RIP OSPF, BGP)   
- forwarding table   
- IP protocol   
- ICMP(Internet Control Message) protocol  (error reporting; router signaling)   

## IP datagram format

![image00002](https://user-images.githubusercontent.com/49184890/122635657-34cde700-d120-11eb-9273-542d510c62f2.PNG)   

- IP datagram의 헤더는 총 20Bytes이다.   
- “20Bytes의 IP헤더 + 20Bytes TCP헤더 + app layer overhead” 로 구성된다.   
- TTL: 최대 통과 가능한 Hop의 개수에 제한을 걺 (무한루프 방지).   
- Upper layer: segment가 TCP인지 UDP인지 결정. 네트워크 계층의 제어를 위한 ICMP 정보가 들어있을 수 있다.   
- HDR Checksum: 20바이트인 헤더를 2바이트씩 쪼개어 더하고 1의 보수를 취한 값이다. 라우터를 통과하면서 업데이트된다 (TTL이 계속 감소하여 헤더 값이 바뀐다).    
- IP address: IPv4는 32bits, IPv6는 128bits이다.   

### (IP fragmentation : IP 단편화, reassembly)

- 위의 IP datagram format에서 헤더 부분의 { 16-bit ID | flags | fragment offset } 부분이 단편화와 관련있다.   
- 링크의 종류가 다르면 최대 보낼 수 있는 패킷의 크기도 다르기 때문에 패킷을 쪼개어 보내야 한다.   
- 수신하게 되는 host는 쪼개어 온 패킷들을 다시 합친다(중간의 라우터들은 합치지 않는다).   
- MTU(Maximum Transfer Unit): 2계층 Frame의 Payload (즉, IP datagram)의 크기를 지칭.   

### 예를 들어, 4000 Bytes dtagram을 보내야하는데 MTU가 1500 Bytes이면 3개의 datagram들로 나누어 전송.   
- ID는 모두 같은 값.   
- fragment flag가 1이면 뒤에 더 이어질 데이터가 있다는 의미이다(마지막 패킷은 0).    
- offset은 합칠 패킷의 순서를 알기 위한 값이다(8 Bytes단위로 끊음).    
- length는 헤더(20 Bytes)를 포함한 크기이므로 실제 segment의 크기는 length – 20 Bytes이다.   

## IP addressing
### (IP addressing: CIDR)
### (IP addresses: how to get one?)

# Transport Layer (4L)
## TCP/IP Protocol
https://tldp.org/LDP/tlk/net/net.html    

TCP/IP Frame Structure    
![tcp_ip_frame](https://user-images.githubusercontent.com/49184890/107650853-3e52c880-6cc2-11eb-8f10-780c7dd8b4d9.gif)    

+ Checksum?   
  Checksum은 데이터 전송이나 저장시 에러 검출을 위해 추가되는 작은 데이터이다.   
  Checksum에는 다음과 같은 방식이 있다. CRC는 Checksum의 한 종류이다.   
  - Parity 방식   
  데이터의 모든 비트 또는 바이트를 XOR하여 Checksum을 구하는 방식이다.   
  - Modular Sum 방식   
  데이터의 모든 바이트를 더한 후 2's complement하여 Checksum을 구하는 방식이다.   
  - CRC 방식   
  2개의 데이터가 서로 자리가 바뀌었을 때는 Parity나 Modular Sum 방식으로 검출할 수 없다.   
  하지만, CRC 방식은 이러한 경우에도 검출할 수 있는 방식이다.   
  CRC는 가장 많이 사용되는 Checksum 방식으로 에러 검출 성능이 우수하다.   
