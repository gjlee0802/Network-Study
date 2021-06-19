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


# 들어가며
## 네트워크 디바이스
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
   
### promiscuity 카운터
promiscuity 카운터가 0보다 크면 네트워크 스택은 로컬 호스트를 목적지로 하지 않는 패킷도 버리지 않는다.   
예를 들면, 이것은 사용자 공간(userspace)의 미가공 소켓(raw socket)을 열어 이러한 유형의 트래픽도 수집하고자 할 때 사용하는 tcpdump나 wireshark 같은 패킷 분석기에서 사용된다.   
패킷 분석기를 시작할 때마다 카운터가 1 증가, 종료하면 1 감소한다. 그리고 값이 0이 되면 더는 패킷 분석기가 실행되지 않는 것이며, 장치는 무차별 모드를 종료한다.   

### 네트워크 장치의 NAPI(New API)
NAPI는 오늘날 대부분의 네트워크 디바이스 드라이버에서 구현하고 있는 기능이다.   
오래된 네트워크 디바이스 드라이버는 인터럽트 구동 모드로 동작하는데 이는 패킷을 수신할 때마다 인터럽트가 발생하는 방식이고 이는 트래픽 부하가 심할 때 성능 측면에서 비효율적이다.   
그리하여 New API(NAPI)라고 하는 새로운 소프트웨어 기법이 개발됐고, 현재 대부분의 네트워크 장치 드라이버에서 지원한다.   
NAPI는 부하가 높은 상태에서 네트워크 디바이스 드라이버가 폴링 방식(polling mode)으로 동작한다.   
이는 각 수신 패킷이 인터럽트를 발생시키지 않음을 의미한다. 그 대신 패킷은 드라이버에 버퍼링되고, 커널이 이따금 패킷을 가져오기 위해 드라이버를 대상으로 폴링한다.   
NAPI를 이용하면 부하가 높은 상황에서 성능이 향상된다.   
지연 시간을 최대한 낮춰야 하면서 높은 CPU 사용률은 기꺼이 감수하는 소켓 어플리케이션을 위해 커널 3.11 이후로 소켓에 대한 바쁜 폴링(Busy Polling on Sockets) 기능을 추가했다.   

### 패킷의 수신과 송신
**네트워크 디바이스 드라이버의 주요 작업**은 다음과 같다.   
- 로컬 호스트를 목적지로 하는 패킷을 수신하고, L3(Network Layer)을 거쳐 L4(Transport Layer)로 전달.
- 로컬 호스트에서 생성되어 외부로 나가는 패킷을 전송하거나, 로컬 호스트에서 수신된 패킷을 포워딩.   


## 리눅스 커널 네트워킹 개발 모델
