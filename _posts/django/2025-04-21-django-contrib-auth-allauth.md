---
title: "Django 프로젝트에서 CustomUser 모델과 로그인 방식 정리"

categories: [django]
tags: [django, authentication]

date: 2025-04-21
last_modified_at: 2025-04-21
---
## 1. AUTH_USER_MODEL이란?

### Django에서 기본 사용자 모델을 대체하는 설정
#### AUTH_USER_MODEL이란?

- Django에서는 기본적으로 `auth.User`라는 내장 사용자 모델을 제공 
- 하지만 대부분의 실제 프로젝트에서는 사용자 필드를 커스터마이징하거나, 소셜 로그인 등 다양한 인증 방식과 연동하기 위해 **사용자 모델을 커스터마이징(Custom User Model)**

- 이를 위해 Django는 설정 파일(`settings.py`)에 다음과 같은 설정을 제공:
```python
AUTH_USER_MODEL = 'accounts.CustomUser' # Custom한 User Model 명시
```
- 위 설정은 Django 전체에 대해 **"이 프로젝트에서 사용자(User)는 accounts.models.CustomUser를 기준으로 사용하겠다"**는 의미

### ⚠️ 주의
- User 관련 설정은 초기에 하는 것이 좋음
    - 이미 project의 Migration이 진행된 상태에서 새로운 User Migration을 시도하면 계!속! error 발생 가능 ... (기존에 Migration한 User 설정과 충돌 발생)


## 2. CustomUser 모델 구성

### AbstractUser를 상속한 사용자 모델 예시
- 가장 일반적인 방식: `AbstractUser`

```python
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
    pass
```
- 현재 프로젝트에선 이렇게 선언... 필요시 수정할 예정 (기존 Model과 필드가 크게 다르지 않다면, migration error가 발생할 확률은 적음)

### 추가 팁
#### 필요한 model은 `get_user_model()` 로 가져오기
- 모델을 직접 import 하는 것 보단, 내장 함수를 쓰는 것이 안전하고 확장성 O
    - (직접 import: 파일의 경로에 의존하게 되어서 에러 발생 가능 ⬆️)