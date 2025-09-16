---
title: "Django 객체 역참조 (reverse relation)"

categories: [django]
tags: [django, django-orm]

date: 2025-05-09
last_modified_at: 2025-05-09
---


## 왜 필요한 개념일까?
- Django ORM을 쓰다보면 정방향 참조는 쉽게 떠올릴 수 있으ㅁ 
    - 예를 들어 `Book` 모델에 `author = ForeignKey(Author)`가 있다면, `book.author`로 접근하면 됨  
- 그런데 반대로 **저자를 기준으로 책 목록을 가져오고 싶을 때**는 어떻게 해야 할까?
- ➡️ **역참조(reverse relation)**

## 어떻게 사용하는가?

### 기본 사용법

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```
- 정방향: book.author
- 역방향: author.book_set.all()
- ForeignKey가 걸린 반대편에서는 자동으로 <모델명>_set 이라는 매니저가 생성됨
    - 가독성이 떨어지므로 related_name으로 가독성 개선 추천

- related_name으로 가독성 개선
```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(
        Author,
        on_delete=models.CASCADE,
        related_name="books"
    )
```

### 마무리
- 정방향: ForeignKey 필드를 통해 직접 접근
- 역방향: 자동 생성되는 <모델명>_set 또는 related_name으로 접근한다
- ORM 쿼리에서도 author.books__title처럼 활용할 수 있다
- 한쪽에서 정의한 관계를 양방향으로 활용할 수 있게 해주는 것이 역참조의 핵심