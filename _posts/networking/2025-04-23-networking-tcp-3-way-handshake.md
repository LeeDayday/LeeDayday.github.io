---
title: "TCP 3-Way Handshake 직접 확인해보기"

categories:
  - networking
tags: [networking, tcp/ip, cs, wireshark]

date: 2025-04-23
last_modified_at: 2025-04-23
---

## TCP 연결 설정의 단계 (3-Way Handshake)

### 3-Way Handshake 간단 정리
- 신뢰성 있는 TCP 연결을 설정하기 위해 Sender 와 Reciever 간 3개의 Segment가 교환됨

| 단계 | 방향 | Seq | Ack | Flags |
| -- | -- | -- | -- | -- |
| 1. SYN | A -> B | x | - | SYN |
| 2. SYN+ACK | A <- B | y | x + 1 | SYN, ACK |
| 3. ACK | A -> B | x + 1 | y + 1 | SYN |

### 주요 TCP State 목록
- client - server 간 통신이라고 가정

![TCP states](https://media.geeksforgeeks.org/wp-content/uploads/20241227141550358912/tcp.webp)

| 상태(State) | 설명 |
| -- | -- |
| LISTEN | Server가 연결 요청(SYN)을 기다리는 중 (수동 대기 상태) |
| SYN_SENT | Client가 SYN을 보내고 ACK를 기다리는 중 |
| SYN_RECEIVED | Server 가 SYN을 받고 SYN+ACK를 보낸 후 ACK를 기다리는 중 |
| ESTABLISHTED | 연결이 정상적으로 수립된 상태 (데이터 송수신 가능) |
| FIN_WAIT_1 | 연결 종료 요청(FIN) 을 보낸 후 상대의 ACK를 기다리는 중 |
| FIN_WAIT_2 | 상대가 ACK를 보냈고, 이제 상대의 FIN을 기다리는 중 |
| CLOSE_WAIT | 상대가 FIN을 보냈고, 나는 아직 닫지 않음 |
| LAST_ACK | FIN을 보내고 상대의 ACK를 기다리는 중 |
| TIME_WAIT | 내 FIN/ACK 이후 상대가 보낼 수도 있는 패킷을 대비하여 대기 (보통 2MSL: 1-4분) |
| CLOSED | 연결이 완전히 종료된 상태 (사용 안 함) |


## WireShark로 TCP 3-Way Handshake 직접 확인해보자

### 캡쳐할 Packet
- netcat 명령어로 google에 HTTP 요청 시도
    ```
    nc google.com
    GET / HTTP/1.1

    ````
- 결과
    - HTTP 응답 헤더, HTML 파일 반환
    ```
    HTTP/1.1 200 OK
    Date: Wed, 23 Apr 2025 02:39:29 GMT
    Expires: -1
    ...(생략)
    ```

---

### 0. DNS 조회
- google.com 이라는 domain을 IP address로 변환하기 위해 DNS query가 이루어짐
- DNS 질의는 대부분 UDP protocol (port 53) 사용 
    - 응답 용량이 큰 경우, TCP protocol (port 53) 사용

#### DNS Query Packet

| No. | Time | Source | Destination | Protocol | Length | Info | 
| -- | -- | -- | -- | -- | -- | -- |
| 20 | 10.274590 | my_ip | 210.220.163.82 | DNS	| 70 | Standard query 0x98ef A google.com |

![DNS Query Image](/assets/images/networking/dns_query.png){: .align-center}
- `type A`: IPv4를 요청하는 DNS Query
- `Class IN`: Internet

#### DNS Answer Packet

| No. | Time | Source | Destination | Protocol | Length | Info | 
| -- | -- | -- | -- | -- | -- | -- |
| 21 | 10.279067 | 210.220.163.82 | my_ip | DNS | 86 | Standard query response 0x98ef A google.com A 142.250.196.142 |

![DNS Answer Image](/assets/images/networking/dns_answer.png){: .align-center}
- `Name google.com`: 질의한 domain 이름
- `Type A`: IPv4를 의미하는 DNS Record
- `Class IN`: Internet
- `Time to Live 267`: 267초동안 cache에 저장해둠. 재질의하지 않아도 유효한 응답을 줄 수 있다는 의미
- `Data length 4`: IPv4 = 32 bit = 4 bytes
- `Address 142.250.196.142`: 최종적으로 응답받은 IP Address

- 추가 Tip
    - 알아낸 google.com의 IP Address로 Wireshark에 Filtering 하면 원하는 Packet을 좀 더 쉽게 찾을 수 있다.
    - `ip.src == 142.250.196.142 || ip.dst == 142.250.196.142`

### 1. Send SYN

- Reciever 입장에서 설명

| No. | Time | Source | Destination | Protocol | Length | Info | 
| -- | -- | -- | -- | -- | -- | -- |
| 22 | 10.280463 | my_ip | 142.250.196.142 | TCP | 78 | 53747 → 80 [SYN] Seq=0 Win=65535 Len=0 MSS=1460 WS=64 TSval=596778860 TSecr=0 SACK_PERM |

- `53747 → 80`: `source`의 임시 port → `destination(google.com)` 의 HTTP port
- `[SYN]`: 연결 요청 Flag
- `Seq=0`: Relative Sequence Number: 0
    - Sequence Number (raw): 2632373117
- `Win=65535`: 윈도우 크기 (수신 버퍼 용량)
- `MSS=1460`: 최대 segment 크기
- `WS=64`: Window scaling 옵션
- `TSval=596778860 TSecr=0`: TimeStamp 옵션 (for RTT 계산)
- `SACK_PERM`: Selective ACK 허용 여부 (손실 복구 최적화)

### 2. Receive SYN, ACK

| No. | Time | Source | Destination | Protocol | Length | Info | 
| -- | -- | -- | -- | -- | -- | -- |
| 23 | 10.316859 | 142.250.196.142 | my_ip | TCP | 74 | 80 → 53747 [SYN, ACK] Seq=0 Ack=1 Win=65535 Len=0 MSS=1412 SACK_PERM TSval=3455834059 TSecr=596778860 WS=256 |

- `80 → 53747`: `destination(google.com)`의 HTTP port → `source`의 임시 port 
- `[SYN, ACK]`:  연결 요청에 대한 수락 응답 Flag
- `Seq=0`: Relative Sequence Number: 0 
    - Sequence Number (raw): 2691239502
- `Ack=1`: Relative Acknowledgment Number: 1 (SYN에 대한 응답, Seq + 1)
    - Acknowledgment number (raw): 2632373118 (=2632373117 + 1)
- `Win=65535`: 윈도우 크기 (수신 버퍼 용량)
- `MSS=1412`: 최대 segment 크기
- `WS=64`: Window scaling 옵션
- `TSval=3455834059 TSecr=596778860`: TimeStamp 옵션 (for RTT 계산)
- `SACK_PERM`: Selective ACK 허용 여부 (손실 복구 최적화)


### 3. Send ACK

| No. | Time | Source | Destination | Protocol | Length | Info | 
| -- | -- | -- | -- | -- | -- | -- |
| 24 | 10.316962 | my_ip | 142.250.196.142 | TCP | 66 | 53747 → 80 [ACK] Seq=1 Ack=1 Win=131584 Len=0 TSval=596778896 TSecr=3455834059

- `53747 → 80`: `source`의 임시 port → `destination(google.com)` 의 HTTP port
- `[ACK]`: 연결 확인 Flag
- `Seq=1`: Relative Sequence Number: 1
    - Sequence Number (raw): 2632373118
- `Ack=1`: Relative Acknowledgment Number: 1 (SYN에 대한 응답, Seq + 1)
    - Acknowledgment number (raw): 2691239503 (=2691239502 + 1)
- `Win=131584`: 윈도우 크기 (수신 버퍼 용량)
- `TSval=596778896 TSecr=3455834059`: TimeStamp 옵션 (for RTT 계산)


---
#### 새롭게 알게 된 점
- 요청을 보낼 때 Destination Port는 반드시 명시되어야 함
    - 요청하려는 서비스가 어떤 건지 알려주기 위해
- 요청을 보낼 때 Source Port는 보통 자동으로 지정됨 (임시 포트. ephemeral port)
    - 응답을 어디로 받아야 할지 식별하기 위해 필요
- TCP에서 ACK number는 '내가 다음으로 기대하는 sequence number'를 의미
    - 즉, 상대가 보낸 SYN의 seq=x에 대해 ACK=x+1을 보내면, 👉 "너의 SYN(x)을 잘 받았고, 그 다음 seq를 기다릴게" 라는 뜻