---
title: "Subnet 개념 이해하기"

categories:
  - networking
tags: [networking, cs, tcp/ip]

date: 2025-04-11
last_modified_at: 2025-04-11
---

### 개념 비유
- **하나의 IP Network** : `192.168.0.0/16`
  - 전체 아파트 단지
- **Subnet**: `192.168.1.0/24`
  - 아파트 101동, 102동, ...
  - 전체 아파트 단지를 동으로 나누는 것이 **Subnetting**
  - 큰 단지를 관리하기 좋게 작은 단위로 나누는 행위
- **Subnet Mask**

### Subnet
- Subnet = Sub Network
- 하나의 큰 Network (IP block)를 작게 나눈 '하위 네트워크'

### Subnet Mask
- IP Address & Subnet Mask ➡️ Network Address
- 예시
```text
IP 주소      : 192.168.1.10     → 11000000.10101000.00000001.00001010
Subnet Mask : 255.255.255.0    → 11111111.11111111.11111111.00000000
--------------------------------------------------------------
AND 결과       :                 → 11000000.10101000.00000001.00000000
                  = 192.168.1.0   → 이게 바로 **네트워크 주소**
```
  - Subnet Mask: 255.255.255.0 = 11111111.11111111.11111111.0
  - 연속된 1의 개수: 24
  - 상위 24bit가 네트워크 영역 (= /24)
- 즉, *Subnet Mask에서 연속된 1의 개수 = CIDR 표기값*