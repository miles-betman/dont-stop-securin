
### **iptables 개요** 
iptables는 [netfilter](https://www.netfilter.org/)라는 프로젝트에서 프로그램으로, firewalling, NAT, and packet mangling for linux 즉 리눅스에서 패킷을 filtering, 주소 변환, 그리고 조작할 수 있는 종합적인 네트워크 프레임워크다. 
iptables 가 이 netfilter의 사용자 공간 인터페이스 역할을 한다. (iptables의 후속작 nftables)


### **iptables 개념 : 테이블과 체인**
iptables에는 테이블(Table)과 체인(Chain) 이라는 큰 분류가 있으며, 체인이라는 규칙을 연결한 테이블 구조로 되어 있다. 

테이블은 iptables의 규칙을 정의할 수 있는 가장 광범위한 범주로, 아래의 4가지 종류가 있다. 

- **filter** : iptables의 기본 테이블로 패킷 필터링 담당
- **nat** : Network Address Translation, IP 주소 변환 
- **mangle** : 패킷 데이터를 변경하는 특수 규칙을 적용하는 테이블, 성능향상을 위한 TOS(Type of Service) 설정
- **raw** : netfilter 의 연결 추적 하위 시스템과 독립적으로 동작해야 하는 규칙을 설정하는 테이블 
- **security** : 리눅스 보안 모듈인 Selinux 에 의해 사용되는 MAC(Mandatory Access Control) 네트워크 관련 규칙 적용 

이 중에서 **filter**는 단어가 의미하는 뜻 그대로 패킷을 걸러내는 용도이며, **Inbound**(호스트로 들어오는 패킷) / **Outbound**(호스트에서 밖으로 나가는 패킷) 및 **Forward**(호스트를 거쳐가는 패킷) 총 3가지 종류의 패킷을 어떻게 처리할 것인지에 대한 규칙을 정의할 수 있다. 
- INPUT : 호스트 컴퓨터의 로컬 프로세스를 향해 들어온 모든 패킷이 방문하는 Chain 이다. 
- FORWARD : 타겟이 호스트가 아닌, 즉 호스트 컴퓨터를 경유하는 패킷이 방문하는 Chain 이다. 이 Chain을 방문하는 Packet은 Local Process를 거치지 않는다. 
- OUTPUT : 호스트 컴퓨터에서 자체적으로 생성된 패킷이 방문하는 Chain 이다. 

![[flowofpacketinkernel.png]]


**nat**는 패킷의 속성인 출발 주소(source), 도착 주소(destination) 등을 변경할 수 있도록 각종 규칙을 정의하는 테이블이다. 라우터(공유기) 등에서 사용하는 주소 변환(nat)와 동일하지만, iptables의 nat는 이에 그치지 않고 다양한 상황에 범용적으로 적용해 사용할 수 있다. nat 테이블의 자세한 Use Case는 뒤에서 설명할 것이다.

iptables에는 테이블의 하위 규칙 속성으로서 체인(Chain) 이라는 것이 존재한다. nat에는 아래의 체인을 사용해 규칙을 정의할 수 있다. 

- **PREROUTING** (DNAT) : 패킷을 INPUT rule 로 보내기 전 ip와 port 를 변경하는 역할을 한다. 
	- 주로 패킷의 도착지(deatination) 주소를 변경한다. D(estination)NAT 
- **INPUT** : 
- **OUTPUT** : 호스트에서 밖으로 흐르는 패킷의 도착지(destination) 주소를 변경한다. (INPUT도 있다) 
- **POSTROUTING** (SNAT 또는 masquerade) : 패킷이 OUTPUT rule 에서 나온 이후 ip 와 port 만을 변경하는 역할을 한다. 
	- 주로 패킷의 출발지(source) 주소를 변경한다. S(ource)NAT 


![[PacketflowinNetfilterandGeneralNetworking.png]]

iptables 사용법
```
$ iptables [-t table][action][chain][match][-j target] 
```
- iptables 를 이용하여 정책을 설정할 때 가장 중요한 것은 실질적 룰(Rule)에 해당하는 매치(Match)와 타겟(Target)이다. 
- 타겟은 iptables 에서 패킷이 규칙과 일치할 때 취하는 동작이고, 매치는 iptables 가 규칙 타겟에 의해 명시되는 동작에 따라 패킷을 처리하기 위해 만족해야 하는 조건들이다. 

간단하게 그림으로 설명하면 아래와 같다.2
  
![](https://blogfiles.pstatic.net/MjAxODA3MDFfNzMg/MDAxNTMwMzc1NzQzMjcy.PuD5YWFToGz6XgrUIAM5xm0z7qDqOhC0yyT9T1myfWQg.U_W_GX_jetIdRY2ALqUP52wI3WiglxM0rVAETNUmvr4g.JPEG.alice_k106/%EA%B7%B8%EB%A6%BC1.jpg?type=w2) 

사용자는 1.2.3.4:85로 서비스 요청 패킷을 전송한다. 그런데 라우터의 역할을 하는 서버 A(1.2.3.4) 에는 1.2.3.4:85로 들어온 패킷의 목적지를 5.6.7.8:80 으로 변환하라는  PREROUTING 규칙이 있기 때문에 패킷의 실제 도착지는 서버 B(5.6.7.8)의 웹서비스인 80 포트가 된다. 

![](https://blogfiles.pstatic.net/MjAxODA3MDFfMjk1/MDAxNTMwMzc1NzQzNzI3.89TV_YRCkZXRkAg5ubu78XsDNcNJMI7zCOBrtRadPK8g.HMKJtp06_UHecvMJTCbbw1hZwH-ciwjR1zbPqgxMJNYg.JPEG.alice_k106/%EA%B7%B8%EB%A6%BC2.jpg?type=w2)

서비스 요청을 했으니 당연히 이에 대한 응답이 전송되어야 한다. 그런데 서버 B가 전송하는 응답 패킷의 출발지(Source)는 5.6.7.8이고, 외부 사용자가 ACK를 정상적으로 받기 위해서는 Source가 다시 1.2.3.4으로 변경되어야 한다. POSTROUTING인 SNAT 룰은 응답 패킷의 Source를 변경해 사용자에게 전달할 수 있다. 3

INPUT / OUTPUT 체인도 존재하지만, 나는 OUTPUT 체인만 사용해 보았다. OUTPUT 체인은 호스트가 주체가 되어 밖으로 나가는 패킷의 목적지를 다르게 할 때 사용된다. 예를 들어, curl 5.6.7.8로 요청을 해도, 실제로는 9.10.11.12로 트래픽이 흐르도록 설정할 수 있다. 이에 대한 자세한 내용은 아래의 내용 중 4.2절을 참고.
  

**3. iptables의 nat 테이블 사용하기**

**3.1 기본 설정**

192.168.1.X (enp0s8) 은 내부 IP로서 VM 간 통신에 사용되는 호스트 전용 브릿지이다.

node01에 DNAT, SNAT 설정을 할 것이며 node02는 node01의 iptables 기능을 사용하는 일종의 클라이언트로써 사용한다.

|                              |                                                                                                                              |                                       |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| 1<br><br>2<br><br>3<br><br>4 | VirtualBox, CentOS 7.2<br><br>node01 : 192.168.1.152 (호스트 전용 브릿지, enp0s8)<br><br>node02 : 192.168.1.153 (호스트 전용 브릿지, enp0s8) | [cs](http://colorscripter.com/info#e) |

실제 환경에서는 그래선 안되겠지만, 원활한 iptables 기능 테스트를 위해 firewalld와 ufw는 비활성화한 뒤 시작한다.

|            |                                                                                                     |                                       |
| ---------- | --------------------------------------------------------------------------------------------------- | ------------------------------------- |
| 1<br><br>2 | [root@node01 ~] service firewalld stop<br><br>Redirecting to /bin/systemctl stop  firewalld.service | [cs](http://colorscripter.com/info#e) |

레드햇 계열의 OS는 기본적으로 패킷 포워딩 기능을 보안상의 이유로 막아둔다고 한다. 아래 명령어로 이를 허용할 수 있다.

|     |                                                 |                                       |
| --- | ----------------------------------------------- | ------------------------------------- |
| 1   | [root@node01 ~] sysctl -w net.ipv4.ip_forward=1 | [cs](http://colorscripter.com/info#e) |

아래의 명령어를 입력하면 현재 iptables의 내용을 확인할 수 있다. 기본적으로 -L 인자로 규칙을 나열할 수 있으며, -nL 인자를 통해 값을 숫자로 출력할 수 있다. -t nat 인자 선택적으로 사용할 수 있는데, 이는 nat 테이블의 내용만을 출력하도록 설정한다.

|                                                                                                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |                                       |
| ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| 1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13 | [root@node01 ~] iptables -nL -t nat<br><br>Chain PREROUTING (policy ACCEPT)<br><br>target     prot opt source               destination         <br><br>Chain INPUT (policy ACCEPT)<br><br>target     prot opt source               destination         <br><br>Chain OUTPUT (policy ACCEPT)<br><br>target     prot opt source               destination         <br><br>Chain POSTROUTING (policy ACCEPT)<br><br>target     prot opt source               destination | [cs](http://colorscripter.com/info#e) |

  

firewalld를 비활성화했기 때문에 어떠한 규칙도 존재하지 않는다. 

  

  

  

**3.2 규칙 사용하기**

  

간단하지만 iptables의 DNAT, SNAT 기능을 모두 사용해볼 수 있는 예제를 설명해보려 한다.4

  

![](https://blogfiles.pstatic.net/MjAxODA2MjRfMTU1/MDAxNTI5ODQ0NDE3NzUw.1e-yupq5kcYxQc8AIc-zcBVtKiEiMbyMA8S2QDLpnC0g.5sU9vFoPWF0hrOyiiAx1crg13sLqYZc5hifOOwRQE24g.PNG.alice_k106/%EA%B7%B8%EB%A6%BC4.png?type=w2) 

  

node01은 iptables 규칙을 설정해 놓은 VM이고, node02는 nginx 서버를 실행하고 있다. 윈도우 상의 git bash 쉘에서 node01의 IP와 85 포트로 요청을 보내면, DNAT & SNAT 규칙에 의해 이 요청은 node02의 nginx 서버로 우회된다. nginx 서버를 설치하는 방법을 모른다면 아래를 참고.

  

[> nginx 설치](https://blog.naver.com/PostView.naver?blogId=alice_k106&logNo=221305928714&redirect=Dlog&widgetTypeCall=true&topReferer=https%3A%2F%2Fwww.google.com%2F&trackingCode=external&directAccess=false#)

  

가장 먼저 DNAT 규칙을 생성해 보자. DNAT 규칙은 PREROUTING 체인에 의해 설정될 수 있다.

  

|   |   |   |
|---|---|---|
|1|[root@node01 ~] iptables -A PREROUTING -t nat -j DNAT -p tcp --dport 85 --to-destination 192.168.1.153:80|[cs](http://colorscripter.com/info#e)|

  

위 명령어에 포함된 인자의 뜻은 아래와 같다.

  

- **-A PREROUTING** : PREROUTING 체인에 규칙을 추가한다(Append). 자매품으로 -I 옵션도 있는데, -I 옵션은 규칙의 번호를 설정해 넣을 수 있다(Insert). iptables 규칙은 위에서 차례대로, 즉 1번부터 차례대로 우선적으로 적용되기 때문에 1번 규칙에 의해 걸러진 경우 2번 규칙은 무시될 수 있다.
- **-t nat** : nat 테이블에 규칙을 추가한다.
- **-j DNAT** : 사용할 기능으로, DNAT 또는 SNAT, MASQUERADE를 명시할 수 있다.
- **-p tcp** : tcp 프로토콜을 사용한다.
- **--dport** : 들어오는 패킷의 목적지 포트를 명시한다. 여기서는 192.168.1.152:85 (node01의 85 포트)로 요청을 보낸다.
- **--to-destination** : 최종적으로 DNAT에 의해 설정될 도착지를 설정한다.

  

위 명령어를 입력한 뒤, 다시 규칙 목록을 확인해 보자. 이번에는 --line-numbers 인자를 추가해 규칙의 번호도 확인한다.

  

|   |   |   |
|---|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5|[root@node01 ~] iptables -nL PREROUTING -t nat <br><br>Chain PREROUTING (policy ACCEPT)<br><br>target     prot opt source               destination         <br><br>DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:85 to:192.168.1.153:80|[cs](http://colorscripter.com/info#e)|

  

잘 추가되었음을 확인했다. 

  

이번에는 목적지(nginx) 에 도착한 뒤, ACK를 잘 돌려받기 위해  SNAT 규칙을 추가한다. 이 때, -j 인자에 MASQUERADE를 사용할 수도 있고, SNAT를 직접적으로 명시할 수도 있다. 아래는 MASQUERADE와 SNAT를 사용하는 두 예시를 보여준다. 

  

|   |   |   |
|---|---|---|
|1|[root@node01 ~] iptables -A POSTROUTING -t nat -j MASQUERADE -o enp0s8|[cs](http://colorscripter.com/info#e)|

**​**

|   |   |   |
|---|---|---|
|1|[root@node01 ~] iptables -A POSTROUTING -t nat -j SNAT -o enp0s8 -s 192.168.1.0/24 --to-source 192.168.1.152|[cs](http://colorscripter.com/info#e)|

  

SNAT와 MASQUERADE는 동일한 기능을 수행하지만, MASQUERADE는 사용할 NIC만을 명시할 뿐 IP를 명시하지 않아도 된다는 장점이 있다. dhcp 등의 경우를 고려하면 참으로 편리하지 않을 수 없다.5

  

위 명령어에 포함된 인자의 뜻은 아래와 같다.

  

- **-o enp0s8** : enp0s8로 나가는(out) 트래픽에 대해서 적용한다.
- **-s 192.168.1.0/24** : 해당 서브넷에 해당하는 IP들이 source인 패킷에 대해 SNAT을 적용한다.67 이 예시에서는 1:1 통신만을 가정하지만, 공유기에 Public IP가 설정되어 있고 그 아래에 NAT IP들이 192 대역을 가지는 경우를 생각해 보면, 이 옵션을 적용할 수 있는 Use case가 명확해질 것이다.
- **--to-source** : 변경되어 최종적으로 패킷에 설정될 Source 주소를 입력한다. 위 예시는 SNAT이 적용되는 패킷의 Source 주소가 192.168.1.152로 바뀌게 된다.

  

> 추가적으로 설정해줘야 하는 규칙 중, filter 테이블의 FORWARD라는 체인이 있다. FORWARD 체인은 해당 호스트가 목적지가 아닌 패킷을 ACCEPT할지, DROP할지를 결정하는데, 위의 DNAT와 SNAT도 FORWARD 체인의 대상이 된다. 별다른 설정을 하지 않았다면 FORWARD의 기본 정책(Policy)이 ACCEPT로 설정되어 있기 때문에 DNAT와 SNAT가 정상적으로 동작할 수 있다.8
> 
>   
> 
> |   |   |   |
> |---|---|---|
> |1<br><br>2<br><br>3|[root@node01 ~] iptables -nL FORWARD<br><br>Chain FORWARD (policy **ACCEPT**)<br><br>target     prot opt source               destination|[cs](http://colorscripter.com/info#e)|
> 
>   
> 
> 그러나 별도의 보안을 위해 FORWARD의 기본 정책을 DROP으로 설정하는 경우, 아래와 같이 규칙을 수정할 수 있다.
> 
>   
> 
> |   |   |   |
> |---|---|---|
> |1<br><br>2|[root@node01 ~] iptables -A FORWARD -i enp0s8 -p tcp --dport 80 -d 192.168.1.153 -j ACCEPT<br><br>[root@node01 ~] iptables -A FORWARD -m state --state ESTABLISHED -j ACCEPT910|[cs](http://colorscripter.com/info#e)|
> 
>   
> 
>  FORWARD 체인의 목록은 아래와 같게 된다. -v 인자로 verbose를 활성화하였다.
> 
>   
> 
> |   |   |   |
> |---|---|---|
> |1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6|[root@node01 ~] iptables -nL FORWARD -v<br><br>Chain FORWARD (policy **DROP** 0 packets, 0 bytes)<br><br> pkts bytes target     prot opt in     out     source               destination         <br><br>    5   292 ACCEPT     tcp  --  enp0s8 *       0.0.0.0/0            192.168.1.153        tcp dpt:80<br><br>    4  1022 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            state ESTABLISHED|[cs](http://colorscripter.com/info#e)|

이상으로 필요한 설정은 끝이 났다. 윈도우 호스트에서 node01로 요청을 보내면 nginx 응답을 확인할 수 있다.

  

![](https://blogfiles.pstatic.net/MjAxODA2MjRfMjYw/MDAxNTI5ODQ3NTQ5MTA4.FLa6YChLijxRDg2pqBLy4Tnilr5JHv-ZmAKUFjwg1G8g.rUBgbD1vH9NsyvxKNvA_MMFETc4dGfCGvpr7i6z-ny8g.PNG.alice_k106/%EC%BA%A1%EC%B2%98.PNG?type=w2)

  

특정 규칙을 체인에서 삭제하고 싶다면, -D 인자를 사용할 수 있다. 아래의 예시는 --line-numbers 인자를 추가해 출력된 규칙의 번호를 이용해 특정 번호의 규칙을 삭제하는 예시를 나타낸다. 

  

|   |   |   |
|---|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6|[root@node01 ~] iptables -nL PREROUTING -t nat --line-numbers<br><br>Chain PREROUTING (policy ACCEPT)<br><br>**num**  target     prot opt source               destination         <br><br>**1**    DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:85 to:192.168.1.153:80<br><br>[root@node01 ~] iptables **-D PREROUTING 1** -t nat<br><br>[Colored by Color Scripter](http://colorscripter.com/info#e)|[cs](http://colorscripter.com/info#e)|

  

  

**4. Use Case**

  

**4.1 NAT Router 구성하기**

  

해보진 않았지만, Masquerade 기능을 활용해 NAT Router를 구성하는 것이 가능하다. 일상에서 접할 수 있는 공유기의 방식을 떠올리면 쉽게 이해가 될 지도 모른다. 

  

예를 들어 외부/내부 네트워크에 동시에 연결되어 있는 서버가 1대 존재하고, 내부 네트워크에만 연결된 서버가 여러 대 존재할 때 iptables의 기능을 활용하면 외부 네트워크에 연결되지 않은 서버들도 외부와 통신할 수 있다. 외부/내부 네트워크에 동시에 연결된 서버가 일종의 네트워크 게이트웨이로써 사용되는 셈이다. 

  

지금 당장은 아니더라도, 나중에 해볼 기회가 있다면 구축해 보고 싶다. 아래는 레퍼런스 링크.

  

[> 레퍼런스 링크](https://blog.naver.com/PostView.naver?blogId=alice_k106&logNo=221305928714&redirect=Dlog&widgetTypeCall=true&topReferer=https%3A%2F%2Fwww.google.com%2F&trackingCode=external&directAccess=false#)

  

  

**4.2 포트를 변경해야 하는 도커 컨테이너 (PREROUTING, OUTPUT 체인)**

  

도커 컨테이너는 런타임에서 포트바인딩 변경을 지원하지 않는다. 추가적인 포트를 열어줘야 한다거나 바인딩된 포트를 변경해야 할 때는 컨테이너를 삭제하고 다시 생성해야 하는데, 컨테이너가 Stateful 하다면 울며 겨자먹기로 이미지를 커밋하고 그 이미지로 컨테이너를 생성해야 한다. 예를 들어 아래와 같은 상황을 가정해보자.

  

|   |   |   |
|---|---|---|
|1<br><br>2|[root@node02 ~] docker run -d -p 81:80 --name mistake nginx<br><br>ca7cd6120d67c48dadb611733d8b90a993c4a314acf2e17311f171f7df4fe6da|[cs](http://colorscripter.com/info#e)|

  

분명히 nginx 컨테이너인데, 실수로 호스트의 81 포트와 매핑된 상황이다. 위 예시는 nginx같은 컨테이너니 맘대로 삭제해도 되겠지만, 컨테이너 내부에서 무엇인가 테스트하고 있는 상황이라면 컨테이너 삭제 & 커밋하는 것은 일만 크게 키우는 셈이다. 이럴 때, iptables를 이용하면 간단히 해결할 수 있다. 11

  

|   |   |   |
|---|---|---|
|1|[root@node02 ~] iptables -A PREROUTING -t nat -j DNAT -p tcp -d 192.168.1.153 --dport 80 --to-destination 192.168.1.153:81|[cs](http://colorscripter.com/info#e)|

  

단, 이 경우에는 PREROUTING (외부로부터 들어오는 트래픽)에 대해서만 라우팅되므로, 로컬 환경에서 80 포트로 요청을 보낸다고 해서 nginx로 라우팅되지는 않는다. 이를 위해 OUTPUT 체인을 사용할 수 있는데, OUTPUT 체인은 호스트에서 발생해 외부로 나가는 패킷의 목적지를 변경한다.12

  

|   |   |   |
|---|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5|[root@node02 ~] iptables -A OUTPUT -t nat -j DNAT -p tcp -d 192.168.1.153 --dport 80 --to-destination 192.168.1.153:81<br><br>[root@node02 ~] curl 192.168.1.153:80<br><br><!DOCTYPE html><br><br>... 생략<br><br>[Colored by Color Scripter](http://colorscripter.com/info#e)|[cs](http://colorscripter.com/info#e)|

  

  

**4.3 REDIRECT 체인**

  

위에서는 설명하지 않았지만, DNAT나 SNAT처럼 -j 인자에 입력할 수 있는 값으로 REDIRECT라는 Target이 존재한다. REDIRECT는 호스트가 dhcp이고, 라우팅되는 최종 목적지가 로컬호스트일때 유용하게 사용할 수 있다. 예를 들어보자.

  

나는 웹 서버를 Apache Tomcat으로 구동하고 있다. 물론 포트는 8080을 Listen하고 있는데, 사용자가 80 포트로 접근해도 8080으로 접근하는 것과 동일하게 처리하고 싶다면 REDIRECT를 사용해 해결할 수 있다. 

  

|   |   |   |
|---|---|---|
|1|[root@node02 ~] iptables -A PREROUTING -t nat -i enp0s3 -p tcp --dport 80 -j REDIRECT --to-port 8080|[cs](http://colorscripter.com/info#e)|

  

물론 OUTPUT이나 PREROUTING 체인을 사용할 수도 있겠지만, 두 체인 모두 static한 IP를 입력해야 한다는 단점이 있다. IP가 유동적으로 변경되는 dhcp 환경에서는 이를 적용하기에 까다로울 뿐만 아니라, Ansible 등으로 여러 대의 서버에 동일한 iptables 설정을 해줘야 할 때에도 각 서버마다 다른 규칙을 적용해야 한다는 불편함이 있다.1314

  

  

  

**5. 마지막으로..**

  

iptables 규칙은 리붓하면 초기화되기 때문에, crontab이나 bashrc 등으로 iptables 규칙을 정의해 두는 것이 권장된다. 참고로 upstart 기반의 리눅스 OS에서는 /eyc/sysconfig/iptables로 선언적인 규칙 파일을 정의할 수 있는 듯 하다. 이 때는, iptables-save 명령어를 사용해 현재 설정된 iptables 규칙들을 파일로서 추출해낼 수 있다.15

  

추가 참고자료1617

  

  

4시간동안 작성했다. 배고프다...

끝.

  

1. http://yahon.tistory.com/173
2. https://www.quora.com/What-is-difference-between-SNAT-and-DNAT
3. 이해를 돕기 위해 위처럼 src/dst를 나타내었지만, 실제로도 저렇게 되는지는 확실하지 않다. 아마 src/dst가 둘 다 바뀔 것 같다.
4. 여기서는 쉬운 예시를 들기 위해 같은 네트워크 내 (192 대역) 패킷 DNAT를 설명하지만, 다른 NIC 간의 패킷 전달도 당연히 가능하다. 단, 이 경우 SNAT를 2개의 NIC에 각각 설정해줘야 한다. 참고 링크 : https://ubuntuforums.org/showthread.php?t=2259305
5. https://kldp.org/node/110527
6. http://kreisel.fam.cx/webmaster/clog/2011-02-11-1.html
7. http://tamenut.tistory.com/entry/iptables-%EC%97%90%EC%84%9C-NAT-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
8. http://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-sg-ko-4/s1-firewall-ipt-fwd.html
9. https://unix.stackexchange.com/questions/65964/help-to-understand-iptables-forward-chain-with-dnat
10. 상태 체크 설명 : http://sata.kr/entry/IPTables-4-m-%EC%98%B5%EC%85%98%EC%9D%98-%ED%99%9C%EC%9A%A9-%EC%98%88%EC%8B%9C
11. 이 상황에서는 masquerade 규칙을 따로 추가해주지 않아도 잘 동작하는데, 이는 도커 엔진이 POSTROUTING 체인에 Docker 컨테이너를 위한 SNAT를 추가하기 때문이다. 물론 firewalld하고 충돌하면 삭제되기 때문에 방화벽과 같이 사용한다면 주의를 요한다.
12. PREROUTING과 다르게 신기하게도, 서로 다른 네트워크 대역이어도 잘 동작한다. 예를 들어 iptables -A OUTPUT -t nat -j DNAT -p tcp -d 1.2.3.4 --dport 80 --to-destination 192.168.1.153:81 처럼 설정을 해도 1.2.3.4는 제대로 192.168.1.153으로 라우팅된다.
13. https://serverfault.com/questions/179200/difference-beetween-dnat-and-redirect-in-iptables
14. https://blog.outsider.ne.kr/580
15. 당연하지만, 이 명령어는 systemd 기반의 OS에서도 잘 동작한다.
16. http://webterror.net/?p=1622
17. https://www.joinc.co.kr/w/Site/System_management/NAT