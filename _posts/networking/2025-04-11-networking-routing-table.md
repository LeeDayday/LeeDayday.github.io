---
title: "Routing Table 에 대하여"

categories:
  - networking
tags: [networking, cs]

date: 2025-04-11
last_modified_at: 2025-04-11
---

### Routing Table
#### 역할
- Router 나 Computer가 Destination IP Address를 보고, Packet을 어디로 보낼지 결정하는 table
- default route
#### 기본 구조
- 목적지 주소
- 
| Destination IP Address | Subnet Mask | Next Hop | Interface | Metric |
| -- | -- | -- | -- | -- |
| `192.168.1.0` | `255.255.255.0` | `0.0.0.0` (직접 연결) | eth0 | 0 |
| `0.0.0.0` (기본 경로)	 | `0.0.0.0` | `0.0.0.0` | eth0 | 100 |
