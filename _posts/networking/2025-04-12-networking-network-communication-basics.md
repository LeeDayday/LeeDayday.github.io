---
title: "네트워크 통신 개념 이해하기"

categories:
  - networking
tags: [networking, cs, tcp/ip]

date: 2025-04-12
last_modified_at: 2025-04-12
---

### 네트워크 통신
- 요즘 우리가 사용하는 거의 모든 네트워크 통신은 **TCP/IP 기반**
- 따라서, TCP/IP Protocol을 올바르게 구성하는 것을 이해하는 것이 핵심

### 네트워크 통신의 조건
- 네트워크 통신: 두 장치 간 data를 주고 받는 과정
    - data를 어디로 보낼지를 알아야 함
    - 상대와 내가 동일한 네트워크에 있는지, 다른 네트워크에 있는지를 알아야 함
    - 다른 네트워크에 있는 경우, 어디로 대신해서 보낼지를 알아야 함
1. 상대방의 IP Address
2. Subnet Mask
3. Gateway IP Address

#### IP Address의 특징
- IP Address는 `Network Address` 와 `Host Address`로 구성
- 동일한 Network에서의 통신
    - `Network Address`이 같으며, `Host Address`은 달라야 함
    - 이렇게 통신 시, 별도의 통신장비가 필요하지 않은 영역을 `Broadcast Domain`라고 함
- 다른 Network에서의 통신
    - `Network Address`이 다른 경우, Router나 Gateway 같은 통신장비를 통해 통신할 수 있음

#### Subnet Mask로 Network Address 확인하기
- `IP Address` **AND** `Subnet Mask` = **Network Address**
- 목적지가 **Local Host**인지, **Remote Host**인지 판단
    - `Own IP Address` & `Subnet Mask`
    - `Destination IP Address` & `Subnet Mask`
    - 일치하면 **Local Host**, 그렇지 않으면 **Remote Host**

##### 동일한 Network Address에 있는 경우 (Local Host)
- 현재 Subnet 내에서 data (Packet) 직접 전송

##### 다른 Network Address에 있는 경우 (Remote Host)
- TCP/IP 설정에 정의된 기본 Gateway로 data (Packet) 전송
- 이후에는 Router가 해당 Packet을 올바른 Subnet으로 전달

--- 
[참고 자료]
- [Microsoft Learn](https://learn.microsoft.com/en-us/troubleshoot/windows-client/networking/tcpip-addressing-and-subnetting?utm_source=chatgpt.com)