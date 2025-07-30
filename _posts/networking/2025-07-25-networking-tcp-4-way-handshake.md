---
title: "TCP 4-Way Handshake 직접 확인해보기"

categories:
  - networking
tags: [networking, tcp/ip, cs, wireshark]

date: 2025-07-25
last_modified_at: 2025-07-25
---

## TCP 연결 종료 (4-Way Handshake)

### 왜 종료도 절차가 필요할까?

- TCP는 신뢰성 있는 통신을 보장
- 연결을 시작할 때와 마찬가지로, 종료할 때도 명확한 절차를 거쳐야 함
- 데이터 수신과 전송은 각각 독립적으로 종료되어야 하므로 4단계로 이루어짐 
    - 양방향으로 FIN을 교환하며 닫는다 (Full Duplex 종료)

### 종료 순서 요약

| 단계 | 전송자 | 메시지(TCP Flag) | 설명 |
| -- | -- | -- | -- |
| 1 | Client | `FIN` | 더 이상 보낼 data가 없음을 알림 |
| 2 | Server | `ACK` | Client의 `FIN` 요청을 수락 (확인) |
| 3 | Server | `FIN` | Server 도 보낼 data 가 없음을 알림 |
| 4 | Client | `ACK` | Server의 `FIN` 요청을 수락 (확인) **이후 일정 시간 `TIME_WAIT` 상태 유지** |

#### TCP Packet의 Flag

- TCP Header에는 여러 개의 제어 Flag 가 존재
- Packet 안에 담겨 전송되는 제어 메시지이며, TCP 상태 변화를 유도

##### 3-Way Handshake, 4-way Handshake에서 사용되는 Flag

| Flag | 3-Way | 4-Way | 설명 |
| -- | -- | -- | -- |
| `SYN` | ✅ | ❌ | 연결 시작용 |
| `ACK` | ✅ | ✅ | 응답 확인용 |
| `FIN` | ❌ | ✅ | 연결 종료 요청 |
| `RST` | ❌ | ❌ | 비정상 종료 시에만 사용 |
| `PSH` | ❌ | ❌ | 데이터 전송 시에만 사용 |
| `URG` | ❌ | ❌ | 긴급 데이터에만 사용 |
| `ECE`/`CWR` | ❌ | ❌ | 혼잡 제어 용도, handshake와 무관 |


#### TIME_WAIT 상태란?

- TCP 4-Way Handshake 에서 먼저 `FIN` 요청한 대상(Active Closer)이 마지막 ACK를 보낼 때, 일정 시간 `TIME_WAIT` 상태에 진입
- `TIME_WAIT` 상태의 핵심 목적
  - 이전 연결의 지연된 packet이 네트워크 상 남아 있을 가능성 방지
    - 만약 연결이 완전히 닫히자 마자 같은 소켓쌍 (IP + Port)으로 새 연결을 맺으면, 이전 연결의 늦은 `FIN`이나 데이터 packet이 새 연결로 잘못 해석될 위험이 있음
    - ➡️ `TIME_WAIT` 동안 기다리며 지연된 packet이 네트워크에서 사라지길 기다림
    - such as **다음 대화와 헷갈리지 않게 잠시 숨 고르는 시간**
  - 상대방이 `FIN` 재전송 시 `ACK` 응답 보장
    - 마지막 `ACK`가 유실될 경우, 서버(Passive Closer)는 `FIN`을 다시 보낼 수 있음
    - Active Closer가 `TIME_WAIT` 상태에 있으면 다시 `ACK`를 보내줄 수 있어, 연결이 완전하게 종료됨
    - such as **상대방이 혹시 마지막 인사를 못 들었을까봐 몇 초간 기다리는 상태**

- 일반적으로 `TIME_WAIT` 지속 시간은 MSL(Maximum Segment Lifetime) 의 2배 (약 2분) 으로 설정됨
