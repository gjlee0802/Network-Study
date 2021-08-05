# Network-Study   

# 1. 패킷 지연과 손실
## 1.1 패킷 지연의 유형
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

# 2. Packet switching & Circuit switching
## 2.1 Packet switching
- 네트워크 자원을 패킷 단위로 나누어 시간을 공유하므로 회선 효율성이 높다.   
- 트래픽이 많으면 Circuit switching은 네트워크 부하가 감소할 때까지 요청을 차단하나, Packet switching은 Store-and-Forward 방식을 사용하기 때문에 데이터가 들어오는 속도와 나가는 속도를 맞출 필요 없이 각 스테이션에 맞도록 속도를 조절할 수 있다. 이로써 전송 지연이 줄어들고 통신 안정성이 늘어난다.
- 가상 회선 방식: 관련된 패킷을 전부 같은 경로를 통해 전송하는 방법이다. 가상 번호를 기반으로 가상 회선을 구현한다. Call Setup 이 필요하다. 가상 회선의 Call Setup 은 라우팅 테이블에 등록하는 과정이다.
## 2.2 Circuit switching
### Circuit Switching 개념
하나의 케이블을 여러 개의 Circuit으로 나눠놓는 것. 목적지에 따라 사용할 Circuit을 예약해놓음.
## 2.3 Packet switching과 Circuit switching의 차이점
- Circuit Switching은 자원을 나누어서 전용으로 사용(No Sharing).    
  데이터를 끊임없이 전송하는 Streaming Data의 경우에는 괜찮지만 Burst(데이터를 가끔 전송)한 경우에 회선이 노는 경우가 많다.
- Burst한 데이터 전송의 경우에는 Packet Switching이 더 유리하다.
  그 이유는 Packet Switching은 나누어서 예약하여 사용하는 방식이 아니라 하나의 자원을 공유하여 사용하기 때문이다. 
  다른 데이터를 전송할 때도 이용할 수 있기 때문에 Circuit보다는 더 효율적으로 전송할 수 있다.

## 패킷 손실이 일어나는 경우
- 앞에서 큐(버퍼)가 무한대 패킷을 저장한다고 가정   
- 실제는 라우터 큐 용량이 유한하고, 큐가 차게되어 도착한 패킷을 저장할 수 없으면 패킷을 버리게 되어(drop), 패킷을 잃어버림(lost)   
- 잃어 버린 패킷은 이전 노드나 출발지 종단에서 재전송 될 수 있음(2계층에서 확인하고 다시 보내도록 함.)   

# 3. OSI 7계층 & TCP/IP 4계층
## 3.1 OSI 7계층 (A-Penguin-Said-That-Nobody-Drinks-Pepsi)
1. 응용계층 **A** pplication Layer
   - 응용 서비스를 지원
   - 사용자의 인터페이스를 제공

2. 표현계층 **P** resentation layer
   - 데이터의 부호화/복호화
   - 데이터의 암호화/복호화
   - 데이터 압축 방법

3. 세션계층 **S** ession Layer
   - 송신 단과 수신 단 사이의 세션 채널을 규정
   - 대화 채널

4. 전송계층 **T** ransport Layer
   - End-to-End 간의 전송 제어 방법을 규정
   - 연결형 서비스 및 비 연결형 서비스 제공
   - 메시지의 분할 및 재조립
   - 연결형의 경우 End-to-End 간의 흐름 제어, 오류 제어 기능 수행
   - 데이터 구조: 세그먼트(Segment)

5. 네트워크 계층 **N** etwork Layer
   - 수신 측 주소(번호)를 가지고 경로 설정
   - 송신 측에서 패킷 구성, 수신 측에서 패킷 분해
   - 송신과 수신측에서 논리적 주소의 설정 (IP주소)
   - 트래픽 제어
   - 네트워크 보안
   - 데이터 구조: 패킷 (데이터그램)

6. 데이터 링크 계층 **D** ata-link Layer
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

7. 물리 계층 **P** hysical Layer

## 3.2 TCP/IP 4계층
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

# 4. Data-link Layer (2L)
## 4.1 데이터 링크 계층의 기능
- 데이터 링크 제어:   
 프레임, 오류 제어 및 흐름 제어와 같은 기술을 사용하여 전송 채널을 통해 메시지를 안정적으로 전송할 수 있습니다.   
 https://www.geeksforgeeks.org/stop-and-wait-arq/   
- 다중 액세스 제어:   
 전송과 수신 사이에 전용 링크가 있는 경우에는 데이터 링크 제어 계층으로 충분하지만, 전용 링크가 없는 경우에는 여러 스테이션이 동시에 채널에 액세스할 수 있습니다.    
 따라서 충돌을 줄이고 상호 토크를 피하기 위해 여러 액세스 프로토콜이 필요합니다.   

## 4.2 Multiple Access Protocols   
https://www.geeksforgeeks.org/multiple-access-protocols-in-computer-network/   
https://inyongs.tistory.com/79   

### Random Access Protocol   
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
#### CSMA/CD
1. NIC(Network Interface Card)는 3계층인 Network Layer로부터 datagram을 수신하여 Frame을 만든다.
2. NIC는 먼저 CSMA를 수행한다. 채널을 대상으로 Carrier Sense를 한다. 만약 Carrier가 Sense되었으면(채널이 바쁘면) 채널이 사용되고 있지 않을 때까지 계속 기다렸다가 전송한다.
3. 다음으로 CD(Collision Detection)을 수행한다. 케이블에 다른 신호가 없으면 전송 성공.
4. CD를 수행하여 전송 중에 충돌이 인식되었다면 jam signal을 전송하여 전송을 무효화(중단) 한다.
5. 전송 중단을 마친 후, NIC는 binary(exponential) backoff를 수행한다.
	- 충돌이 많을수록 충돌을 줄이기 위해 대기시간의 선택지를 늘린다.
	- m번째 충돌 후 다음 전송까지 대기시간 선택지: {0, 1, 2, . . . , -1}
	- 위처럼 충돌횟수에 따라 기하급수적으로 선택지가 늘어난다.
	- NIC가 위의 선택지에서 랜덤하게 선택한 숫자를 K라 했을 때, 512bit * K 만큼 기다린다.
### Controlled Access   
- Reservation   
- Polling   
- Token Passing   
### Channelization   
- FDMA   
- TDMA   
- CDMA   

## 4.2 ARP (Address Resolution Protocol)
**IP 주소를 물리적 네트워크 주소로 대응시키기 위해 사용되는 프로토콜이다.**

- One-Hop으로 이루어진 내부 네트워크(Local Area Network)에서만 작동하는 프로토콜이다.   
- ARP Query를 전송하여 Destination IP에 해당하는 MAC주소를 찾음.   
- ARP Query는 Target MAC주소에 FF-FF-FF-FF-FF-FF를 입력하여 전송한다. (모든 MAC주소가 수신.)   
- 수신하는 호스트는 ARP Query 메시지에 있는 Destination IP가 자신의 IP와 일치하면 Source에 자신의 MAC주소를 입력하여 ARP Query에 응답한다.   
- 각각의 호스트는 응답받은 MAC주소를 ARP Table에 기억한다. <IP 주소; MAC 주소; TTL>   
- TTL(Time To Live)은 주소 매핑을 잊어버리기 까지의 시간이다. (일반적으로 20분)   
### (ARP protocol: same LAN)

- A가 datagram을 B에게 보내고 싶을 때, A의 ARP table에 B의 MAC주소가 없다면,   
- A는 ARP query 패킷에 목적지 IP 주소로 B의 IP 주소를 넣어 Broadcast 한다.   
  (Dest MAC 주소는 FF-FF-FF-FF-FF-FF)   
- B는 ARP 패킷을 수신하고 B의 MAC주소를 담아 A에게 답장한다. (Unicast로 응답.)   
- ARP는 “plug and play”이다. ARP 프로토콜은 자동적으로 실행되어 ARP Table이 갱신된다.   

### (Addressing: routing to another LAN, 외부 네트워크의 장치에 송신의 경우)

- B가 외부의 네트워크(라우터를 통과해야하는 네트워크)에 있는 경우에는 B의 MAC 주소를 알 필요가 없다.   
- IP의 네트워크 주소가 일치하지 않으면 외부 네트워크의 IP이므로 게이트웨이(라우터)에 전달된다.   
- 2계층은 One-hop의 Peer-to-Peer에만 신경쓰기 때문에 외부로 향하는 라우터의 MAC주소를 Dest MAC 주소로 한다.   

# 5. Ethernet (1L&2L : Physical Layer&Data-link Layer)

## 5.1 이더넷 특징
- OSI 모델의 **물리 계층**에서 신호와 배선, **데이터 링크 계층**에서 MAC(media access control) 패킷과 프로토콜의 형식을 정의한다.    
- 이더넷 기술은 대부분 **IEEE 802.3 규약**으로 표준화되었다.   
- 네트워크에 연결된 각 기기들이 **48비트**의 고유의 **MAC 주소**를 가지고 이 주소를 이용해 상호간에 데이터를 주고 받을 수 있도록 만들어졌다.   
- **CSMA/CD**라는 multiple access protocol을 이용하여 통신한다.   

# 6. Network Layer (3L)
## 6.1 IPv4 주소체계
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

## 6.2 data plane과 control plane 개념

## 6.3 Network layer의 역할
- Segment를 호스트에게 전송(Segment에 추가 데이터를 붙여서 Packet으로 전송).   
- 전송부에서는 segment들을 datagram(혹은 Packet)으로 담아서 전송한다.   
- 수신부에서는 Segment부분을 Transport layer로 올려줌.   
- Network layer 프로토콜은 모든 호스트와 라우터에 구현된다.   
- 라우터는 IP 패킷(datagram)의 헤더를 보고 어느 경로로 보낼지 결정한다.   

## 6.4 Network layer의 특징
### 두가지 핵심 기능
- **forwarding**: 라우터에서 다음 라우터에게 패킷을 전달해주는 것.   
- **routing**: 최적의 경로를 찾는 것.   

패킷 헤더의 dest IP주소를 보고 routing algorithm을 통해 업데이트한 local forwarding table에서   
적절한 출력링크를 찾아 전송한다.   

### Network layer service models
3계층의 Service Model은 Best-effort 방식이다(제대로 전송하려고 최선을 다할 뿐 보장은 하지 않는다). -> 신뢰성을 보장하는 것은 4계층에서 한다.   
3계층에는 Connection이 필요하지 않다(Connection-less service).   

### Internet network layer 구성요소
- routing protocol (path selection; RIP OSPF, BGP)   
- forwarding table   
- IP protocol   
- ICMP(Internet Control Message) protocol  (error reporting; router signaling)   

## 6.5 IP datagram format
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

## 6.6 IP addressing
### (IP addressing: CIDR)
> 참고 : https://kim-dragon.tistory.com/9   

CIDR: **Classless** Inter-Domain Routing   
Class개념을 이용하지 않음.   
host를 full로 사용하지 않는 낭비를 줄이기 위해 network주소를 더 많이 표현하고 표현가능한 host주소를 줄임.   

> TCP/IP 네트워크가 1980년대 인터넷으로 성장함에 따라, 더욱 유동적인 주소 체계가 급속히 필요해졌다. 이는 서브넷, Variable-Length 서브넷, 그리고 마침내 사이더의 개발을 야기하였다. 이전의 클래스 구분이 이제는 무시되므로, 새로운 시스템은 클래스 없는 라우팅이라고 불렸으며, 상대적으로 이전의 시스템은 클래스 있는 라우팅으로 불리게 되었다.

### (IP addresses: how to get one?)
host가 IP 주소를 어떻게 얻을까?   
-> DHCP: Dynamic Host Configuration Protocol (동적으로 IP주소를 호스트에게 주는 프로토콜)   

### DHCP client-server scenario
DHCP server가 IP를 부여한다.
1. client는 DHCP서버가 있는지 확인하기 위해 DHCP discover메시지를 broadcast한다. (Dest IP: 255.255.255.255)
2. DHCP server는 답장으로 IP address(yiaddr)를 하나 제안한다.(DHCP offer)
3. client는 제안받은 IP(yiaddr)를 사용하겠다는 것을 알리기 위해 DHCP request를 broadcast한다.
4. DHCP server는 ACK메시지를 broadcast하여 yiaddr을 사용하기로 했음을 알린다.

> DHCP discover(client->server) -> DHCP offer(server->client) -> DHCP request(client->server) -> DHCP ACK(server->client)   


### (DHCP: more than IP addresses)
- IP주소만 제공하는 것이 아니라 router(Gateway)의 주소에 대한 정보도 알려준다.   
- DNS server의 이름과 ip address를 알려준다.   
- Subnet mask(network mask)를 제공한다.

### (IP addresses: how to get one?)
20 bits를 network 주소로 사용하게 된다면 나머지 32-20 = 12 bits는 host 주소로 하용한다.   
12 bits로 가질 수 있는 host 주소의 개수는 (2^12 – 2) 개다.

### Hierarchical addressing: more specific routes
- 라우팅할 때에는 네트워크 주소가 더 길게 매칭이 되는 라우터로 라우팅한다.

## 6.7 NAT: network address translation
- 내부 네트워크는 사설 IP를 사용한다. (내부 네트워크는 사설 네트워크로 구성)   
- 내부 네트워크들과 연결된 외부로 향하는 라우터 하나만 공인 IP를 사용한다.   
- NAT은 사설 IP를 공인 IP로 바꾸어 통신하도록 도와준다.   
- NAT translation table에 { WAN side addr : LAN side addr }을 기록한다.   
- Port forwarding을 통해 사설 ip 호스트에서 서버를 운영할 수 있도록 한다.   
- Port forwarding은 (외부ip:포트 -> 사설ip:포트) 라우팅 경로를 미리 설정하는 것이다.   

## 6.8 ICMP: Internet Control Message Protocol
- 오류나 request/reply 메시지(Control messages)를 전달하기 위해서 Type과 Code를 이용하여 알린다.   
- IPv4일 경우에는 정보 오류를 알려주기 위한 기능만을 제공하지만 IPv6에서는 더 많은 기능을 제공한다.   
- 예를 들어 TTL이 0이 되어 패킷이 drop되면 source에게 (Type:11, Code:0)을 통해 알려준다.   

### (Traceroute and ICMP: ICMP를 이용한 Traceroute 구현)
- 호스트가 TTL이 1인 패킷을 전달하면 first hop 라우터에서 TTL이 0이므로 Control message를 호스트에게 보내준다.   
- 호스트는 라우터로부터 받은 Control message 응답시간을 보고 전송 delay를 유추한다.   
- 마찬가지로 호스트에서 TTL이 2, 3, 4, 5...인 패킷을 전달하여 라우터들의 Control message 응답시간을 측정한다.   
- 마지막에 dest까지 도달하면 접속하려는 port번호가 사용하지 않는 port이므로  dest port unreachable 응답을 받는다.   

## 6.9 IPv6와 IPv4의 공존: Tunneling기법
- 어떤 라우터는 IPv6로 동작하고 어떤 라우터는 IPv4로 동작한다.   
- IPv4는 IPv6와 호환성 문제가 있다.   
- IPv6 Router=> IPv4 Router의 경우: IPv6의 datagram을 통째로 IPv4의 segment에 넣어 새로운 패킷을 전송한다.   
- IPv4 Router=> IPv6 Router의 경우: IPv4 패킷의 segment를 패킷으로 보낸다(IPv4 데이터를 때어내어 IPv6 패킷만 보냄).   
- IPv6 라우터는 패킷 내부에 IPv6의 datagram이 들어있는지 판단할 수 있어야 한다(헤더의 upper layer로 판단).   
- 
## Routing algorithm classification
Q: global or decentralized information?   
 - global: 그래프에 대한 모든 정보(Vertex, cost)를 알고 구하는 것. => Algorithm: “link state” algorithms
 - decentralized(distributed): 인접한 정보(인접한 Vertex, cost)만 아는 것. => Algorithm: “distance vector” algorithms
   
### Link state routing algorithm
- Dijkstra's algorithm
- Bellmanford algorithm
#### Oscillations possible
cost를 traffic으로 볼 때 발생하는 문제점.   

### Distance vector algorithm
- Bellmanford **equation** (dynamic programming)
#### Routing loops
link cost가 변화하는 경우 발생 가능한 문제점.
## Inter-AS tasks (eBGP, iBGP)
- 어떤 네트워크(AS)로 접근할 수 있는지 알아야 한다(**eBGP : external BGP**).   
- AS 내의 모든 라우터들에게 정보를 공유해야 한다(**iBGP : internal BGP**).   
## Choosing among multiple ASes
만약 x에 연결된 AS가 두가지 이상이라면   
1. x로 접근하기 위해 거쳐야 하는 AS의 수가 적은 길을 택한다.   
2. 만약 거쳐야 하는 AS 수가 같으면, AS 내부에서 Shortest Path를 찾는다(Hot-potato 방식).   
   
> Hot potato: 전체적인 cost로 정확하게 판단하는 것이 아니라 intra-AS에서 least-cost를 찾는 것.    
## Intra-AS Routing
### RIP (Routing Information Protocol) : distance vector algorithm
#### RIP : example
cost를 홉의 수로 판단하여 기록한다.   
![image00004](https://user-images.githubusercontent.com/49184890/124355489-69778d80-dc4c-11eb-85bf-2308536bcb25.PNG)   
D가 A로부터 z로 향하는 홉 수가 더 적은 4홉인 정보를 받으면, 다음과 같이 z로 향하는 정보를 수정한다.   
![image00005](https://user-images.githubusercontent.com/49184890/124355495-6bd9e780-dc4c-11eb-9849-395b0b3149b8.PNG)   
#### RIP : link failure, recovery
정보 갱신 여부에 상관없이 주기적으로(30초) 계속 정보를 알려줘야 한다.   
계속 정보가 넘어오지 않는 경우에는 죽은 것으로 판단하여 테이블을 업데이트 한다.   
업데이트를 한 정보를 주변 이웃 노드들에게 알려준다.   
#### RIP : table processing
RIP routing table은 Application-level의 프로세스인 route-d(daemon)에 의해 관리된다.   
정보는 UDP로 전달한다.   

### OSPF (Open Shortest Path First) : link-state algorithm
AS 전체에 대한 모든 정보를 다 알아야 하며, 모든 노드들이 모든 정보를 공유한다.   
각각의 노드에서 topology map을 구성할 수 있다.   
OSPF는 IS-IS routing protocol과 거의 동일하다.   

# 7. Transport Layer (4L)


## Multiplexing / demultiplexing
- multiplexing(송신부): Transport 헤더를 추가.   
- demultiplexing(수신부): 올바른 소켓으로 Segments를 전달하기 위해 헤더 정보를 참조.   
## TCP와 UDP
### TCP 특징
TCP는 다음의 동작을 통해 신뢰성을 보장한다.   
- congestion control   
- flow control   
- connection setup   
- TCP는 소켓이 1 대 1로 연결되어야 한다.   
### UDP 특징
- UDP는 데이터의 손실을 검사하지만 다시 보내라는 요청을 하지 않는다.   
- no-frills extension of “best-effort” IP   
- no connection establishment   
- no connection state at sender, receiver   
- small HDR size   
- no congestion control: TCP의 경우에는 버퍼를 고려하여 속도를 조절한다.   
- UDP는 소켓이 1 대 N으로 연결 가능하다.   

## Reliable Data Transfer
rdt 버전별 특징
- rdt 2.0: ACK와 NAK 메세지를 이용하여 정상 송수신 확인   
- rdt 2.1: ACK/NAK 메시지가 깨진 경우를 고려   
- rdt 2.2: ACK 메세지만을 이용하여 ACK와 NAK을 표현   
- rdt 3.0: Timeout을 도입하여 에러 검출된 경우뿐만 아니라 loss(아예 전달이 되지 않은 것)를 고려   
### rdt 1.0
### rdt 2.0
### rdt 2.1
만약 ACK/NAK에서 에러가 검출되면?   
문제점: 패킷이 중복될 수 있다.   
해결책: Sequence number을 패킷에 붙여서 중복된 패킷을 검출한다(Sequence nubmer는 0과 1로만 이루어져도 된다).   
#### rdt 2.1 : Sender FSM
<img width="500" alt="image00008" src="https://user-images.githubusercontent.com/49184890/124355800-0686f600-dc4e-11eb-9e28-42ed87b9ed36.PNG">   

#### rdt 2.1 : Receiver FSM
<img width="500" alt="image00009" src="https://user-images.githubusercontent.com/49184890/124355803-0850b980-dc4e-11eb-87c3-db0c2d2e7251.PNG">   

### rdt 2.2
Sequence number 1에 대한 ACK을 전송하여 Seq num 0에 대한 NAK을 알려줄 수 있다.   
-> NAK 메세지가 별도로 존재하지 않으며, ACK 메세지로만 ACK과 NAK을 알려줄 수 있다.   
### rdt 3.0
timeout 개념을 도입했다.   
충분한 시간 내에 ACK/NAK 메시지가 수신되지 않았으면 loss 된 것으로 판단한다.   

#### rdt 3.0 : Sender FSM
<img width="500" alt="image00010" src="https://user-images.githubusercontent.com/49184890/124387405-7f9f4f80-dd19-11eb-90f9-3b580ed02a05.PNG">   

#### rdt 3.0 : in action
<img width="500" alt="image00011" src="https://user-images.githubusercontent.com/49184890/124387429-a198d200-dd19-11eb-8687-cc877b2850c8.PNG">   

## Stop-and-Wait vs Pipelining
Stop-and-Wait : 일단 전송한 다음에 멈추고 ACK/NAK를 기다려서 다시 처리하는 방식   
<img width="457" alt="image00013" src="https://user-images.githubusercontent.com/49184890/124387593-2edc2680-dd1a-11eb-8988-a680ec268302.PNG">   
Pipelining : 한번에 여러 패킷을 전송하고 전송한 여러 패킷에 대한 ACK을 수신하는 방식
<img width="466" alt="image00014" src="https://user-images.githubusercontent.com/49184890/124387615-43202380-dd1a-11eb-8e61-a718b37723d2.PNG">   

## Pipelined protocols
- Go-back-N : N 개의 패킷을 전송하고 기다린다. **Cumulative ack을 사용**한다.      
- Selective repeat : 독립적인 여러 ACK을 각각의 패킷에게 응답한다. 송신부는 ACK 응답을 받지 못한 패킷에 대해서만 다시 전달한다.   

### Go-back-N
- N개의 패킷을 전송하고 기다린다.   
- 수신측은 Cumulative ACK만을 전송한다.   
- Cumulative ACK는 연속적으로 받은 패킷에 대해 잘 받았음을 알려주는 ack이다.   
- 송신부는 가장 오래된 in-flight 패킷에서 타이머를 가동한다. 타이머가 expire 됐을 때(시간이 만료했을 때) 모든 패킷을 다시 전송한다.   
> 장점
> - 단순하다.
> - 수신부에 버퍼가 필요없다. 
#### GBN : sender extended FSM
<img width="500" alt="image00015" src="https://user-images.githubusercontent.com/49184890/124472614-4b8b6380-ddd9-11eb-9bf2-5d1c13180a9f.PNG">   

#### GBN: receiver extended FSM
<img width="500" alt="image00016" src="https://user-images.githubusercontent.com/49184890/124472688-6067f700-ddd9-11eb-8245-9b48621a043a.PNG">   
수신부는 받을 패킷의 번호만 유지하면 된다.   

### Selective Repeat
- 수신부는 독립적인 ACK를 각각의 패킷에게 응답한다.   
- 송신부는 제대로 수신되지 않은 패킷에 대해서만 다시 전달한다.   
- GBN보다 효율적인 프로토콜이다.

> GBN과의 차이점   
> 1. 각각의 패킷들에 대해서 Timeout을 체크한다(타이머가 각 패킷에 대해서 작동해야 한다). GBN의 경우에는 가장 오래된 패킷에 대한 타이머 하나만 유지하면 된다.   
> 2. GBN의 경우 receiver에 버퍼가 필요없지만 Selective repeat의 경우에는 receiver에도 N사이즈의 Receive window만큼 버퍼가 있어야 한다(버퍼에 정상 수신된 버퍼는 미리 저장한다).   

#### Selective repeat : 딜레마, Window 사이즈와 Sequence number 사이의 관계

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


# Application Layer
## DNS: domain name system
참고
> https://it-mesung.tistory.com/180   
> https://samsikworld.tistory.com/489   
> https://www.cloudflare.com/ko-kr/learning/dns/what-is-dns/   
   
### DNS는 A distributed(분산된), hierarchical(계층적) database이다.
![84001753-3cea3700-a9a2-11ea-801a-2f4acaf3e3c9](https://user-images.githubusercontent.com/49184890/128291782-76a4babf-cffc-4cef-8cca-097e8c4cfa2f.png)   
- http://www.naver.com/index.html : 이런 형식을 URL이라고 부른다.   
- www.naver.com : 이런 형식을 **Host Name**이라고 부른다.   
- .com : 이것은 **Top-level Domain Name(TLD)** 이라고 부른다.   
- .naver.com : 이것은 **Second-level Domain Name**이라고 부른다.   

### DNS 조회 과정
8단계로 과정 이해하기
- 사용자가 웹 브라우저에 'example.com'을 입력하면, 쿼리가 인터넷으로 이동하고 DNS 재귀 확인자가 이를 수신합니다.   
- 이어서 확인자가 DNS 루트 이름 서버(.)를 쿼리합니다.   
- 다음으로, 루트 서버가, 도메인에 대한 정보를 저장하는 최상위 도메인(TLD) DNS 서버(예: .com 또는 .net)의 주소로 확인자에 응답합니다. example.com을 검색할 경우의 요청은 .com TLD를 가리킵니다.   
- 이제, 확인자가 .com TLD에 요청합니다.   
- 이어서, TLD 서버가 도메인 이름 서버(example.com)의 IP 주소로 응답합니다.   
- 마지막으로, 재귀 확인자가 도메인의 이름 서버로 쿼리를 보냅니다.   
- 이제, example.com의 IP 주소가 이름 서버에서 확인자에게 반환됩니다.   
- 이어서, DNS 확인자가, 처음 요청한 도메인의 IP 주소로 웹 브라우저에 응답합니다.   
   
DNS 조회의 8단계를 거쳐 example.com의 IP 주소가 반환되면, 이제 브라우저가 웹 페이지를 요청할 수 있습니다.   
- 브라우저가 IP 주소로 HTTP 요청을 보냅니다.   
- 해당 IP의 서버가 브라우저에서 렌더링할 웹 페이지를 반환합니다(10단계).   
   
### DNS 쿼리의 유형
- 재귀 쿼리(recursive query) : 이와 같이 로컬 DNS 서버가 열 DNS 서버를 차례대로(Local DNS 서버 -> Root DNS 서버 -> com DNS 서버 -> naver.com DNS 서버) 물어보며 답을 찾는 과정이다.    
> 요청 순서 : local DNS-> root DNS -> TLD DNS -> authoritative DNS   
> Root DNS가 제일 바빠짐   
- 반복 쿼리(iterated query) : 이 경우, DNS 클라이언트는 DNS 서버가 가능한 최상의 응답을 반환하도록 합니다. 쿼리한 DNS 서버가 쿼리 이름과 일치하는 이름을 갖고 있지 않은 경우, 하위 수준의 도메인 네임스페이스에 대해 권한 있는 DNS 서버에 대한 참조를 반환합니다.   
> "나는 모르는데 이 서버한테 물어봐~"   
> Local DNS가 제일 바빠짐   
- 비재귀 쿼리
