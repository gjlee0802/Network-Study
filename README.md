# Network-Protocol-Study   

# Multiple Access Protocols   

https://www.geeksforgeeks.org/multiple-access-protocols-in-computer-network/   

https://inyongs.tistory.com/79   
- Slotted ALOHA   
- Pure ALOHA   
- CSMA   
- CSMA/CD   

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
