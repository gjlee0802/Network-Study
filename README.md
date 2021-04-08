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
## OSI 7계층
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

7. 물리 계층 P hysical Layer

## TCP/IP 4계층

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
- CSMA/CD   
## Controlled Access   
- Reservation   
- Polling   
- Token Passing   
## Channelization   
- FDMA   
- TDMA   
- CDMA   

# ARP (Address Resolution Protocol)
IP 주소를 물리적 네트워크 주소로 대응시키기 위해 사용되는 프로토콜
 
# Ethernet 

## 이더넷 특징
- OSI 모델의 **물리 계층**에서 신호와 배선, 데이터 링크 계층에서 MAC(media access control) 패킷과 프로토콜의 형식을 정의한다.    
- 이더넷 기술은 대부분 **IEEE 802.3 규약**으로 표준화되었다.   
- 네트워크에 연결된 각 기기들이 **48비트**의 고유의 **MAC 주소**를 가지고 이 주소를 이용해 상호간에 데이터를 주고 받을 수 있도록 만들어졌다.   
- **CSMA/CD**라는 multiple access protocol을 이용하여 통신한다.


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
