---
title: "[OS Basics] 프로세스(Process)의 정의와 구조"

categories:
  - os
tags: [os, cs]

date: 2025-04-04
last_modified_at: 2025-04-11
---

### Process 의 정의
#### Process란?
- 실행 중인 프로그램의 하나의 실행 단위 (An instance of a program in Execution)
- OS에서 program이 실행되면, 단순히 코드를 읽는 것이 아니라 *독립적인 실행 환경 (=process)*이 구성됨
- 비유를 하자면...
    - User: 요리를 주문하는 손님
    - OS: 요리사
    - Program: 요리 레시피
    - Process: 실제로 요리중인 음식

#### Program과 Process의 차이

| 항목 | Program | Process |
| -- | -- | -- |
| 상태 | Static (실행되지 않은 코드) | Dynamic (실행 중인 상태) |
| 저장 위치 | 주로 Disk (파일 형태로 존재) | Memory에 적재되어 실행됨 |
| 생성 시점 | 작성 / 설치 시 | Program을 실행할 때 OS 에 의해 |
| 예시 | hello.exe, main.c | program 을 실행했을 때 만들어진 실행 단위 |


### Process의 구조
#### Logical Address Space (논리 주소 공간)
- OS는 Program이 실행되면 process에 **독립적인 주소 공간**을 할당
    - = Logical Address Space
- 구성 요소

| 영역 | 역할 |
| -- | -- |
| Text Segment | 실행 code |
| Data Segment | 전역 변수, static 변수 등 |
| Heap | 동적 memory |
| Stack | 함수 호출, 지역 변수 저장 |

- 각 process 는 고유한 Logical Address Space를 가짐
    - 서로 다른 process 간에는 직접적인 memory 침범 불가

#### OS가 Process State을 관리하는 핵심 구조: PCB (Process Control Block)
- PCB: OS가 개별 process를 관리하기 위해 유지하는 데이터 구조
- 구성 요소

| 영역 | 역할 |
| -- | -- |
| Process State | 현재 process의 state |
| Program Counter | 다음에 실행할 명령어의 주소 |
| CPU Registers | 수행 중이던 작업을 저장 (Context Switching 시 필요) |
| Memory Management Info | Page Table, Segment Table 등 메모리 관련 정보 |
| Procces ID | Process 고유 식별자 |
| Accounting Info | CPU 사용 시간, Process 시작 시간 등 |
| I/O 상태 정보 | 어떤 I/O 자원을 요청했는지, 대기 중인 장치 등 |

### Process의 실행 흐름
#### Process States
- Process 는 특정 시점에 하나의 State를 가진다
- Process는 실행을 위해 여러 State를 오가며, OS 가 이를 State transition으로 관리
- 대표적인 5가지 process state
| 상태 | 설명 | 
| -- | -- |
| New | Process가 생성 중인 상태 (ex. Program 실행 명령이 들어온 시점) |
| Ready | 실행할 준비가 된 상태 (CPU가 할당되기만을 기다리고 있음) |
| Running | CPU가 할당되어 실제 명령을 수행 중인 상태 |
| Waiting(Blocked) | I/O 등의 이벹르를 기다리는 중이라 실행할 수 없는 상태 |
| Terminated | 실행을 마치고 종료된 상태 (자원을 회수 중이거나 회수됨) |

#### Process State Transition
```markdown

1. **New → Ready**  
   - 프로세스가 생성되어 실행 준비 완료 *(Admitted)*

2. **Ready → Running**  
   - 스케줄러가 CPU 할당 *(Scheduler Dispatch)*

3. **Running → Waiting (Blocked)**  
   - 입출력(I/O) 등 외부 이벤트 대기 *(I/O or event wait)*

4. **Waiting → Ready**  
   - I/O 완료 등 이벤트 발생 시 준비 상태로 복귀 *(I/O or event completion)*

5. **Running → Ready**  
   - 타임 슬라이스 종료 (Context Switching 발생) *(Time-out)*
   - Context Switching 발생

6. **Running → Terminated**  
   - 실행 완료 또는 종료 명령 *(Release)*
```

#### 상태 관리가 필요한 이유
- CPU 는 한 번에 하나의 Process만 실행 가능 (Multi-Cores는 병렬 처리 가능)
- System 자원을 효율적으로 분배하고 충돌을 방지하기 위해

### Thread와의 비교
#### Thread
- Process 내에서 실행되는 작업 단위

### Multiprocessing 과 Multithreading