# Network-Protocol-Study   

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
