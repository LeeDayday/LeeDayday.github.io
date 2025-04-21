---
title: "Django Forms 쉽게 이해하기"

categories: [django]
tags: [django, forms]

date: 2025-04-21
last_modified_at: 2025-04-21
---
## Form 이란?
### Django Forms 은 Google Forms 와 같다!
- Google Forms (Google 설문조사)을 떠올리면 Django Form을 쉽게 떠올릴 수 있다.

![Google Form Image](/assets/images/django/google-forms.png){: .align-center}{: width="50%" height="100%"}
- Google Forms을 보면 다음과 같은 질문 속성을 정의할 수 있다.
    - 질문의 유형 (객관식, 주관식, ...)
        - 단일 선택, 다중 선택
        - 단문형 텍스트, 장문형 텍스트
    - 응답 필수 질문
    - 제출 기한
    - ...
- 사용자가 정해진 형식과 맞지 않는 입력을 하면, 제출이 거부된다. (유효성 검사)
- 유효성 검사를 성공적으로 통과한다면, 제출 완료 & 기록에 남는다


| Google Forms| Django Forms |
| -- | -- |
| 질문 유형 정의 | 입력 필드 정의 |
| 유효성 검사 | 유효성 검사 |
| 검사 통과 시 저장 | 검사 통과 시 DB 저장 |
| 시각적 폼 제공 | HTML + Backend 처리 |

- 더 다양한 기능은 차차 알아가보자
- [공식 코드](https://github.com/django/django/tree/1831f7733d3ef03d1ca7fac3e8d9f4c5e3e3375e/django/forms)