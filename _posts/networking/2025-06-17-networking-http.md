---
title: "HTTP 에 대하여"

categories:
  - networking
tags: [networking, http]

date: 2025-06-17
last_modified_at: 2025-06-17
---

## 1. HTTP란 무엇인가?

### HTTP의 기본 개념
- HTTP (HyperText Transfer Protocol)
- web 에서 client와 server가 data를 주고받기 위해 사용하는 request/response 기반의 protocol
- `예: 크롬 브라우저로 naver.com 접속 과정`
    - 브라우저가 `naver.com` server에 페이지를 달라고 **요청**
    - server 는 HTML, CSS, JS 등을 보내줌 (**응답**)

### HTTP의 주요 특징
- 비연결성(Stateless)
    - 한번 request & response 연결이 끊어짐
- text 기반
    - 사람이 읽을 수 있는 ASCII text 형식
- 요청 방식
    - GET, POST, PUT, DELETE, ...

## 2. HTTP/2.0의 등장 배경

### 왜 HTTP/2.0이 필요했는가?
- HTTP/1.1은 오랫동안 표준 protocol로 사용되었지만, web 환경이 발전하면서 성능 문제가 드러남

- 문제 1: 연결 수 제한과 bottleneck
    - HTTP/1.1에서는 하나의 TCP 연결로 한 번에 하나의 요청만 처리 가능
    - 그래서 browser는 resource를 병렬로 불러오기 위해 여러 TCP 연결을 강제로 열었음
    - server, client 모두 불필요한 resource 낭비
- 문제 2: HOLB (Head-of-Line Blocking)
    - 요청을 여러 개 보내도 응답은 순서대로만 받을 수 있음
    - 앞에 있는 요청이 오래 걸리면, 뒤에 있는 요청들은 하염없이 기다림
- 문제 3: Header 중복
    - 의무화된 Request Header
    - HTTP Request 마다 Header 반복 전송
    - 쓸데없이 큰 traffic 발생
- 문제 4: Server는 Request 없이는 Response 불가
    - Server 가 먼저 resource를 줄 수 없음

### SPDY 와의 관계
- Google 이 개발한 실험용 protocol (2010년)
- 도입한 기능
    - Multiplexing
        - 한 연결로 여러 request/response 을 병렬로 처리
    - Header 압축
    - Server Push
        - Server 가 미리 Client에게 resource를 줌
    - 우선순위 지정
- SPDY가 웹 성능을 눈에 띄게 개선하며 주목받음
- SPDY의 거의 모든 기능이 HTTP/2.0에 흡수됨
    - SPDY 는 이후 2016년 완전 종료

## 3. HTTP/2.0의 핵심 특징

### Multiplexing
- 하나의 TCP 연결 위에서 여러 개의 Request와 Response을 동시에 처리하는 기술
- 각 Request/Response는 Stream으로 구분되어 독립적으로 흐름 유지

### Header Compression (HPACK)
- HTTP Request/Reponse는 Header 가 항상 붙음
    - 대부분의 web 요청은 비슷하거나 같은 Header 반복 전송
- 이 Header를 HPACK Alg로 압축하여 전송량을 줄임

### Server Push
- Client가 요청하지 않아도, Server가 필요한 resource를 미리 전송

### Stream 과 Frame 구조
- Stream
    - 하나의 논리적인 Request/Response eksdnl
    - 여러 Stream이 하나의 연결에서 동시에 존재할 수 잇음
- Frame
    - HTTP/2에서 실제 전송되는 최소 단위
    - data를 쪼개서 전송하며, 각각 어떤 Stream에 속하는지 정보 포함
- 구조 요약
    - TCP 연결 위에 여러 개의 Stream
    - 각 Stream은 여러 개의 Frame으로 나뉘어 전송됨
- 장점
    - 요청 간 간섭 없음
    - 유연한 제어 가능 (흐름 제어, 우선순위 설정)


### Binary Protocol
- 기존 text 기반 > binary 기반으로 동작하여 성능을 높임
- 오류 발생 가능성 감소
- 하지만 사람이 디버깅하긴 어려워짐

## 4. HTTP의 진화

- HTTP/0.9
    - 1991년
    - one-line protocol
        - 오직 GET method 만
    - 응답은 오로지 html 파일만

- 요청 형태
```
GET /mypage.html
```
- 🟢: 새롭게 도입, 🔴: 한계

- HTTP/1.0
    - 1996년
    - 🟢 HTTP Method 지원 확장: (GET, HEAD, POST)
    - 🟢 Status Code 도입 
    - 🟢 Header 도입 (Request, Reponse 모두)
        - 🟢 image, css 등 다양한 파일 지원 (Response Header - Content-Type)
    - 🔴 매 요청마다 TCP 연결 생성 & 종료

- 요청 형태
```
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)
```

- HTTP/1.1
    - 1997년
    - 표준 프로토콜
    - 🟢 Keep-Alive 도입
        - TCP 연결 재사용 가능
    - 🟢 Pipelining 도입
        - 응답을 기다리지 않고 여러 요청을 미리 연속적으로 보냄
        - 🔴 HOLB 현상 발생
            - 서버는 순서대로 응답해야 함
            - 병렬 처리 요구 ⬆️
    - 🟢 Chunk 된 Response 지원
    - 🟢 추가적인 Cache 제어 메커니즘 도입
    - 🟢 Host Header 필수화
        - 하나의 IP에서 여러 domain 운영 가능
        - Virtual Hosting 가능
    - 🔴 Header 반복 전송


- 요청 형태
```
GET /en-US/docs/Glossary/Simple_header HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header
```

- HTTP/2
    - 2015년
    - Text Protocol 이 아닌 Binary Protocol
    - 🟢 다중화 Protocol
        - 동일한 연결을 통한 병렬 요청 수행 가능
    - 🟢 Header 압축
        - data의 중복 및 오버헤드 제거
    - 🟢 Server Push
        - Client Cache에 data 저장

## 5. 실전 적용과 고려사항

### HTTP/2 를 쓰기 위해 필요한 조건
- HTTPS (TLS) 이 사실상 필수
    - 표준상 HTTPS를 요구하지는 않지만,
    대부분의 브라우저는 HTTPS을 사용한 경우에만 HTTP/2를 활성화
- 웹서버 지원 여부 확인
    - server software 가 HTTP/2를 지원해야 함
