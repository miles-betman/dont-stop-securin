### [0. 개요](https://eveningdev.tistory.com/249#0.-%EA%B0%9C%EC%9A%94)

기업 환경에서는 보안 강화를 위해 VPN(Virtual Private Network)을 활용하는 경우가 많습니다. VPN을 사용하면 사내 네트워크에 원격으로 접속할 수 있으며, 인터넷을 통한 데이터 전송을 암호화하여 보안을 강화할 수 있습니다.

하지만 VPN의 트래픽 처리 방식에 따라 **Full Tunneling(풀 터널링)**과 **Split Tunneling(스플릿 터널링)** 두 가지 방식으로 나뉘며, 각각 장단점이 존재합니다.

이 글에서는 **Full Tunneling과 Split Tunneling의 차이점**을 설명하고, **기업 환경에서 어떤 방식을 선택해야 하는지** 고민해보겠습니다.  

### [1. 풀 터널링(Full Tunneling)과 스플릿 터널링(Split Tunneling) 비교](https://eveningdev.tistory.com/249#1.-%ED%92%80-%ED%84%B0%EB%84%90%EB%A7%81\(Full-Tunneling\)%EA%B3%BC-%EC%8A%A4%ED%94%8C%EB%A6%BF-%ED%84%B0%EB%84%90%EB%A7%81\(Split-Tunneling\)-%EB%B9%84%EA%B5%90)

|   |   |   |
|---|---|---|
|**구분**|**풀 터널링(Full Tunneling)**|**스플릿 터널링(Split Tunneling)**|
|**트래픽 경로**|모든 트래픽이 VPN을 거쳐 사내 네트워크로 전달|사내 네트워크와 인터넷 트래픽이 분리|
|**보안성**|높은 보안성 (인터넷 접속도 사내 정책 적용 가능)|보안 취약 가능성 (직접 인터넷 접속이 가능)|
|**속도**|사내 네트워크 부하 발생 가능|인터넷 트래픽이 분산되어 속도 향상 가능|
|**적용 환경**|기업 보안이 중요한 환경 (금융, 의료, 공공기관)|성능이 중요한 환경 (원격 근무, 개발 환경)|

### [2. 풀 터널링(Full Tunneling)](https://eveningdev.tistory.com/249#2.-%ED%92%80-%ED%84%B0%EB%84%90%EB%A7%81\(Full-Tunneling\))

![](https://blog.kakaocdn.net/dna/ccuIIV/btsMmddUGub/AAAAAAAAAAAAAAAAAAAAANadmP8BrUgq97XbqmEgEvqV8AH44GfPcCd5cLgNSQiL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1759244399&allow_ip=&allow_referer=&signature=Wp6c6UkVryx5pCMLtKdRhe2Jr7E%3D)

사용자의 모든 트래픽이 VPN을 통해 기업 내부 네트워크로 전달됩니다. 즉, 인터넷 접속을 포함한 모든 요청이 VPN 서버를 거쳐 전달되므로 강력한 보안 통제가 가능합니다.

이 경우 사내 시스템에 접근 시 기업의 보안 정책이 적용된 트래픽만 허용되게 할 수 있고 모든 트래픽에 대해 감시하고 로그를 남길 수 있습니다. 또한 VPN을 거쳐야 하므로 보안성이 높다는 장점이 있습니다.

하지만 모든 트래픽이 VPN 서버를 거치기 때문에 속도가 느려질 가능성이 높고 많은 사용자로 인해 과부하가 생길 수 있습니다.

### [3. 스플릿 터널링(Split Tunneling)](https://eveningdev.tistory.com/249#3.-%EC%8A%A4%ED%94%8C%EB%A6%BF-%ED%84%B0%EB%84%90%EB%A7%81\(Split-Tunneling\))

![](https://blog.kakaocdn.net/dna/c1xkdV/btsMlMOuweX/AAAAAAAAAAAAAAAAAAAAAOdZjoycvpDDcJK7sp2X2Va-tLlOCGPsBFKDcKmOVt02/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1759244399&allow_ip=&allow_referer=&signature=Ae07TA8g%2FwNbHR5xu4E3mcmGA30%3D)

기업 내부 시스템 트래픽만 VPN을 사용하고, 일반 인터넷 트래픽은 직접 인터넷으로 전달됩니다.

(기업 내부 자원(사내 서버, DB, 업무 시스템)에 접속할 때만 VPN을 사용하고, 일반 웹 서핑, 이메일 등은 VPN을 거치지 않고 직접 인터넷에 연결합니다.)

일반 인터넷 트래픽은 VPN을 거치지 않아 속도가 빠르고, VPN 서버에 대한 부하가 비교적 적습니다. 하지만 보안, 로그, 모니터링, 접근 통제 등에서 어려움이 생깁니다.

### [4. 스플릿 터널링 방식으로 인해 발생하는 문제](https://eveningdev.tistory.com/249#4.-%EC%8A%A4%ED%94%8C%EB%A6%BF-%ED%84%B0%EB%84%90%EB%A7%81-%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C-%EC%9D%B8%ED%95%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94-%EB%AC%B8%EC%A0%9C)

![](https://blog.kakaocdn.net/dna/c1xkdV/btsMlMOuweX/AAAAAAAAAAAAAAAAAAAAAOdZjoycvpDDcJK7sp2X2Va-tLlOCGPsBFKDcKmOVt02/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1759244399&allow_ip=&allow_referer=&signature=Ae07TA8g%2FwNbHR5xu4E3mcmGA30%3D)

사내 특정 시스템의 경우 스플릿 터널링 방식을 채택하고 있습니다. 이 경우 VPN 트래픽은 대부분 데이터센터를 통해 들어가지만 인터넷 트래픽의 경우 사용자 개인의 공인 IP로 해당 시스템에 접근하게 되는데 Global Edge의 화이트리스트로 관리하고 있는 클라우드 서비스에 접근할 때 문제가 발생합니다.

**1. 화이트리스트(Whitelist) 문제**

네이버 클라우드의 Global Edge는 화이트리스트로 최대 50개까지만 관리할 수 있습니다. 이 경우 시스템의 담당자 및 접근 가능한 사용자를 최대 50개까지만 등록하여 사용할 수 있는데 시스템이 커지고 확장되면서 화이트리스트로 인한 문제가 발생할 가능성이 매우 큽니다.

**2. IP 기반 접근 제어 문제 (유동 IP)**

사용자 개인은 유동 IP를 사용하기 때문에 해당 시스템에 접근하기 위해서는 해당 IP를 허용해주어야 하는데 IP가 매번 바뀔 우려가 있고 변동이 있을 때마다 Edge를 재배포하는 방식은 각 사용자의 공인 IP가 달라지면서 접근이 차단될 가능성이 있음

**3. 보안 및 모니터링 문제**  
보안 정책 우회 위험사용자의 트래픽이 VPN을 거치지 않고 인터넷을 통해 직접 내부 시스템에 접근하므로, 기업의 보안 모니터링 및 감사를 우회할 가능성이 있습니다.

이 경우 스플릿 터널링(Split Tunneling) 방식을 사용하더라도 VPN 내부에서 NAT 기능을 활성화하여 고정 IP로 접근하도록 설정이 필요합니다.

### [5. 해결 방안](https://eveningdev.tistory.com/249#5.-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EC%95%88)

![](https://blog.kakaocdn.net/dna/ccuIIV/btsMmddUGub/AAAAAAAAAAAAAAAAAAAAANadmP8BrUgq97XbqmEgEvqV8AH44GfPcCd5cLgNSQiL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1759244399&allow_ip=&allow_referer=&signature=Wp6c6UkVryx5pCMLtKdRhe2Jr7E%3D)

스플릿 터널링(Split Tunneling) 상태에서 특정 트래픽만 VPN을 통해 전달하려면 VPN NAT 설정이 필요합니다.

이 방식은 **특정 대상 IP 대역 (사내 시스템 등)으로 향하는 트래픽을 강제로 VPN을 경유**하도록 만드는 방법입니다.

**Split 터널링에 NAT를 적용하는 이유**

스플릿 터널링의 보안 취약점을 보완하면서 VPN을 효율적으로 활용하기 위함

1. 인터넷 트래픽은 로컬 네트워크로 나가고, 기업 내부 시스템으로 가는 VPN을 사용

2. 일부 기업 시스템에서는 **VPN을 통해 특정 IP 주소에서만 접근을 허용**하는 경우

3. 위 케이스처럼 **클라이언트의 IP를 고정 IP로 변환**시킬 필요가 있는 경우

