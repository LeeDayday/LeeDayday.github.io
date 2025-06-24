---
title: "OSI Model 에 대하여"

categories:
  - networking
tags: [networking, osi]

date: 2025-06-18
last_modified_at: 2025-06-18
---

## OSI 모델이란?

### 정의 및 목적
- OSI (Open Systems Interconnection) model
- Network 통신을 7개의 계층으로 나눈 표준 참조 모델
- 다양한 시스템 간의 통신이 서로 호환 가능하고 일관되게 이루어질 수 있도록

### 등장 배경
- 과거엔 컴퓨터 제조사마다 통신 방식이 제각각 (자체적인 프토콜 사용)
- 서로 다른 시스템 간 연결/데이터 교환이 어려움
- OSI 모델은 실제 프로토콜을 강제하지는 않지만, 네트워크를 설명하고 설계하는 기준 프레임워크로 자리잡음

### TCP / IP 모델과의 차이

| 항목 | OSI model | TCP/IP model |
| -- | -- | -- |
| 사용 | 이론 모델 (교육/설계 중심) | 실제 인터넷에서 사용되는 표준 |
| 구조 | 계층마다 역할이 명확히 분리 | 계층이 통합되어 상대적으로 단순 |
| 예시 | 세션 / 표현 계층 존재 | 응용 계층에 통합됨 |

- 현실에서는 TCP/IP가 실제 통신에 사용되지만, 네트워크를 학문적으로 설명하거나 디버깅할 때는 OSI 모델을 기준으로 삼는 경우가 많음

## OSI 7계층 구조

### 계층 목록
- 각 계층은 하위 계층에 의존하면서 상위 계층에 서비스를 제공하는 구조
- 위 > 아래: Data 전송 과정

| 계층 번호 | 계층 이름 | 주요 역할 | 예시 Protocol 및 장비 |
| -- | -- | -- | -- |
| 7 | Application (응용) | 사용자에게 네트워크 기능 제공 | HTTP, FTP, SMTP, DNS |
| 6 | Presentation (표현) | 데이터 형식, encoding/decoding, 암호화 | SSL/TLS, JPEG, MPEG |
| 5 | Session (세션) | 연결(session) 설정, 유지, 종료 | NetBIOS, RPC |
| 4 | Transport (전송) | 신뢰성 보장, port number, 흐름 제어 | TCP, UDP |
| 3 | Network (네트워크) | IP 주소 지정, routing | IP, ICMP, Router |
| 2 | Data Link (데이터 링크) | MAC 주소 기반 통신, 오류 검출 | Ethernet, ARP, Switch |
| 1 | Physical (물리 계층) | 비트 전송, 전기적 신호, 케이블 | UTP, 광케이블, 허브 |

### 위 > 아래 방향성과 통신 흐름
- Source 측 (Application Layer -> Physical Layer 순으로)
- 각 계층은 상위 계층의 data를 받아 **header 를 붙여 캡슐화**

1. 사용자가 웹페이지 요청 (7)
2. data 형식 맞춤, 암호화 등 처리 (6)
3. 세션 생성 (5)
4. TCP/UDP 로 신뢰성 보장 (4)
5. IP Address 붙여 경로 결정 (3)
6. MAC 주소 붙이고 frame 화 (2)
7. 전기 신호로 전송 (1)


## OSI 모델을 왜 배우는가?

### Debugging, Network 설계 시 이점
- OSI model은 복잡한 network 문제를 체계적으로 분석할 수 있게 해줌
  - 계층별로 역할이 분리 ➡️ 문제 발생 시, 어느 계층에서의 문제인지 구분하기 쉬움

### 문제 원인 계층별 추적
- 문제 상황 예시: `웹페이지가 안 떠요!`
- OSI 계층에 따라 분석해보기
  - 계층 별로 문제를 나누어 debugging 범위를 좁힐 수 있음

| 계층 | checkpoint |
| -- | -- |
| 1 계층 | 케이블이 제대로 꽂혔는가? 와이파이 연결은 되었는가? |
| 2 계층 | Switch에서 MAC Address 충돌은 없는가? |
| 3 계층 | IP Address 는 설정되었는가? Gateway는 정상인가? |
| 4 계층 | TCP 연결이 이루어졌는가? Port가 열려있는가? |
| 7 계층 | Web Server가 실행중인가? URL이 올바른가? |

### 보안/성능/분산 시스템 설계에서의 활용
- 보안에서의 활용
  - 계층마다 보안 이슈가 다름
    - 2 계층: ARP Spoofing
    - 3 계층: IP Spoofing
    - 4 계층: Port Scanning
    - 7 계층: SQL Injection, XSS
- 성능 최적화
  - 4 계층(TCP 조정), 3 계층(Routing), 2 계층(MAC Cache 조정) 등 각 계층 단위로 튜닝 가능
- 분산 시스템 설계
  - 데이터 전송 경로, 보안 계층, 포맷 처리 등 계층 단위로 분리해 설계 가능

## OSI 모델과 TCP/IP 모델 비교

### 계층 통합 여부

| 항목 | OSI model (7 layers) | TCP/IP model (4 layers) |
| -- | -- | -- |
| Application | Application (7) + Presentation (6) + Session (5) | Application Layer |
| Transport |  Transport (4) | Transport Layer |
| Network | Network (3) | Network Layer |
| Link | DataLink (2) + Physical (1) | Network Access Layer |

### 실제 network에선 TCP/IP 모델이 쓰이지만 OSI 모델로 설명하는 이유
- 실제 인터넷 통신은 TCP/IP 구조를 따름
- TCP/IP는 계층 간 구분이 애매한 겨웅가 많아, 문제 분석이나 설계 논의 시 OSI model 로 설명하는 것이 더 명확
- 예시
  - WireShark에서도 OSI model 기준으로 Protocol qnstjr
  - L2 Switch, L3 Router, L7 Load Balancer 등 모두 OSI 기준으로 설명
