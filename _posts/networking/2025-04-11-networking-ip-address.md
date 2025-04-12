---
title: "IP Address 이해하기"

categories:
  - networking
tags: [networking, cs, tcp/ip]

date: 2025-04-11
last_modified_at: 2025-04-11
---

### IP Address의 정의와 구성
#### IP Address의 정의
- Internet Protocol Address, 인터넷 상의 장치를 식별하기 위한 고유한 숫자 주소
- Logical Address

#### IP Address가 필요한 이유
- Network 내에서 각 장치를 구분
- Data를 정확한 목적지로 전달

#### IP Address의 구성
##### IPv4
- 내부적으로는 32bit 이진수
- **8bit 씩 나눠 10진수로 표현 (4개의 옥텟)**
-  ```.```으로 구분 (ex. ```192.168.0.1```)
    - **Network Address** + **Host Address** 로 구분
        - 구분 기준: **Subnet Mask**
##### IPv6
- 128 bit 이진수
- 8개의 헥스텟(각 16진수)으로 표현
- ```:``` 으로 구분 (ex. ```2001:0db8:0000:0000:0000:8a2e:0370:7334```)
- IPv4의 주소 공간 고갈 문제를 해결하기 위해 도입
- Broadcast 제거. Multicast 기반
- 보안(IPSec), 자동 주소 설정(SLAAC), 모바일 환경 대응 등 기능 개선


### IP 주소 체계
- IPv4 주소 체계는 Class 기반 방식에서 CIDR 기반 방식으로 발전

#### Classful Addressing (IP Class 기반)
- IP address를 **A / B / C / ... Class**로 고정된 범위에 따라 구분
- 고정된 크기의 Subnet 제공
- 단순하고 빠른 분류 방식이지만, 너무 고정적이고 주소 낭비가 심함

#### CIDR (Classless Inter-Domain Routing)
- 클래스 대신 **가변 길이(prefix length)** 로 네트워크 범위를 표현 (예: `/24`, `/18`)
    - **주소 공간을 더 효율적으로 활용** 가능
    - [Routing Table]도 간소화 가능 (route aggregation)

##### CIDR 표기법 예시
- `192.168.1.0/24` → 앞 24비트가 네트워크, 나머지는 호스트
- `/x` 가 클 수록 네트워크 영역이 더 큼
    - `/20`이면 더 큰 네트워크 대역, `/30`이면 더 좁은 대역

#### Subnet Mask 개념 
- **CIDR 은 Subnet Mask를 bit 수로 표현한 방식**
- Subnet Mask는 **IP Address를 Network Address 와 Host Address로 구분하는 기준**
- 예를 들어 `/24` = `255.255.255.0`

### IP Address의 종류

#### Public IP
- internet 상에서 고유한 주소로 사용
- ISP로부터 할당 받음
- internet을 통해 직접적으로 통신 가능한 주소

#### Private IP
- 내부 Network에서만 존재
- IP Address 충돌 방지, 내부 장치 구분
- 외부 internet과 통신하기 위해서는  **Router나 NAT(Network Address Translation)**를 통해 외부와 통신