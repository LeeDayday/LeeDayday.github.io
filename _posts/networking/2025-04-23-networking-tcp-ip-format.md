---
title: "TCP Segment 구조 직접 확인해보기"

categories:
  - networking
tags: [networking, tcp/ip, cs, wireshark]

date: 2025-04-23
last_modified_at: 2025-04-23
---

- TCP Segment 구조 Diagram
![DNS Query Image](https://media.geeksforgeeks.org/wp-content/uploads/20230624200917/TCP-packet-format.jpg){: .align-center}

- Wireshark에서 확인한 TCP Segment 구조
![DNS Query Image](/assets/images/networking/tcp_syn.png){: .align-center}
- `Source Port`: 53747
- `Destination Port`: 80
- `Sequence Number`: 0 
- `Acknowledgement Number`: 0
- `Data Offset`: 1011 .... = Header Length: 44 bytes
    - TCP payload 가 시작되는 위치를 나타냄
    - TCP header의 크기를 저장
- `Reserved`: 0
    - `Flags` 앞에 존재하며 미래에 사용될 것을 대비해 항상 0으로 설정
    - Wireshark에서는 별도로 표시하지 않거나 `Flags`의 일부로 포함시킴
- `Flags`: 0x002 SYN
- `Window`: 65535
- `Checksum`: 0x9ca2
- `Urgent Pointer`: 0
- `Options`: (24bytes), Maximum segment size, No-Operation (NOP), Window scale, ...
- `Padding`: 
    - Options 가 4바이트 로 나누어떨어지지 않는 경우, 크기를 맞춰주기 위해 0을 채우는 필드
    - Wireshark 에서는 별도로 표시하지 않음
- `Data`: TCP Segment Len: 0
    - payload (data) 가 없는 순수 SYN Packet 이어서 data가 표시되어 있지 않음

--- 

- [이미지 1 출처](https://www.geeksforgeeks.org/tcp-ip-packet-format/)