---
title: "TCP 와 UDP 비교하기"

categories:
  - networking
tags: [networking, tcp/ip, cs, tcp, udp]

date: 2025-07-31
last_modified_at: 2025-07-31
---

## 1. TCP vs UDP

### 연결 방식, 신뢰성, 순서 보장 등 기본 개념 비교

#### 개요
- TCP: Transmission Control Protocol
- UDP: User Datagram Protocol
- TCP와 UDP 는 전송 계층 (Transport Layer) 에서 동작하는 대표적인 프로토콜
- 각각의 특징에 따라 **신뢰성 vs 속도**, **순차성 보장 vs 순서 무관** 등 장단점이 뚜렷
  - 상황에 따라 적절한 선택이 중요

#### 주요 비교표

| 항목 | TCP | UDP |
| -- | -- | -- |
| 연결 방식 | 연결 지향 (Connection-Oriented) | 비연결지향 (Connectionless) |
| 신뢰성 보장 | O (packet 손실 시 재전송) | X |
| 순서 보장 | O | X |
| 흐름 제어 | O (수신자 상태 반영) | X |
| 혼잡 제어 | O (네트워크 상태 반영) | X |
| 속도 | 느림 (오버헤드 큼) | 빠름 (경량 프로토콜) |
| 전송 단위 | Segment | Datagram | 
| Header 크기 | 최소 20 bytes | 8 bytes |
| 사용 예시 | HTTP, SMTP, FTP 등 | 실시간 스트리밍, DNS, DHCP, VoIP 등 |


#### 실전 예시: 어떤 상황에 어떤 프로토콜을 써야 하는가?

| 상황 | 적합한 프로토콜 | 이유 |
| 웹 페이지 접속, 파일 다운로드 | TCP | 데이터 누락/중복 없이 전달 보장 필요 |
| 온라인 게임, 실시간 음성 토신 | UDP | 지연이 치명적인 경우 |
| DNS | UDP (기본) | 매우 빠른 응답 요구. 손실 시 재질의 |

## 2. TCP vs HTTP Keep-Alive

### TCP 연결 자체와 HTTP에서의 Keep-Alive 차이

| 항목 | TCP 연결 | HTTP Keep-Alive |
| -- | -- | -- |
| 의미 | OS 수준의 Transport Layer 연결 | HTTP Protocol의 Application Layer Option | 
| 대상 | Server와 Client간 TCP Session | HTTP Request/Response을 동일 TCP Session 위에 지속적으로 전송 |
| 목적 | 연결 자체를 구성하여 데이터를 주고 받음 | 매번 TCP 연결 생성/해제 비용을 줄이기 위해 TCP 연결을 재사용 |
| 기본 적용 | HTTP/1.1부터 기본 활성 | TCP 는 HTTP와 무관하게 항상 존재해야 통신 가능 |

- TCP는 전제 조건, HTTP Keep-Alive 는 그 TCP 연결을 얼마나 오래 쓸지에 대한 정책


### Keep-Alive가 왜 필요한가?

- HTTP/1.0 에서는 요청 하나마다 TCP 연결 생성 & 종료 해야 했음 ➡️ 매 요청마다 3-Way Handshake + 4-Way Handshake ➡️ 매우 비효율적!
- 따라서 TCP 연결을 재사용하여
  - 연결 오버헤드 감소 ⬇️ (Handshaking 비용 절감)
  - 성능 ⬆️ (응답 속도 향상)
  - 서버 부하 ⬇️ (자원 절약)

### HTTP/1.1, HTTP/2, HTTP/3에서의 차이

| HTTP ver | Keep-Alive 방식 | 특징 |
| -- | -- | -- |
| HTTP/1.0 | 기본 비활성(활성화를 원한다면 `Connection:keep-alive` 명시 필요) | 명시하지 않으면 요청마다 TCP 연결 생성 & 종료 |
| HTTP/1.1 | 기본 활성화 (Keep-Alive Default) | `Connection: close` 명시 시에만 연결 종료 |
| HTTP/2 | TCP 연결 하나로 **Multiplexing** | 하나의 TCP 연결에서 동시 요청 처리 (Keep-Alive 보다 발전된 구조) |
| HTTP/3 | TCP 대신 QUIC(UDP 기반) 사용 | 연결 재사용은 계속되나 전송 계층 자체가 바뀜 |

#### 예시 흐름

- HTTP/1.0 (Keep-alive 비활성)
  - Request 1: `TCP 연결 ➡️ Request ➡️ Response ➡️ TCP 종료`
  - Request 2: 다시 `TCP 연결 ➡️ Request ➡️ Response ➡️ TCP 종료`
    - 매 요청마다 새로운 TCP 연결
- HTTP/1.1 (Keep-alive 기본 적용)
  - Request 1: `TCP 연결 ➡️ Request ➡️ Response`
  - Request 2 ~ n: 같은 TCP 연결 재사용
  - 일정 시간 비활성 상태 지속 시, Server 또는 Client가 `TCP 연결 종료` 

#### 요약 정리

- **TCP 연결**: Data 송수신을 위한 Transport Layer의 기반 통신
- **HTTP Keep-Alive**: 같은 TCP 연결을 HTTP 요청 간 재사용하자는 Application Layer 기능
- HTTP/2, HTTP/3: Keep-Alive를 넘어선 진화 ➡️ 더 효율적인 병렬 요청 처리

## 3. HTTP Keep-Alive

### 작동 방식

- HTTP/1.0: 기본적으로 Request-Response 1회마다 TCP 연결 종료
  - 각 요청마다 `연결 설정(TCP 3-Way Handshake)` + `Data 전송` + `연결 종료(TCP 4-Way Handshake)`
    - ➡️ 매우 비효율적!
- HTTP Keep-Alive 는 여러 HTTP 요청을 하나의 TCP 연결에서 처리하도록 지원
  - 하나의 TCP 연결을 유지 (persistent connection) 하여 여러 요청을 처리

### Connection: keep-alive vs close

| HTTP header | 의미 |
| -- | -- |
| Connection: keep-alive | 현재 TCP 연결을 유지하고, 다음 요청도 같은 연결로 처리하겠다는 요청 |
| Connection: close | 이 요청을 끝으로 TCP 연결을 즉시 종료하라는 지시

- HTTP/1.0 에서는 Connection: keep-alive를 명시해야 지속 연결
- HTTP/1.1부터는 keep-alive가 default. 연결을 끊으려면 close로 명시해야 함

### 서버/클라이언트 설정 고려사항

- Server 측 고려사항
  - Keep-Alive Timeout 설정
    - 너무 짧으면 재연결 발생, 너무 길면 리소스 낭비 (Connection pool 고갈 위험)
  - 최대 Keep-Alive 요청 수 제한 (Max-requests)
  - 비정상 종료 대비한 Connection 정리 전략 필요

- Client 측 고려 사항
  - 재사용 가능한 TCP 연결을 Connection Pool로 관리
  - HTTP Proxy나 Load Balancer 존재 시, 연결 지속 여부 확인 필요

#### Connection Pool

- Connection pool: 동시에 처리할 수 있는 최대 connection 수
  - Connection pool 고갈: 서버가 새 연결을 더 이상 받지 못해 장애 발생 (Timeout / Connection Refused)

- 왜 Keep-Alive timeout이 너무 길면 리소스가 낭비될까?
  - TCP 연결 상태 유지 = Server가 Memory, CPU, 소켓 등을 점유한다는 뜻
  - 즉, 연결이 유지된 상태에서 요청이 오지 않는다면?
    - 리소스만 계속 잡아먹는 상태

### 실전 성능 최적화에서의 중요성

- 지연 감소: TCP 연결 재설정 비용 제거 (Handshake, TIME_WAIT)
- CPU/Memroy Resource 절약: 재연결보다 지속 연결 유지가 효율적
- 동시성 향상: Client 당 연결 수 제한 상황에서 효율적 자원 활용 가능
- 웹 성능 최적화의 기초: image, CSS, JS 등 다수 요청 처리에 핵심
- 많은 Web Server와 CDN 도 기본적으로 Keep-Alive 적극 활용


## 4. TCP Fast Open (TFO)

### 전통적인 TCP 3-Way Handshake vs TFO

- TFO: 전통적인 TCP 3-way Handshake의 RTT를 줄이는 방식

| 구분 | 전통적인 TCP | TCP Fast Open (TFO) |
| -- | -- | -- |
| 연결 과정 | TCP 연결 설정(3-Way Handshake) 후 Data 전송 시작 | SYN Packet에 Data를 포함시켜 보냄 (0-RTT 전송) |
| 최초 요청 시도 | SYN ➡️ SYN-ACK ➡️ ACK ➡️ Data 전송 | SYN(+Data 전송) ➡️ SYN-ACK ➡️ ACK(+Data 수신) |
| 속도 | RTT 1회 후 데이터 전송 | 데이터 즉시 전송이 가능 (최대 1 RTT 단축) |

### Fast Open의 원리 (쿠키 기반 0-RTT 연결 시도): TCP 연결 지연 최소화

- TFO는 Cookie 기반 신뢰 관계를 통해 data를 조기 전송
  - 아는 사람(cookie)이라서 문 열자마자 바로 말하는 느낌
1. 초기 연결 시
  - Client가 TFO 지원 Server에 접속
  - Server는 응답에 TFO-Cookie를 포함하여 전송
  - Client는 Cookie를 local에 저장
2. 이후 재접속 시
  - Client 는 Cookie와 함께 SYN+Data를 Server에 전송
  - Server는 Cookie가 유효하다면, SYN-ACK + 수신 처리

### 장점 및 보안 이슈

#### 장점
- 지연 시간 (RTT) 감소
  - SYN Packet에 data를 함께 전송하므로 요청이 더 빠르게 처리됨
- 모바일/무선 환경에서 유리(빈번한 재접속에 최적화)
- Google, Facebook 등 대규모 서비스에서 실험적으로 도입

#### 보안 이슈 및 제약
- 쿠키 위조
- SYN Flood 공격 악용
  - 초기 요청에 데이터 포함 ➡️ 서버 부담 ⬆️
- Middlebox 호환성 문제
  - 일부 Router/Firewall 이 SYN에 data 포함된 packet을 차단
- 초기 연결엔 적용 불가
  - TFO Cookie가 없으면 일반 3-Way Handshake처럼 작동

## 5. TLS 0-RTT (Zero Round-Trip Time): 보안 계층에서도 속도 개선 필요

- TLS(Transport Layer Security): 인터넷에서 데이터를 암호화해 안전하게 주고받도록 하는 보안 프로토콜
  - Application/Presentation Layer에서 동작하는 프로토콜
  - 예: HTTPS: HTTP + TLS
- TLS는 TCP 위에서 동작
  - (1) Client -> TCP 연결 (3-Way Handshake)
  - (2) Client <-> Server: TLS Handshake (암호화 방식 협상, 인증서 등)
  - (3) TLS Handshake 완료되면 <-> Application Data (HTTP 요쳥/응답, ...)
    - 즉, TCP가 안정적인 통신 통로를 만들어 주면, TLS 는 그 위에 안전한 암호화 터널을 구축하는 것

### TLS 1.3의 핵심 개선: 핸드셰이크 간소화
- TLS 1.2
  - Handshake 과정이 최소 2 RTT (왕복 2회) 필요
  - Client - Server가 서로를 확인한 뒤에만 Data 전송
- TLS 1.3
  - 암호 스위트 단순화 + 암호화 기본 적용
  - Handshake 과정이 1 RTT (0 RTT 까지 가능)
  - 더 빠르고 더 안전하게 연결 가능

### 0-RTT 데이터 전송이란?
- Client가 과거에 연결한 적이 있는 서버라면, Handshake 없이 곧바로 데이터 전송 가능 (0-RTT)
- 방법
  - 이전에 받은 **session ticket/PSK(Pre-Shared Key)**를 재사용
  - Server는 이를 검증한 뒤, Data 처리 바로 시작

### 장점과 재전송 공격(Replay Attack) 이슈

#### 장점
- 0-RTT: 사용자 체감 성능 개선
- 모바일, 무선 환경에서 효과적 (끊김 후 빠른 재연결 가능)
- 재사용성: session ticket으로 TLS 연결 빠르게 재구성 가능

#### 재전송 공격 이슈

- 0-RTT data는 암호화는 되어있지만 서버는 client를 아직 완전히 검증하지 않은 상태
- 악의적인 제3자가 초기 전송 데이터를 복사해서 다시 보내면, 서버가 이를 중복 처리할 수 있음
- 예시
  - 사용자가 0-RTT로 쇼핑몰에서 결제 요청 보냄
  - 이를 공격자가 복사해 한 번 더 보냄 -> 이중 결제 가능성

- TLS 1.3 대응 방법
  - 서버는 0-RTT data에 대해 재처리 불가능한 API만 허용
  - Idempotent(멱등성, 어떤 연산을 여러 번 적용하더라도 결과가 변하지 않는 성질)한 요청만 0-RTT 로 처리하도록 제한



