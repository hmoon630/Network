# 챕터 8

## 네트워크 확인을 위한 명령어

### traceroute

traceroute는 출발지부터 목적지까지 네트워크 경로를 확인할 때 사용하는 명령어이다.

traceroute는 L3의 TTL 필드를 이용한다. TTL을 1부터 1씩 증가시키면서 목적지에 도달할 때까지 패킷을 반복적으로 전송하며 경로를 추적한다.  

### tcpdump

tcpdump는 네트워크 인터페이스를 오가는 패킷을 캡처하는 기능의 명령어이다. 같은 역할을 하는 애플리케이션으로 와이어샤크가 있다.
