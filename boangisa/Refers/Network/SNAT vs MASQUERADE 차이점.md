
### SNAT (Source NAT)

###### 고정 IP로 SNAT 설정
```bash
# 고정 IP로 SNAT 설정
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j SNAT --to-source 203.0.113.10
```

**특징:**

- **고정 IP 지정**: `--to-source`로 명시적 IP 지정
- **Static 환경**: 공인 IP가 고정인 환경에 적합
- **성능**: 약간 더 빠름 (IP 조회 불필요)

### MASQUERADE

###### 인터페이스 기반 MASQUERADE 설정 
```bash
# 인터페이스 기반 MASQUERADE 설정
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE
```

**특징:**

- **동적 IP 대응**: 인터페이스의 현재 IP를 자동으로 사용
- **DHCP 환경**: IP가 변경되는 환경에 적합
- **자동 추적**: 인터페이스 IP 변경 시 자동으로 따라감

## 실제 사용 예시로 이해하기

### 시나리오 1: 회사 환경 (고정 IP)


###### 회사의 고정 공인 IP: 203.0.113.10
```bash
# 회사의 고정 공인 IP: 203.0.113.10
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j SNAT --to-source 203.0.113.10
```

- 항상 같은 IP로 변환
- IP 변경 걱정 없음

### 시나리오 2: 가정 환경 (DHCP)

###### ISP에서 DHCP로 IP 할당받음 (매번 다를 수 있음)
```bash
# ISP에서 DHCP로 IP 할당받음 (매번 다를 수 있음)
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o ppp0 -j MASQUERADE
```

- ppp0 인터페이스의 현재 IP 자동 사용
- IP가 바뀌어도 자동 대응

### 왜 브릿징에서 MASQUERADE+REDIRECT를 사용하나?

**브릿징 환경 특성:**

- 네트워크 토폴로지가 동적으로 변경됨
- 인터페이스 IP가 자주 바뀔 수 있음
- 유연한 대응이 필요

###### 브릿징 환경에서의 설정 예시
```bash 
# 브릿징 환경에서의 설정 예시
# 브릿징 환경에서의 설정 예시
iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
```

**MASQUERADE의 장점:**

- 인터페이스 IP 변경 시 자동 대응
- 설정이 더 간단함
- 브릿징 같은 동적 환경에 적합

**SNAT의 단점:**

- IP 변경 시 수동으로 규칙 수정 필요
- 동적 환경에서 관리가 복잡함

따라서 **고정 IP 환경에서는 SNAT**, **동적 IP 환경에서는 MASQUERADE**를 사용하는 것이 일반적입니다.  
