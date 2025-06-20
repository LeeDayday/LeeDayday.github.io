---
title: "TCP/IP Interview Questions"

categories:
  - networking
tags: [networking, tcp/ip, interviews, cs]

date: 2025-03-02
last_modified_at: 2025-06-16
---

[TCP / IP - 50 Interview Questions]

참고 자료
[Geeks for Geeks](https://www.geeksforgeeks.org/top-50-tcp-ip-interview-questions-and-answers/?ref=next_article_top)

[깃허브](https://github.com/jeonyeohun/Getting-Ready-For-Interview)
### 1. TCP/IP는 무엇인가?
- TCP와 IP를 함께 묶어 부르는 용어
	- IP: Packet이 네트워크를 통해 목적지까지 도달할 수 있는 주소 체계 제공
		- *Packet을 목적지까지 전달*
	- TCP: 데이터를 작은 packet으로 나누고, 올바르게 전송되었는지 확인하며, 손실된 packet을 재전송하는 역할 수행
		- *Packet이 잘 갔나? 제대로 안 갔으면 재전송*
- TCP
	- Transmission Control Protocol (전송 체어 프로토콜)
	- 데이터의 안정적이고 정돈된 전달(신뢰성)을 보장하는 연결 지향 protocol
		- web browsing, email 같이 데이터 무결성이 중요한 application에 사용
		- 매 통신마다 데이터를 송수신하기 전, client - server 간 연결을 설정해주어야 함
	- 데이터를 **작은 부분으로 나누어 전송**하고, 데이터가 **제 시간에** **안전하게** 도착하는지 확인
- IP
	- Internet Protocol (인터넷 프로토콜)
	- 데이터 조각들이 올바른 도착지로 전달될 수 있도록 돕는 주소 체계

	- 32자리 이진수로 구성.
		- 8자리 / 8자리 / 8자리 / 8자리 이진수 => 255.255.255.255 십진수 (0~255)
	- 하나의 네트워크 안에 IP들은 네트워크 영역은 같아야 하고, 호스트 ip는 달라야 통신이 가능
		- 203.240.100.1
			- 네트워크 영역: 203.240.100
			- 호스트 IP: 1
			- 네트워크 주소와 호스트 주소를 구분하는 방법: **Class**

### 2. TCP/IP class
- IP 주소에서 **Network Address** 와  **Host Address** 를 나누는 기준
- 총 5개 (A, B, C, D, E)
	- 보통 A, B, C
	- D, E는 멀티캐스트용, 연구용으로 사용
- ![[Pasted image 20250221001617.jpg]]

	- **A Class**
		- 맨 앞자리 수(Leading bits) 가 항상 **0** 
			- 즉, 32자리의 이진수: ==0xxxxxxx==.xxxxxxxx.xxxxxxxx.xxxxxxxx
		- **Network Address**: 첫번째 옥테드 영역 (2^7 가지)
		- **Host Address**: 나머지 부분 (2^24  - 2가지) 
			- 제외되는 2가지 (13.0.0.0: 네트워크 주소, 13.255.255.255: 브로드캐스트 주소)
		- 표현 가능한 범위 (십진수)
			- 0.0.0.0 ~ 127.255.255.255
		- point: 첫번째 옥테드가 1~127이면 A Class

	- **B Class**
		- 맨 앞자리 수(Leading bits) 가 반드시 **10**
		- **Network Address**: 1~2번째 옥테드 영역 (2^(16-2) 가지)
		- **Host Address**: 나머지 (2 ^ 16  - 2 가지)
		- 표현 가능한 범위 (십진수)
			- 128.0.0.0 ~ 191.255.255.255

	 - **C Class**
		- 맨 앞자리 수(Leading bits) 가 반드시 **110**
		- **Network Address**: 1~3번째 옥테드 영역 (2^(24-2) 가지)
		- **Host Address**: 나머지 (2 ^ 8  - 2 가지)
		- 표현 가능한 범위 (십진수)
			- 192.0.0.0 ~ 223.255.255.255
	

### 3. Private IP란?
- **같은 네트워크 내에서** 통신할 때 사용되는 IP 주소
- Router가 네트워크 내 장치들에게 자동 할당, 각 장치는 고유한 Private IP를 가짐
	- Public IP 보다 안전함
		- 같은 네트워크에 연결된 다른 장치들은 서로의 private IP를 확인 O
			- 같은 Local Network 내에선 추적 가능
		- Public IP처럼 인터넷 상에선 볼 수 X

+) Public IP
- **네트워크 외부**와통신할 때 사용되는 IP 주소
- ISP (Internet Service Provice, 인터넷 제공 업체)에 의해 할당
	- 추적 용이
	- VPN으로 IP 주소를 숨기기도
- Dynamic IP Address
	- 시간이 지나면서 변경되는 주소
- Static IP Address
	- 시간이 지나도 변경되지 않는 영구적인 인터넷 주소
	- 주로 DNS 서버에 사용됨


### 4. IP는 네트워크에서 데이터를 보호하는가?
- IP 자체는 **데이터의 정확한 전달을 보장하지 않음**
- 데이터 보호 및 무결성 검사는 **전송 계층 (Transport Layer) - TCP / UDP** 의 역할
	- TCP와 UDP는 각각 데이터가 올바르게 전달되었는지 확인하는 자체적인 메커니즘이 있음
- IP Layer의 역할
	- OSI 7계층 모델 - Network Layer (3), TCP/IP 4계층 모델 - Internet Layer (2)
	- **Packet을 올바른 목적지로 식별 & 전달**
		- IP header의 무결성을 검증하기 위해 Checksum 적용 (IPv4)
			- 데이터 오류는 감지하지 못함
	- Transport Layer의 TCP, UDP가 데이터 무결성 확인
		- By Checksum
- IP 주소가 없는 경우 TTL(Time to Live)의 역할
	- TL: IP packet이 네트워크를 통과할 수 있는 최대 hop 수
		- hop: packet이 라우터를 한 번 통과하는 것
	- Packet은 TTL 값을 감소시키며 네트워크를 따라 이동
	-  TTL 값이 0이 된다면 Packet 폐기. 
- UDP의 문제점
	- TCP는 데이터 전송 실패 시, 재전송 요청
	- UDP는 전송 실패 감지 X
		- 발신자 (source) 는 packet 손실 여부를 알 수 없음

### 5. MAC Address란?
- Media Access Control Address
- 네트워크 인터페이스 (Wi-Fi, 이더넷 카드) 에 할당된 고유 식별자 (물리적인 주소)
- Local Network 내에서 장치를 식별하고 통신하는데 사용
- 일반적으로 16진수 형식 (예: 11:22:33:44:55:66)
+) IP Address 와 다른 점

| 구분       | MAC Address               | IP Address         |
| -------- | ------------------------- | ------------------ |
| 역할       | 네트워크 인터페이스 장치 식별          | 장치 간 통신을 위한 논리적 주소 |
| 범위       | LAN (Local Network 내에서만)  | LAN, WAN           |
| 변경 가능 여부 | 고유                        | 동적 할당 및 변경 가능      |
| 사용 목적    | Local Network 내 장치를 찾고 통신 | Network 간 통신 및 라우팅 |
| 주소 형식    | 16진수                      | IPv4: 32진수         |
- *왜 MAC 주소가 필요한가?*
	- 실제 데이터 전송에는 물리적인 주소가 필요
	- IP주소로 먼저 위치를 찾고
	- MAC주소를 사용해 실제 장치로 데이터 전달
---
- *예시 상황 1) LAN 통신*
	- PC1 (192.168.1.10) 이 PC2 (192.168.1.20)에게 데이터를 보내려고 함
		- 복습! 192니깐 해당 IP Address 는 C class. Network 영역은 192.168.1
	- PC1은 ARP (Address Resolution Protocol)을 사용해 PC2의 MAC 주소를 찾음
		- **ARP 요청**. 네트워크에 broadcast (모든 장치에게 물어봄. "이 IP의 MAC주소 아는 사람?")
		- **ARP 응답**. PC2: "헐 그거 나야. 내 MAC 주소는 AA:BB:CC:DD:EE:FF 야!"
	- PC2은 PC2의 MAC 주소를 확인한 후, 이더넷 프레임에 목적지 MAC 주소를 포함하여 데이터 전송
	- PC2는 자신의 MAC 주소와 일치하는 데이터 수신, 데이터 처리
- *예시 상황 2) WAN 통신*
	- 사용자가 google.com에 접속 -> IP 주소를 기반으로 네트워크 라우팅 진행
	- LAN에서는 MAC 주소가 필요
	- packet은 여러 router를 거치며 전달되지만, 각 Hop 마다 router는 자신의 MAC 주소를 사용하며 다음 장치로 전송

### 6. TCP 와 UDP 의 차이
- Transmission Control protocol (TCP)
	- 연결 방식
		- 연결 지향 (Connection-oriented) Protocol
		- 데이터 전송 전 반드시 연결 설정 & 전송이 끝나면 연결 종료
	- 신뢰성
		- 높은 신뢰성
		- **데이터를 목적지까지 반드시 전달**
	- 오류 검츨 및 흐름 제어
		- 기능 제공
		- 데이터 확인 응답 및 흐름 제어
	- 데이터 순서 보장
		- **순서대로 도착 보장 (Sequencing)**
	- 속도
		- **비교적 느림** (오버헤드 ⬆️)
	- 손실된 packet 재전송
		- O
	- Header 크기
		- 가변 (20 ~ 60 bytes)
	- 무게
		- Heavy weight protocol
	- Broadcast 및 Multicast 지원 
		- X
	- 사용 예시
		- HTTP, HTTPS, FTP, SMTP, Telnet 등 신뢰성이 중요한 서비스

- User datagram protocol (UDP)
	- 연결 방식
		- 비연결형 (Connectionless) Protocol
		- TCP 같은 사전 연결 작업, 사후 종료 이런 개념 없음 (= 오버헤드가 없다)
	- 신뢰성
		- 낮은 신뢰성
		- **전달 보장 X**
	- 오류 검출 및 흐름 제어
		- 기본적인 오류 검출 (Checksum) 만 제공
		- 흐름 제어 기능 X
	- 속도
		- 빠르고 효율적
	- 손실된 Packet 재전송
		- X
	- Header 크기
		- 고정 (8 bytes)
	- 무게
		- Light-weight protocol
	- Broadcast 및 Multicast 지원
		- O
	- 사용 예시
		- DNS, DHCP, TFTP, SNMP, RIP, VoIP

### 7. TCP/IP에서 Routing의 개념 설명
- Routing
	- TCP/IP에서 데이터가 한 컴퓨터에서 다른 컴퓨터로 이동하는 과정
	- Router는 주로 *교통 관리 역할* 수행
		- 데이터 주소 확인, 최적의 경로 선택
	- 데이터가 전송될 때, (여러 개의) Router를 거쳐 최종 목적지에 도착

### 8. TCP가 제공하는 services
- 프로세스 간 통신 (Process to process communication)
	- 송신 process - 수신 process간 직접적인 통신을 가능하게 함
	- Port number를 이용해 특정한 프로세스 식별
		- 80 포트: HTTP , 443: HTTPS, ...
- 스트림 지향 (Stream orientation)
	- 데이터를 packet으로 나누지 않고, 연속된 byte (stream) 처럼 다룸
	- application은 파일을 읽듯이 연속된 데이터 흐름을 처리할 수 있음
- 전이중 서비스 (Full duplex service)
	- 동시에 송&수신 가능 (양방향 통신 지원)
	- ex) 채팅 앱에서 동시에 메시지 주고 받을 수 있음
- 다중화 (Multiplexing)
	- 한 장치에서 여러 프로세스가 동시에 TCP를 사용할 수 있도록 관리
	- 송신 측에서 다양한 application (port number)을 구별하여 데이터 전송
		- 한 pc에서 동시에 웹 서핑 (80포트) + 이메일 송수신(25) + FTP 다운로드(21) 가능
- 신뢰성 (Reliability)
	- 데이터 손실, 순서 오류 등을 감지
	- 재전송 요청 (ACK, Retransmission) 으로 보장
	- Packet이 올바르게 도착했는지 확인 (by checksum)
	- 순서대로 도착하도록 순서 제어 수행

### 9. TCP Header Format
- 20 ~ 60 bytes (가변: 필수 (20bytes) + option (최대 40bytes))
	- 필수 20을 맞추기 위해 reserved (3 bits) + flag bit (9 bits) 이나 reserved (6 bits) + flag bit (6 bits) 로 하는 것 같음
- 여러 field 포함
	- 데이터를 신뢰성 있게 전송하기 위하여
		- 데이터 흐름 제어, 오류 감지
- **Source Port Address** (16 bits -> 2 bytes)
	- 송신지 (발신자) 포트 번호
	- 어떤 application (process) 에서 보낸 것인지 식별
- **Destination Port Address** (16 bits -> 2 bytes)
	- 목적지 (수신자) 포트 번호
	- 데이터를 수신하는 application (ex. 웹 서버: 80 포트) 식별
- **Sequence Number** (32 bits -> 4 bytes)
	- 데이터 순서를 맞추기 위한 Sequence Number
	- TCP가 packet을 올바르게 정렬할 수 있도록
- **Acknowledgement Number** (32bits -> 4 bytes)
	- 수신 확인 번호 (ACK)
	- 정상적으로 수신된 다음에 기대하는 순서 번호
- **HLEN or Offset** (4 bits)
	- TCP Header의 길이를 나타냄 (20 ~ 60 bytes)
- **Reserved** (6 bits)
	- 향후 사용을 위해 예약된 필드
- **Flag bit** (1bit 씩 총 9개)
	- TCP packet의 상태와 동작을 결정하는 control bit
		- URG (긴급 데이터), ACK (수신 확인 응답), PSH (즉시 전달), RST (연결 초기화), SYN (연결 시작), FIN (연결 종료), 
		  CWR (혼잡 제어), ECE (혼잡 발생 여부 알림), NS(실험적인 보안 기능. 거의 안 씀)
- **Window Size** (16 bits -> 2 bytes)
	- source가 한 번에 전송할 수 있는 최대 데이터 크기
	- 흐름 제어 (Flow Control) 기능 수행
- **Checksum** (16 bits -> 2 bytes)
	- 데이터의 무결성 검사
	- 오류 발생 여부를 확인하여 손상된 데이터 폐기 / 재전송 요청
- **Urgent Pointer** (16 bits -> 2 bytes)
	- Flag bit의 URG가 설정된 경우, 우선 처리할 데이터 위치 지정
	- 긴급 데이터 (ex. ctrl + c 같은 interrupt 신호)를 빠르게 전달
---
- Options and Padding (최대 40 bytes)
	-  추가적인 옵션 데이터 (ex. 타이밍 정보, 보안 기능)
	- TCP header 크기를 4 bytes 배수로 맞추기 위해 padding 사용

### 10. TCP Flag의 내용은?
- *URG*: 긴급 포인터가 유효
- *ACK*: 응답 번호 (ACK num)가 유효함 (=packet이 정상적으로 도착했다)
- *PSH:* 즉시 데이터 전송 요청
- *RST:* 연결 초기화 (=강제 종료가 필요할 때)
- *SYN:* 순서 번호 (Synchronize Sequence Numbers) 동기화 (=TCP 연결을 시작할 때)
- *FIN:* 연결 종료 요청 (=TCP 연결 정상적 종료)

### 11. TCP Checksum Field의 역할은?
- TCP Protocol 에서 중요한 필수 Field, 16 bits
	- **오류 제어 (Error Control)** 를 위한 Checksum값을 저장
	- **데이터 무결성**을 보장하는 핵심 요소
		- TCP 통신의 모든 과정에서 사용됨
- Source 측 (TCP Segment 생성 시)
	- TCP packet을 만들 때, source 측이 checksum 을 계산
	- 해당 값을 checksum field에 삽입
	- 네트워크를 통해 전송
- Destination 측 (TCP Segment 수신 시)
	- Packet을 받은 후, Checksum 다시 계산
	- Source 측 Checksum과 비교하여 손상 여부 확인
		- 값이 다르다면 손상된 것으로 간주
		- *재전송 요청*
- 단순히 각 비트 (header + data) 값을 더하고 1의 보수를 취하는 방식
	- *모든 오류를 감지할 순 없음 (ex. 짝수 개의 비트가 변하는 경우)*
### 12. Port 란?
- Transport Layer 에서의 Port
	- 네트워크 서비스와 application 간 데이터를 주고받는 출입구 역할
	- 16비트 숫자로 표현되며, 범위는 0 ~ 65535
	- TCP, UDP 통신에서 특정 application을 식별하는데 사용됨
		- 모든 TCP 연결에는 source port & destination port 필요
		- 다양한 포트를 활용하여 여러 어플리케이션 프로토콜을 동시에 처리
- +) Application Layer에서는 ICMP처럼 포트를 사용하지 않는 프로토콜도 존재
- Port 번호 범위와 역할
	- 0
		- 예약된 port
		- 네트워크 트래픽에는 사용되지 않음
	- 1 ~ 1023
		- Well-Known Ports (시스템 포트)
		- 주요 네트워크 서비스 (ex. HTTP: 80, FTP: 21)
		- 어떤 운영체제도 시스템 포트를 임시 포트로 사용하지 않음
	- 1024 ~ 49151
		- Registered Ports (등록된 포트)
		- 특정 어플리케이션 및 서비스용 (ex. MySQL: 3306)
	- 49152 ~ 65535
		- Ephemeral Ports (임시 포트)
		- 클라이언트가 네트워크 연결을 위해 임시 사용
			- 클라이언트 - 서버 통신 시, 서버는 고정된 포트 사용 (예: 80)
		- 이 범위는 운영체제에 따라 다름

### 13. TCP에서 사용되는 시스템 포트 (Well-Known Port)의 종류
| PORT   | Service                                    | 설명                      | Protocol |
| ------ | ------------------------------------------ | ----------------------- | -------- |
| 20, 21 | FTP (File Transfer Protocol)               | 파일 전송 (데이터: 20, 제어: 21) | TCP      |
| 22     | SSH (Secure Shell)                         | 원격 접속 (보안)              | TCP      |
| 23     | Telnet                                     | 원격 접속 (보안 없음)           | TCP      |
| 25     | SMTP (Simple Mail Transfer Protocol)       | 이메일 발송                  | TCP      |
| 53     | DNS (Domain Name System)                   | 도메인 → IP 변환             | TCP/UDP  |
| 67, 68 | DHCP (Dynamic Host Configuration Protocol) | IP 주소 자동 할당             | UDP      |
| 80     | HTTP (Hyper Text Transfer Protocol)        | 웹사이트 접속 (보안 없음)         | TCP      |
| 110    | POP3 (Post Office Protocol v3)             | 이메일 수신 (완전 다운로드)        | TCP      |
| 143    | IMAP (Internet Message Access Protocol)    | 이메일 수신 (서버 유지)          | TCP      |
| 443    | HTTPS (HTTP Secure)                        | 보안 웹사이트 접속 (SSL/TLS)    | TCP      |

### 14. DNS Server의 목적은?
- DNS Server는 사용자가 입력한 웹사이트 주소를 컴퓨터가 이해할 수 있는 IP 주소로 변환
  **website 주소 -> IP 주소**
- 사용자는 복잡한 IP 주소를 기억할 필요 없이, 도메인 이름을 입력하여 웹사이트를 이용할 수 있다

### 15. TCP에서 Endpoint의 정의
- TCP Endpoint는 Client와 Server Process 간 TCP/IP 네트워크 연결을 쉽게  설정하고 통신할 수 있도록 하는 개념
- 전화 통신 모델과 유사
	- Client -> Server: Call 
	- Server: Answer
- 한 번 네트워크 연결이 설정되면, Client와 Server는 해당 연결을 통해 데이터를 주고 받으며 통신


### 16. TCP 에서의 오류 제어 (Error Control) 메커니즘 설명
- TCP Protocol은 손상/누락/순서 어긋/중복 segment를 감지할 수 있음 
- by 3가지 기술
	- Checksum 
		- 데이터가 손상되었는지 확인
	- Acknowledgement (응답 확인)
		- 데이터가 정상적으로 도착했는지 확인
	- Retransmission (재전송)
		- 오류가 발생하면 데이터 다시 전송

### 17. Congestion (혼잡) 이란?
- Network Layer에서 Message Traffic이 너무 많아져
  네트워크 응답 시간이 느려지는 상태
- TCP Congestion Control (혼잡 제어) 메커니즘
	- **Slow Start (느린 시작)**
		- window (송신 packet 수) 처음 시작할 땐 작게
		- 네트워크 상황에 따라 조절
	- **Congestion Avoidance (혼잡 회피)**
		- packet 전송 속도 조절
	- **Fast Rentransmit (빠른 재전송)**
		- 같은 packet에 대한 ACK 응답이 3번 중복되면, 손실로 판단 & 즉시 재전송
	- **Fast Recover (빠른 복구)**
		- Congestion이 감지되었을 때, 한 번에 window 크기를 줄이지 않고 점진적으로 감소

### 18. Stop-and-Wait vs Sliding Window protocol 비교
- Stop-and-Wait 와 Sliding Window 의 공통점
	- 기본적으로 *Flow Control* 와 *Error Control*을 위한 protocol
	- 여러 Network Layer에서 응용될 수 있음 (특히, *Transport Layer*, *Network Layer*)
- Stop-and-Wait
	- 한 번에 하나의 TCP segment/Frame를 전송. 수신 측의 ACK를 기다림
	(Transport Layer에선 TCP segment, Network Layer에선 Frame)
		- 송신 window size: 1
		- 수신 window size: 1
	- 한 번에 한 방향으로만 전송 가능 (Half-Duplex)
	- 낮은 효율
		- 비효율적이라 현재 TCP 에선 거의 사용되지 않음
- Sliding Window
	- 한 번에 여러 개의 TCP segment/Frame 전송. 손상되거나 유실된 것만 재전송. 최대 N개
	- ACK가 수신만큼 window-sliding 발생 -> 추가적인 TCP segment/Frame 전송 가능
		- 송신 window size: N (한 번에 최대 N개 전송 가능)
		- 수신 window size: 1~N
	- Frame Sorting 과정이 필요할 수도
	- 양방향 데이터 전송 가능 (Full-Duplex)
		- 데이터 전송 & ACK 수신 동시에 가능
	- 높은 효율
		- 현대 TCP에서도 핵심적으로 사용되는 Protocol

### 19. RTT (Round Trip Time) 란?
- 왕복 지연 시간, 네트워크 상태를 측정하는 중요한 지표
- Packet이 목적지로 전송된 후, 해당 packet에 대한 응답 (ACK) 가 다시 돌아오는데 걸리는 총 시간 (ms)
- 영향을 미치는 주요 요인
	- 거리 (Distance)
		- 요청이 서버에 도달하고, 응답이 돌아오는 데 걸리는 거리
		- 거리가 길수록 RTT 증가
	- 전송 매체 (Transmission Medium)
		- 신호를 전달하는 네트워크 매체 (광섬유, 구리선, 무선)
		- 빠른 전송 매체를 사용할수록 RTT 감소
	- 네트워크 홉 (Network Hops)
		- 신호가 여러 장치 (router, switch) 를 거쳐 전달
		- Hop이 많을수록 RTT 증가
	- 트래픽 수준 (Traffic Levels)
		- 네트워크 트래픽이 많으면 packet 대기량 높음 > RTT 증가
	- 서버 응답 시간 (Server Response Time)
		- 서버 요청 처리 시간
- RTT 는 대략적인 값이며, 네트워크 경로와 혼잡도에 따라 값이 변동될 수 있음
- RTT 감소 방법
	- CDN (Condent Delivery Network) 사용
		- 여러 서버로 구성된 네트워크, 특정 웹사이트의 콘텐츠를 각 서버에 복사하여 저장
		- RTT를 줄이는 방식
			- PoP
			- Web Caching
			- Load Distribution
			- Scalability
			- Tier 1 Network Access

### 20. TCP의 ACK 가 중요한 이유
- host의 성공적인 packet 수신을 확인 (acknowledge)하기 위해
- ACK flag가 설정되어 있을 경우, 해당 TCP header의 'Acknowledgment number' field에 유효한 번호가 포함되어 있다는 의미

### 21. 재전송 (Retransmission) 이란?
- TCP 에서 Retransmission이란, 손실되었거나 손상된 packet을 network를 통해 다시 보내는 것
- Retransmission: TCP 같은 protocol이 신뢰성 있는 통신을 제공하기 위해 사용하는 메커니즘
	- *신뢰성 있는 통신* : data packet이 정확하게 전달됨을 보장
- **TCP: ACK + Retransmission**
	- **-> 신뢰성 보장**


### 22. RTT 예측 계산 연습 문제
- 현재 RTT가 30 ms 이고, 이후에 수신된 ACK가 각각 26ms, 32ms, 24ms에 도착했다면, 새로운 RTT 추정치는? (α = 0.9 사용)
- The formula for estimate new RTT : ```newRTT=αRTT+(1−α)arrivalRTT```
```text
newRTT1 = 0.9 * (30 ms) + 0.1 * (26 ms) = 29.6 ms
newRTT2 = 0.9 * (29.6 ms) + 0.1 * (32 ms) = 29.84 ms
newRTT3 = 0.9 * (29.84 ms) + 0.1 * (24 ms) = 29.256 ms
```
- 주어진 RTT 예측 공식은, 
```newRTT=α * (oldRTT) + (1−α)arrivalRTT```
한 번의 ACK 도착 시간을 기준으로, 새로운 RTT를 순차적으로 update 하는 방식
- RTT는 계속 갱신해 나가는 동적인 값. ACK마다 한 번씩 계산이 반복 됨


### 23. TCP의 주요 특징
- Connection-oriented (연결 지향)
	- destination과 connection(연결)을 설정한 후, data 전송
- Stream Data Transfer (스트림 기반 데이터 전송)
	- Byte Stream을 Packet(TCP Segment) 로 나누어 IP 계층에 전달
- Reliable (신뢰성 보장)
	- data 손상/중복/손실 된 경우, 복구
- Point to Point
	- End-to-End data 전달을 보장
		- 송신자 - 수신자 사이 신뢰성 있는 data 전달 보장
		- UDP는 End-to-End 보장 X (Best-effort delivery)
- Interoperability (상호운용성)
	- 플랫폼 간 호환성 문제를 제거하여 다양한 system 간 통신 가능
		- OS, Hardware, Programming Language 관계 없이 통신 가능
		- 표준화된 protocol 이기 때문에 가능
		- UDP도 기본적인 상호운용성은 있으나, TCP만큼 엄격한 순서 보장, 흐름 제어, 에러 복구 등을 제공하지 않기 떄문에 복잡한 상호운용성 요구 상황에는 TCP 가 더 적합
- Error and Flow Control
	- 오류 검사, 흐름 제어, ACK 기능 제공
- Name Resolution (이름 해석)
	- Domain > IP address 변환하는데 도움을 줌
	- TCP가 직접 처리하는 기능은 아니지만, TCP 통신을 시작하기 전에 필요한 단계이기 때문에 TCP 특징 목록에도 포함되는 경우가 있음
- Routability
	- TCP/IP는 routing이 가능한 protocol
	- IP를 기준으로 경로를 결정할 수 있음
- Full Duplex
	- 양방향으로 동시에 data를 전송하고 수신할 수 있는 기능 제공

### 24. SCTP Protocol 이란?
- Stream Control Transmission Protocl (스트림 제어 전송 프로토콜)
- Connection-oriented 방식 (연결 지향)
	- Endpoint 연결이 설정되면, 동시에 여러 개의 data stream을 전송할 수 있는 full-duplex 통신 제공
	- Next-generation TCP
- 인터넷 상 전화 통화 지원하기에 용이하도록 설계됨
- 무선 네트워크에서의 연결 설정이나 멀티미디어 데이터 전송관리에도 적합하도록 설계
- 일반적인 인터넷 통신에서는 여전히 TCP/UDP 가 주

### 25. Leaky Bucket 과 Token Bucket 의 차이 (Traffic Control Algorithms)
- 공통점
| Leaky / Token Bucket |
| -- |
| 혼잡 발생 전 예방 |
| 주로 *송신자*에서 미리 조절 |
| 전송량을 정해진 규칙에 따라 제한 |


- Leaky Bucket
	- 일정한 속도로만 data (packet) 을 전송
	- 갑자기 traffic이 몰려도 내부 buffer(=Bucket =Queue)에 쌓아두고 똑같은 속도로 처리
		- 너무 많이 몰리면 *buffer overflow*, **Packet Drop** 발생
	- **물이 일정한 속도로 새는 구멍 난 양동이와 같은 개념**
		- 많은 data가 들어와도 *내보내는 속도는 항상 일정*
		- 단, data가 너무 많이 들어오면 양동이가 넘쳐서 data 손실됨 (Packet Drop)
			- *Burst는 안 됨*
	- 효과
		- 네트워크 입장에선 항상 예측 가능한 traffic을 받을 수 있게 됨 
		- 혼잡/traffic을 control 한다기보단 단순 shaping

- Token Bucket
	- 일정한 시간마다 *Token* 발급, Packet 전송할 때마다 Token 사용
		- 별도의 요청이 없는 동안에는 token 축적...
	- Token이 있으면 전송 가능, 없으면 대기
	- 토큰이 여러 개 쌓이면 Burst(한꺼번에 여러 개 전송) 가능
		- Token은 일정한 비율로만 생성되므로, 장기적으로는 평균 속도 제한 존재
	- API 요청 제한, DDoS 방어, ... 유연한 rate control

### 26. 3-Way Handshaking Protocol

```markdown
### 1단계 (SYN)
- ```Client ➡️ Server```
- Client가 Server와 연결을 시작하고자 할 때 SYN (Synchronize Sequence Number) Flag가 설정된 Segment를 보냄
	- 해당 segment는 통신을 시작하려는 의사와 함께 처음으로 사용할 Sequence Number를 서버에게 알림

### 2단계 (SYN + ACK)
- ```Client ⬅️ Server```
- Server는 Client의 요청에 응답하여 SYN과 ACK flag가 설정된 Segment를 보냄
	- SYN: Server도 통신을 시작할 준비가 되었다 & 자신이 사용할 Sequence Number를 Client에게 전달
	- ACK: Client가 보낸 Segment 잘 받았음

### 3단계 (ACK)
- ```Client ➡️ Server```
- Server가 보낸 Segment 잘 받았음
- Client와 Server간 신뢰할 수 있는 연결 성립
- 이제 실제 data 전송 시작 가능
```

### 27. Connection-Oriented Service 와 Connection-less Service 비교

| Connection-Oriented Service | Connection-less Service | 설명 |
| -- | -- | -- |
| 전화 시스템과 유사 | 우편 시스템과 유사 | 연결 후 통신하는 전화 ↔️ 주소만 쓰고 보내는 우편 |
| 오래 지속되고 안정적인 통신에 적합 | 갑작스럽고 짧은 통신에 적합 | Streaming ↔️ 간헐적 요청 (예: DNS) |
| 통신 전, 연결 설정이 필요 | 연결 설정 필요 없음 | TCP: 연결 필요 ↔️ UDP: 연결 X |
| 혼잡 발생 가능성 낮음 | 혼잡 발생 가능성 있음 | TCP: 흐름/혼잡 제어 포함 ↔️ UDP: X |
| 신뢰성 보장 | 신뢰성 보장 X | TCP: 손실 시 재전송 보장 ↔️ UDP: X |
| 모든 Packet이 동일 경로를 따름 | Packet 마다 경로가 다를 수 있음 | TCP: 순서 보장 ↔️ UDP: 순서 불일치 가능 |
| 높은 대역폭 요구 | 낮은 대역폭으로도 충분 | TCP: 추가 제어 정보 많음 ↔️ UDP: 간단 |

### 28. TCP 연결의 단계는 무엇인가?
- TCP에서 Connection-Oriented Transmission (연결 지향 전송)은 3 단계를 거침
1. 연결 설정 (Connection Establishment)
2. 데이터 전송 (Data Transfer)
3. 연결 종료 (Connection Termination)

### 29. TCP Sliding window의 기능은?
- variable-size window (가변 크기 윈도우) 를 활용하여 Flow control (흐름 제어) 와 Reliable Transfer (신뢰성 있는 데이터 전송) 모두 제공
- full-duplex, 각 연결에서는 양방향으로 동시에 데이터 전송 가능
- 서로 속도나 처리 능력이 다른 장치들 간 원할한 통신 지원

### 30. TCP Header의 최대/최소 크기
- **20 bytes ~ 60 bytes**
- 최소한의 정보 (Header 필수 요소)

	| 항목 | bit | bytes |
	| -- | -- | -- |
	| Source Port Number | 16 | 2 |
	| Destination Port Number | 16 | 2 |
	| Sequence Number | 32 | 4 |
	| Acknowledgment Number | 32 | 4 |
	| Data offset, Reserved, Flags | 16 | 2 |
	| Window Size | 16 | 2 |
	| Check sum | 16 | 2 |
	| Urgent | 16 | 2 |
	| Total | 160 | 20 |

- 추가 옵션 필드

	| 항목 | bytes |
	| -- | -- | 
	| Option  + Padding | 최대 40 bytes |

### 31. Port Number 는 고유해야 할까? Port Number 는 왜 IP Address 보다 짧을까?
- `TCP Header 내 Port Number: 16 bit < IPv4: 32 bit`
- Port Number는 통신하는 protocol을 구분하기 위한 것
	- client와 server가 통신할 때 어떤 service (process) 에 연결할지 지정
- 통신 protocol 종류 (Port Number 수) 가 컴퓨터 시스템 수 (IP Address) 보다 훨씬 적기 때문에 더 짧음
- 추가 질문: 임의의 포트(Ephemeral Port) 도 고유할까?
	- TCP 통신 과정을 보면 sender가 초기 요청을 보낼 때 OS가 자신의 Port Number를 임의로 배정해줌
		- OS는 현재 system 내에서 사용 중이지 않은 Port Number 중 하나를 임의로 선택해서 할당
			- 이 선택 또한 해당 system 내에서 일시적으로 고유한 값
- 결론:
	- **Port Number가 고유하다** = `해당 연결, 해당 시간, 해당 시스템에서 식별자로서 충돌이 없도록 관리된다`

### 32. UDP와 IP 는 모두 신뢰성이 없을까? 그 이유는?

| 항목 | UDP | IP |
| -- | -- | -- |
| 신뢰성 | 없음 | 상위 protocol에 의존 |
| 무결성 | 

- 신뢰성: "데이터가 도착하는가?"
- 무결성: "도착한 데이터가 변형 없이 정확한가?"

### 33. Datagram이란?
- Network 상에서 정보를 전송하기 위한 논리적 단위
	- UDP 에서 전송되는 데이터 단위
	- (TCP에서의 데이터 단위: Segment)
- `2 Header Sections + Data Payload` 로 구성
- 출발지 > 목적지까지 전송
	- TCP와 달리 사전에 연결 과정(3-Way Handshake) 없음
	- 신뢰성 x
	- 무결성 x

### 34. Registered Port 와 Dynamic Port란?

| Port 종류 | 범위 | 관리 주체 | 특징 |
| -- | -- | -- | -- |
| Well-Knwon | 0 ~ 1023 | IANA 가 할당, 제어, 등록 모두 관리 | HTTP(80), HTTPS(443), ... 등과 같이 널리 알려진 서비스가 사용 |
| Registered | 1024 ~ 49151 | IANA가 등록만 관리 | 중복 방지를 위한 등록 |
| Dynamic (Private/Ephemeral) | 49152 ~ 65535 | 완전 자유 사용 | OS 나 Application이 임시 Port로 자동 할당. 등록, 제어 X | 

### 35. TTL (Time To Live) 필드의 중요성은?
- 전송되는 data의 수명/유효 시간을 의미
	- 해당 시간이 지나면 데이터는 폐기됨 (TTL = 0 이면 폐기)
- Packet이 Network 상에서 **허용된 최대 Hop 수**를 나타낸다고도 볼 수 있음
- **전송 불가능한 Datagram이 Network 상에서 무한히 순환하는 상황을 방지**

### 36. IPv4 패킷이 도착했는데, 처음 8비트가 `01000010`이다. 수신자는 이 패킷을 폐기한다. 그 이유는?
- 처음 4비트: IP version
	- `0100`: IPv4
	- `0110`: IPv6
- 다음 4 비트: 헤더 길이 (단위: 4 bytes) (IPv4만 헤더 길이를 비트로 명시)
	- IPv4의 최소 헤더 길이: 20 bytes
	- `0010` 은 헤더 길이가 2 * 4 bytes = 8 bytes 임을 의미함
	- 최소 길이를 만족하지 않으므로, 수신자는 해당 패킷을 유효하지 않다고 판단하고 폐기함

### 37. IPV4 패킷에서 HLEN 값이 2진수로 `1000` 이다. 몇 bytes의 옵션이 포함되어 있나?
- HLEN: 전체 헤더 길이 (필수 필드 (20 bytes) + 옵션 필드), 최대 60 bytes
- HLEN: `1000` 이라면 전체 헤더 길이: 4 bytes * 8 = 32 bytes
	- 옵션 필드: 32 bytes (전체 헤더 길이) - 20 bytes (필수 필드) = 12 bytes

### 38. Open-loop 혼잡 제어, Closed-loop 혼잡 제어 기술은 무엇인가?
- Open-loop 혼잡 제어
	- 혼잡을 예방하기 위한 제어 정책 (사전에 설계된 정책)
	- source, destination 양측에서 제어됨
		- 예: Packet 간 생성 간격 유지, 데이터 흐름 제어, 정적 라우팅 등
- Closed-loop 혼잡 제어
	- 혼잡이 발생한 이후에 이를 감지하고 제어하는 방식
	- 예: TCP 혼잡 제어 알고리즘, ICMP 메시지를 통한 알림 등

### 39. IPv4 Header 에서 Router를 통과할 때마다 변경되는 필드는?
- Header Checksum
	- 역할: IPv4 Header의 오류 검출용 값
	- 변경되는 이유
		- Router 는 IP Header의 일부 필드 (예: TTL) 등을 직접 수정하기 때문에, Header 전체의 Checksum도 다시 계산해야 함
	- Router 를 거칠 때마다 반드시 변경됨
- TTL 
	- 역할: Packet에 수명을 할당하여 루프 방지
	- 변경되는 이유
		- Router를 지날 때마다 1씩 감소
- Total Length
	- 역할: IP Header + data 전체 길이
	- 변경되는 이유
		- packet fragmentation이 일어날 경우, router가 수정할 수 있음
	- 반드시 바뀌는 것은 아님

### 40. 방화벽이란?
- 컴퓨터나 네트워크로 들어오거나 나가는 data를 제어하는 보안 시스템
- 일종의 '벽'처럼 작동
	- 미리 정의된 규칙을 충족하지 않는 위험한 traffic 차단
	- 안전한 데이터만 통과하도록 허용

### 41. NAT (Network Address Translation) 의 개념 설명
- NAT는 Router가 Private Network와 Internet 사이를 오가는 data의 IP Address를 변환하는 과정
- 기기가 data를 전송하면, Router는 해당 기기의 Private IP Address 를 Public IP Address로 변경
- data가 다시 돌아오면, Router는 이를 올바른 기기로 다시 전달
	- 하나의 Public IP 로 여러 Private IP의 인터넷 접근을 가능하게 해주는 기술
	- Private Network -> Public Network 간 주소 변환

### 42. 한 호스트가 다른 호스트에게 100개의 datagram을 전송한다. 만약 첫 번째 diagram의 identification number이 1024라면, 마지막 datagram의 identification number은? (IPv4 에서) 
- IPv4의 Identification field는 각 datagram (packet)을 구분하기 위해 사용
- 각 datagram 마다 id 가 1씩 증가
- datagram의 id는 fragmentation 시에도 사용됨. 따라서 같은 datagram 조각 구별 가능
- 1024 + 99 = 1123

### 43. IPv6 Header 에 Checksum이 없는 이유는?
- checksum 기능이 상위 계층 protocol에서 제공됨
- IP 계층에서 필요하지 않기 때문에 제거됨 (중복 및 성능 저하 우려)

### 44. IPv4 에서 IPv6으로 전환할 때 사용되는 3가지 전략
- Dual-stack (이중 스택)
	- 하나의 장비 (예: Router, Server) 가 IPv4와 IPv6 프로토콜을 동시에 지원하여 두 가지 네트워크와 통신할 수 있도록 지원
- Tunneling (터널링)
	- IPv6 packet을 IPv4 packet 안에 캡슐화해서 IPv4 network를 통해 전달
	- IPv4 networkf를 tunnel처럼 사용해 IPv6 데이터를 전송
- Header Translation (헤더 변환)
	- IPv4, IPv6 header를 서로 변환하여 양쪽 protocol 간의 호환성 확보
	- 주로 NAT64, SIIT 같은 기술이 포함됨

### 45. Tunneling이란?
- 같은 유형의 source network와 destination network를, 다른 유형의 network를 통해 연결해야할 때 사용되는 Internetworking 기술
- IPv6 간 통신을 위해 IPv4 network를 'tunnel' 처럼 통과

### 46. ARP protocol 의 목적은?
- ARP: Address Resolution Protocol
	- IP 주소만 알고 있을 때 해당 장치의 물리적 주소(MAC Address) 를 알아내기 위해 사용
- 동일 network 내 (local network) data를 보내고자 할 때, ARP 는 destination 장치의 IP Address를 MAC Address 로 변환
- MAC Address 는 Ethernet Frame을 해당 장치에 직접 전송하는데 사용됨
- **같은 network 내에서 장치 간 data 전송을 위해 반드시 필요한 절차**

### 47. Fragmentation 이란?
- Network Layer에서 수행되는 기능
- Gateway 가 큰 packet을 더 작은 단위인 fragment로 나누는 기술
	- 각 fragment는 독립된 내부 packet으로 전송되며, 각각의 packet은 자체적인 header 와 trailer를 가지고 있음
- 하나의 datagram은 최종 destination에 도달하기 전까지 여러 번 fragmentation 이 수행될 수 있음
- fragment들을 다시 재조립(reassembly) 하는 것은 어려운 작업
	- fragment들이 독립적인 datagram으로 취급됨
	- reassembly는 최종 destination에 도달한 host가 담당

### 48. 10Mbps Ethernet에서 48-bit Jamming Signal을 사용할 떄, Round Trip Propagation Delay 시간이 46.4ms일 때, frame의 최소 크기는?
- frame은 전파 지연보다 충분히 길어야 충돌을 감지할 수 있음 (최소 frame 크기가 필요한 이유)
- 전송 속도: 10Mbps
- Round Trip Propagation Delay (왕복 전파 지연 시간): 46.6ms
- 최소 프레임의 크기 = 전파 지연 시간 x 전송 속도
	= 46.4 x 10^-3 x 10 x 10 Mbps = 46.4 x 10^4 bit = 464 Kbit


### 49. TCP Congestion Control 의 slow start 단계에서 Congestion Window (cwnd) 의 크기는 어떻게 되는가?
- TCP는 network에 너무 많은 packet을 한꺼번에 보내 Congestion을 일으키지 않기 위해 Congestion Control을 수행
- 초기에는 network의 용량을 모르기 때문에 점진적으로 알아가는 방식이 필요
- Slow 단계에서는 Congestion Window의 크기가 지수적으로 증가
- 일정 threshold에 도달하면 Congestion Avoidance 단계로 넘어가며 선형 증가로 바뀜


### 50. n-bit Frame Sequence Number를 사용하는 Selective Reject Protocol의 최대 Window 크기는?
- Selective Reject는 신뢰성 있는 data 전송을 위한 ARQ(Automatic Repeat reQuest) 방식 중 하나
- 송신자는 여러 frame을 보낼 수 있고, 수신자는 오류난 frame만 재전송 요청
- n-bit frame sequence Number => 사용가능한 번호는 총 2^n 개
- Selective Reject는 수신 측에서 중복되는 번호 구분이 어려워서, 2^(n-1)을 넘을 수 없음
- 2^(n - 1)
