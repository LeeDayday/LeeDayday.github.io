---
title: "DNS 이해하기"

categories:
  - networking
tags: [networking, dns]

date: 2025-06-25
last_modified_at: 2025-06-25
---

## DNS란 무엇인가?

### 정의 및 목적
- Domain Name System
- 사라임이 이해하기 쉬운 Domain Name을, 컴퓨터가 이해할 수 있는 IP Address 로 변환해주는 시스템

- 예:
> 사용자가 브라우저에 www.google.com을 입력하면,
> DNS 는 이를 실제 서버 주소인 `xxx.xxx.xxx.xx` 같은 IP 로 변환해줌

### DNS의 필요성

| 문제 | 해결 |
| 사람은 IP 주소를 외우기 어렵다 | `naver.com` 같은 domain을 사용 |
| 컴퓨터는 domain을 이해 못함 | DNS가 Domain 을 IP로 변환 |

- DNS는 사람 ↔️ 인터넷 사이의 주소 번역자 같은 느낌

## DNS의 작동 원리

### DNS 질의 과정 (Domain > IP Address 찾는 흐름)

0. 사용자 브라우저 접속
- 사용자가 브라우저 주소창에 `www.google.com`을 입력

1. Browser DNS Cache 확인
- (예전에 이 주소를 방문한 적이 있던가? Browser가 저장해둔 기록을 확인하는 과정)
- 일부 Browser는 자체 DNS Cache를 가질 수 있음
- 가장 최근에 방문한 IP 주소가 있으면 그대로 사용
  - 크롬의 경우, `chrome://net-internals/#dns` 에서 현재 browser가 저장하고 있는 DNS Record 확인 가능
- 없으면 Browser에게 OS Resolver에 질의 (2단계로 진행 🚀)

2.  OS Resolver의 질의
- (예전에 이 주소를 방문한 적이 있던가? OS가 저장해둔 기록을 확인하는 과정)
- OS Resolver는 아래 순서로 진행 
  - `hosts` 파일 확인
    - `hosts` 파일은 Domain 과 IP Address 를 수동으로 mapping 해둔 text file
      - mac의 경우, `/etc/hosts` 에 저장됨
    - DNS Cache 검색보다 우선순위 높음
  - OS DNS Cache 확인
    - OS Memory에 동적으로 저장되는 cache (자동 생성/삭제)
      - 삭제는 TTL을 기준으로
      - 최근에 질의한 결과를 임시로 메모리에 저장해 재사용
    - RAM, OS Resolver 내부에 저장됨
  - 없으면 Local DNS Resolver 에 질의 (3단계로 진행 🚀)

3. Local DNS Resolver (Recursive Resolver) 의 질의
- (browser와 os resolver가 찾지 못한 이름을 대신 찾아주는 외부 DNS Server)
- 보통 ISP나 회사 네트워크에서 제공. 다수의 사용자가 공유
- 주요 역할: 권한 있는 DNS Server 까지 찾아가서 최종 IP 받아오기
  (Domain을 구성하는 각 계층을 따라 반복 질의 수행)

- Local DNS Resolver 는 아래 순서로 진행
  - 자체 Cache 확인
    - Local DNS Resolver 는 대규모 사용자 요청을 처리하므로, 큰 Cache DB를 유지
    - 자체 Cache로 반복 질의 횟수르 최소화
    - 이전에 같은 domain에 대한 기록이 있으면 바로 IP 반환 🔚
  - Root Name Server에 질의
    - Root Name Server는 `.`(dot) 영역의 최상위 서버
    - TLD Name Server의 위치를 알려줌
    - 직접적으로 IP Address는 알려주지 않음
  - TLD Name Server에 질의
    - TLD Namer Server는 `.com`, `.org` 등 도메인 확장자(TLD) 에 해당하는 Name Server
    - Authoritative Server의 위치를 알려줌
  - Authoritative Server에 질의
    - 실제 IP Address (A Record, AAAA Record 등)를 보유한 DNS Server

4. 최종 처리
  - Local DNS Resolver는 받은 IP Address를
    - 자신의 Cache에 저장
    - OS Resolver 에게 응답 전달
    - 최종적으로 Browser 에 전달되어 웹 페이지 요청 진행

### Resolver

#### 단어 뜻
- resolve: 해결하다, 해석하다, (문제나 의문을 풀다)
- DNS 에서 **"resolver"란, Domain Name을 IP Address로 해석해주는 주체**를 뜻함

#### 역할
- Domain Name 을 IP Address 로 변환하는 전체 과정을 담당하는 컴포넌트
- 보통 2 종류로 나뉨

| 종류 | 이름 | 역할 |
| -- | -- | -- |
| Stub Resolver | OS Resolver | browser/application 대신 DNS 질의 시작 (hosts, cache 확인) |
| Recursive Resolver | Local DNS Resolver | Stub Resolver 를 대신 `Root > TLD > Authoritative DNS` 까지 질의 수행, 결과 caching |

- Stub은 요청자, Recursive는 발발 돌아다니며 답을 찾아오는 해결사 느낌

### URL
- Domain Name 은 사실 맨 끝에 '.'이 생략되어 있음 (이 '.'이 root domain)
  - 예를 들어 `www.example.com.` 처럼 원래 끝에 '.' 이 있음
    - `www`: 서브 도메인
    - `example`: 도메인 이름
    - `com`: TLD (Top Level Domain)
    - `.`: 최상위 루트


### 재귀 질의 vs 반복 질의

#### 재귀 질의
- 사용자 (혹은 Stub Resolver) 가 Local DNS Resolver에게 단 1번만 요청
- 이후에는 Local DNS Resolver가 모든 과정을 수행
  - DNS 에서 "재귀 질의"란, client가 한 번 질의하면, 그 다음 모든 질의 과정은 Local DNS Resolver가 책임지고 처리한다는 의미의 책임 위임(Recursive Responsibility)
- 사용자에게는 최종 결과만 전달됨

#### 반복 질의
- Root Name Server, TLD Name Server 는 직접 IP Address를 알려주지 않고, 다음 단계의 Server 위치만 알려줌<br>Authoritative Server는 해당 domaindml IP Address (A/AAAA Record 등)를 최종적으로 응답
- 질의자는 각 단계를 직접 따라가며 반복 질의
  - Local DNS Resolver가 수행

### Root Name Server > TLD Name Server > Authoritative Name Server 구조

- Root Name Server
  - 인터넷 DNS 계층의 최상위
  - `.` 영역을 담당 (`www.example.com*.*`)
  - 전 세계에 약 13개의 root server (A~M)가 있고, 수백 개의 거울 서버로 분산 운영됨
  - 역할
    - 질의 받은 domain의 TLD(예: `.com`, `.net`, 등)에 해당하는 TLD Name Server의 위치를 알려줌
- TLD Name Server (Top Level Domain)
  - TLD(domain 확장자) 별로 구분된 DNS 서버
    - 각 domain 종류에 따라 운영 주체가 다름
  - 역할
    - 해당 domain의 Authoritative Name Server 위치 (NS Record)를 알려줌
- Authoritative Name Server 
  - 특정 domain의 정확한 IP 정보(A/AAAA Record 등)를 가지고 있는 서버
  - 실제로 해당 domain을 운영하는 조직/기관이 관리함
  - 역할
    - `www.example.com`에 대한 최종 IP Address를 응답
    - A/AAAA Record, MX Record, CNAME 등 보유 
    
## DNS 구성 요소

### A, AAAA, CNAME, MX, NS, TXT Record

- DNS 는 단순히 Domain -> IP Adress를 찾는 것뿐만 아니라, 다양한 정보를 질의할 수 있도록 여러 종류의 Record 타입을 제공
- A (Address) Record
  - `Domain Name -> IPv4 (32 bit)` 를 매핑
  - 가장 기본적이고 널리 사용되는 Record
  - Web browser 가 IP Address를 얻기 위해 가정 먼저 조회함
- AAAA (Quad A) Record
  - `Domain Name -> IPv6 (128 bit)` 를 매핑
  - IPv6를 사용하는 경우, A Record 대신 사용됨
- CNAME (Canonical Name) Record
  - 하나의 Domain Name을 다른 Domain Name (alias) 로 연결
  - 실제 IP Address를 갖고 있지 않음
    - 대신 연결된 domain의 A/AAAA Record를 따라감
  > 예시
  - → www.example.com은 example.com을 가리키며, 실제 IP는 A 레코드로 얻음
- MX (Mail Exchange) Record
  - Domain에 대한 Mail Server Address 정의
  - 이메일 전송 시 어떤 Server로 메일을 보낼지 결정
- NS (Name Server) Record
  - 해당 Domain을 관리하는 Authoritative DNS Server의 Domain Name을 지정
  - Root, TLD, Authoritative Server 단계에서 자주 등장
- TXT (Text) Record
  - Domain에 대한 text 정보를 담는 record
  - SPF, DKIM, Domain 소유 증명 등에 사용


### 권한 DNS 서버, 캐시 DNS 서버의 역할

- Authoritative DNS Server (권한 DNS Server)
  > **정답을 보유한 출처**
  > Domain Name에 대한 최종 IP 정보 (A/AAAA 등) 을 가지고 있는 DNS Server
  - 역할
    - 특정 Domain에 대한 정확한 Record를 저장하고 응답
    - 자신이 관리하는 Domain만 응답 가능
    - DNS Query 흐름의 마지막 단계에서 응답
  - 실제 운영 주체
    - 웹사이트 소유자, 호스팅 업체, 클라우드 DNS Service
    - 예: AWS Route 53, Cloudflare, ...

- Cache DNS Server
  > 예전에 찾은 정보를 임시로 저장해두는 저장소
  > 재사용 가능한 DNS 응답을 임시로 저장(Cache) 해두고 빠르게 응답
- 역할
  - 이전에 질의했던 Domain의 결과를 저장해 재사용 (TTL 기반)
  - 저장된 Cache가 유효하다면, 다시 Root > TLD > Authoritative DNS Server 를 거치지 않아도 됨
- 실제 운영 주체
  - 대부분 ISP, 조직 내부 DNS Server가 Cache Server 로 동작

## DNS 캐싱과 TTL

### Cache가 중요한 이유
- 반복된 질의를 줄여 인터넷 traffic과 응답 시간 절감
- Root/TLD 서버 부하 감소
- 효율적인 Network 자원 사용
  - **DNS Query 는 계층적으로 오래 걸릴 수 있어서 Cache가 핵심 성능 요소**

### 캐싱 동작 원리

- DNS 질의 결과는 다음 요청에서도 재사용될 수 있도록 여러 계층에 걸쳐 Caching 됨

#### Cache가 저장되는 위치 (Query 흐름 순서대로)

| 위치 | 설명 | 예시 | 
| -- | -- | -- |
| Browser Cache | Browser 가 자체적으로 DNS 응답 저장 | 최근 방문한 사이트 |
| OS DNS Cache | OS가 보관하는 DNS 응답 기록 | `/etc/hosts` 확인 후 저장|
| Local DNS Resolver Cache | ISP나 회사 내부 DNS Server가 저장 | 많은 사용자들의 요청 처리|
| Authritative DNS Server Cache (경우에 따라) | 일부 DNS 서비스가 역방향 조회나 다른 zone 위한 Cache 보유| (선택적) |

#### Cache의 저장 방식

- 최초 질의 시, 상위 DNS Server로부터 IP Address 받아옴
- 응답과 함께 TTL 값도 함께 전달됨
- 해당 TTL 동안 Cache에 저장됨
- 같은 domain 요청이 들어오면 cache 에서 바로 응답

#### DNS Caching 흐름 예시

```markdown
1. 사용자가 www.example.com 접속 시도
2. Browser > OS > Local DNS Server 순서로 Cache 확인
3. Cache가 없으면 계층 질의 수행 (Root > TLD > Authritative)
4. IP Address 획득 + TTL 정보 수신
5. 각 단계별로 결과를 Cache에 저장
6. 이후 동일한 요청 시, Cache 에서 바로 응답
```
### TTL (Time To Live) 개념

- DNS 응답 데이터가 Cache에 머물 수 있는 유효 시간
- TTL 이 지나면 해당 Cache는 만료되어 삭제되고, 다시 DNS 질의를 해야 함

#### TTL 이 왜 필요한가?

- 오래된 DNS 정보 사용 방지 (IP Address 변경 시 반영 필요)
- 지나친 질의 발생 억제
- Cache 효율 조절 수단
  - TTL이 너무 길면 > 최신 정보 반영 어려움
  - TLL이 너무 짧으면 > 트래픽 증가

#### TTL은 어디서 지정되는가?

- Authoritative DNS Server 가 응답시 TTL 값을 포함시킴
- A, AAAA, CNAME 등 모든 Record 유형에 TTL이 있음

#### Domain IP 변경 후 반영이 지연되는 이유

- Domain의 IP Address를 변경해도, 사용자 환경 (Browser, OS, Local DNS server 등) 에 기존 DNS 정보가 TTL 만큼 Cache 되어 남아 있을 수 있음
- 이로 인해 변경 사항이 즉시 반영되지 않고, 일정 시간 동안 이전 IP Address로 접속되는 현상 발생
  - TTL 이 만료되어야 새 DNS 정보가 반영됨


## DNS와 보안 이슈

### DNS Spoofing, Cache Poisoning

- DNS Spoofing
  - 사용자가 특정 Domain에 접속하려 할 때, 공격자가 위조된 IP Address를 응답하는 공격
  - 사용자는 실제 사이트가 아닌 가짜 피싱 페이지에 접속하게 됨
    - 조건 1: Cache에 해당 Domain의 IP 정보가 없어 DNS Query가 발생해야 함
    - 조건 2: 공격자는 DNS Query가 전송된 것을 감지 및 예상하여 피해자에게 위조된 응답 패킷을 실제 응답 패킷보다 먼저 보냄
    - 추가 피해: 잘못된 IP가 Cache에 저장됨

- Cache Poisoning
  - DNS Server Cache에 악의적인 정보를 심어, 다수 사용자에게 잘못된 IP 정보를 전달
  - Spoofing을 대규모로 확산시킬 수 있는 방식

### DNS over HTTPS (DoH), DNSSEC 개요

- DNS over HTTPS (DoH)
  - DNS Query를 일반 text가 아닌 HTTPS 로 암호화
  - ISP나 Hacker가 질의 내용을 가로채거나 조작하지 못하도록 보호
  - for 내용 보호
- DNSSEC (DNS Security Extensions)
  - DNS 응답에 디지털 서명을 붙여 위변조 방지
  - 사용자는 받은 DNS 정보가 신뢰할 수 있는 출처인지 검증 가능
  - for 정당성 검증

---- 

## DNS 가 없다면?

- 사용자는 웹사이트 접속 시 domain 대신 IP Address를 외워야 함
- 이메일 송수신, 소프트웨어 업데이트 등 대부분의 인터넷 서비스 정상 동작 불가
  - 컴퓨터는 IP로 통신하지만, 대부분의 응용 프로그램은 Domain을 기반으로 동작
    - **대부분의 소프트웨어는 Domain을 통해 통신. 이를 IP로 바꿔주는 게 DNS**
  - IP Address는 고정되어 있지 않음
    - cloud 환경, Load Balancing, CDN 사용 시 하나의 Domain이 여러 IP로 Mapping 됨
    - 장애 발생 시, IP를 다른 서버로 바꾸기도 함
      - **DNS가 Domain ↔️ IP mapping을 관리해주는 핵심 장치**
