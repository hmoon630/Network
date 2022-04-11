# 챕터 3

## 네트워크 통신 방식

### 유니 캐스트

- 1:1 통신
- 출발지와 목적지가 1:1로 통신한다.

### 브로드캐스트

- 1:N 통신
- 동일 네트워크에 존재하는 모든 호스트가 목적지
- 유니캐스트로 통신하기 전, 상대방의 정확한 위치를 파악하기 위해 사용
- **IPv6에는 존재하지 않음**

### 멀티캐스트

- 1:그룹(멀티캐스트 구독 호스트) 통신
- 하나의 출발지에서 다수의 특정 목적지로 전송
- 그룹 주소를 이용해 그룹에 속한 다수의 호스트로 패킷 전송

### 애니 캐스트

- 1:1 통신(동일 그룹 내 1개 호스트)
- 가장 가까운 호스트에서 응답
- IPv4에서는 일부 기능 구현, IPv6는 모두 구현 가능
- 가장 가까운 DNS 서버를 찾거나 게이트웨이를 찾을 때 사용

## BUM 트래픽

Broadcast, Unknown Unicast, Multicast를 지칭하는 BUM 트래픽은 서로 다르지만 네트워크 상에서 비슷하게 동작한다.

Unknown Unicast는 목적지 주소는 명시되어 있지만 스위치 입장에서 목적지 주소를 학습하지 못한 상황을 가리킨다. 이때 패킷을 모든 포트로 플러딩(전송)하게 된다.

BUM 트래픽이 중요한 이유는 유니캐스트 일지라도 실제 동작 방식은 브로드캐스트에 가깝기 때문이다. 패킷을 받는 모든 NIC에서 도착지 주소를 확인하고 자신의 것이 아닌 패킷을 버리지만, 네트워크 자원을 쓸데없이 사용하기 때문에 BUM 트래픽이 많아지면 성능 저하를 초래할 수 있다.

이더넷 환경은 ARP 브로드캐스트를 먼저 보내기에 BUM 트래픽이 많이 발생하지는 않는다.

## MAC 주소 체계

MAC 주소는 OUI(Organizational Unique Identifier)와 UAA(Universally Administered Address)로 이루어져 있고, 각각 24비트를 사용한다.

MAC 주소는 동일 네트워크 상에서 동일해서는 안되지만, 이 MAC 주소는 실수나 의도에 의해 유일하지 않을 수 있다.

MAC 주소는 ROM 형태로 BIA(Burned-In Address)이지만, 결국 메모리에 적재되어 구동되기 때문에 MAC 주소를 변경할 수도 있다.

## MAC 주소 동작

NIC는 MAC 주소를 가지고 있다. 패킷을 수신 했을 때 목적지 주소가 자신이 아니면 이를 폐기한다.

만약 분석 용도로 전체 패킷을 수집해야 할 경우 무차별 모드로 수신하여 처리할 수 있다. 대표적으로 와이어샤크(Wireshark)가 있다.

## Bogon IP

IANA가 여러가지 목적으로 공인 IP에 할당하지 않고 예약한 주소를 Bogon IP라고 한다.

[RFC 3330 Special-Use IPv4 Addresses](https://tools.ietf.org/html/rfc3330)

## TCP/UDP

TCP/IP 프로토콜 스택에서 4계층은 TCP와 UDP가 담당한다. L4는 프로세스를 잘 찾아가고 분할한 패킷을 잘 쪼개고 잘 조립하는 것이 목적이다.

## TCP

### 윈도 사이즈와 슬라이딩 윈도

TCP는 상대방이 잘 받았는지 확인하기 위해 ACK를 확인하고 다음 패킷을 전송한다. 하지만 송신자와 수신자가 멀어 RTT(Round Trip Time, 왕복 지연시간)가 늘어나면 응답을 기다리는 시간이 더욱 길어진다.

그래서 데이터를 보낼 때 패킷을 하나가 아닌 여러개를 보내고 응답을 하나만 받는다. 패킷 유실의 가능성이 있으므로 적절한 송신량을 결정해야 하는데, 한번에 받을 수 있는 데이터 크기를 **윈도 사이즈**라고 하고 네트워크 상황에 따라 윈도 사이즈를 조절하는 것을 **슬라이딩 윈도**라고 한다.

### 3-way handshake

|Act        |Client(A)  |Server(B)  |
|-----------|-----------|-----------|
|초기 상태  |           |LISTEN      |
|A>B SYN    |SYN-SENT   |LISTEN     |
|B>A SYN, ACK|SYN-SENT   |SYN-RECEIVED|
|A>B ACK    |ESTABLISHED|SYN-RECEIVED|
|최종 상태  |ESTABLISHED|ESTABLISHED|

### TCP 플래그

- SYN: 연결 시작 용도. 시작될 때 1로 표시하여 보낸다.
- ACK: ACK가 유효할 경우 1로 표시한다. 초기 SYN이 아닌 모든 패킷은 기존 메시지에 대한 응답이므로 1로 표시
- FIN: 연결 종료시 1로 표시, 데이터 전송을 마친 후 정상적으로 양방향 종료 시 사용
- RST: 연결 종료시 1로 표시, 강제 종료를 위해 일방적으로 끊을 때 사용
- URG: 긴급 데이터인 경우 1로 표시
- PSH: 서버 측에서 전송할 데이터가 없거나 데이터를 버퍼링 없이 응용 프로그램으로 즉시 전달을 지시할 때 사용

## UDP

UDP는 음성 데이터나 실시간 스트리밍과 같이 시간에 민감한 프로토콜이나 애플리케이션에 사용된다. 멀티캐스트처럼 단방향으로 다수와 통신하여 응답을 받기 어려운 환경에서도 주로 사용한다.

UDP는 데이터가 유실되더라도 계속 전송하여 동영상이나 음성이 멈추지 않도록 한다. 일부가 유실되더라도 사람에게는 이질감이 없을 수 있다.

## ARP

실제로 통신은 IP 주소 기반으로 일어나고 MAC 주소는 상대방의 주소를 자동으로 알아내 통신하게 된다. 이때 상대방의 MAC 주소를 알아내기 위해 사용되는 프로토콜이 **ARP(Address Resolution Protocol)** 이다.

호스트에서 통신을 처음 시도 할 때 상대방의 MAC 주소를 몰라 2계층을 캡슐화 할 수 없다. 이 때 ARP 브로드캐스트를 이용해 상대방의 MAC 주소를 질의하게 된다.

성능을 위해 통신할 때마다 브로드캐스트 하지 않고 ARP 테이블에 정보를 유지한다. 하지만 논리 주소(IP)는 언제든 바뀔 수 있으므로 일정 시간 후 삭제한다.

## GARP

GARP는 ARP와 반대로 Target IP 필드에 자신의 IP 주소를 채워 ARP 요청을 보낸다. 즉 ARP는 상대방의 MAC 주소를 알아내기 위해 사용하지만 GARP는 자신의 IP와 MAC 주소를 알릴 목적으로 사용된다.

GARP를 사용하는 이유는 다음과 같다.

### IP 주소 충돌 감지

IP 주소는 유일하게 할당되어야 하지만 이미 사용 중 일 수도 있다. IP 충돌을 방지하기 위해 이미 네트워크에서 사용되고 있는지 GARP를 통해 확인한다.

### 상대방의 ARP 테이블 갱신

가상 MAC 주소를 사용하지 않는 데이터베이스 HA(High Availability)를 위해 주로 사용된다.

액티브-스탠바이 형식으로 동작하는 상황에서 마스터 노드가 다운 될 경우, 스탠바이 장비가 마스터 노드로 승격된다. 그러나 네트워크 상에는 기존의 노드의 MAC 주소가 남아있어 응답 불가능한 장비로 패킷이 전달될 수 있는데, 이 ARP 테이블을 갱신하여 새롭게 승격된 마스터 노드의 MAC 주소로 패킷이 전달될 수 있도록 한다.

하지만 최근 네트워크 장비는 이런 형태의 HA를 사용하지 않을 수 있다. GARP를 이용해 패킷을 가로챌 수 있어 보안상 취약점이 발생할 수 있기 때문이다. 그래서 가상 MAC을 사용하는 HA 솔루션이 사용된다.

### 클러스터링, FHRP

클러스터링에서 가상 MAC 주소를 사용하는 경우, ARP 정보를 가상 MAC 주소로 학습하기 때문에 ARP 테이블을 갱신할 필요가 없어진다. 하지만 스위치에 있는 MAC 테이블은 마스터 노드가 변경될 때 가상의 MAC 주소를 잘 찾아가도록 갱신이 필요해진다. 이때 GARP를 전송하여 스위치가 MAC 테이블을 갱신하도록 한다.

## RARP

Reverse ARP는 자신의 MAC 주소는 알지만 IP를 할당받기 위해 요청할 때 사용한다.

하지만 제한된 기능으로 인해 BOOTP와 DHCP에 대체되어 사용되지 않는다.

## Default Gateway

Default Gateway는 로컬 네트워크에 목적지가 없을 경우 다른 네트워크에 대한 전달 호스트 역할을 하는 노드이다.
