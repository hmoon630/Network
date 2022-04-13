# 챕터 10

## 방화벽

리눅스는 호스트 방화벽 기능을 위해 iptables를 주로 사용하고, CentOS에서는 firewalld, Ubuntu에서는 UbuntuFW를 사용해 방화벽 서비스를 제공한다.

사실 iptables나 firewalld가 직접 방화벽 기능을 수행하는 것이 아닌 **netfilter**라는 리눅스 커널 모듈을 통해 실제 필터링이 이루어진다. 나머지는 netfilter에 대한 프론트엔드 역할을 하는 것이다.
