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

### DNS 질의 과정 (Domain > IP 찾는 흐름)

### 재귀 질의 vs 반복 질의

### Root Name Server > TLD > 권한 네임 서버 구조

## DNS 구성 요소

### A, AAAA, CNAME, MX, NS, TXT Record

### 권한 DNS 서버, 캐시 DNS 서버의 역할

## DNS 캐싱과 TTL

### 캐싱 동작 원리

### TTL (Time To Live) 개념

### 변경 후 반영되지 않는 이유

## DNS 관련 실습 및 명령어

## DNS와 보안 이슈

### DNS Spoofing, Cache Poisoning

### DNS over HTTPS (DoH), DNSSEC 개요

## DNS 가 없다면?
