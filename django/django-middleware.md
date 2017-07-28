# 장고 미들웨어 맛보기(qna앱)

### 구현 기능

- ##### 장고 미들웨어 구현

- ##### 오늘 날짜의 답변내역이 없을 경우 질문페이지로 리디렉션




### Refer

- ##### [Django-middleware-docs](https://docs.djangoproject.com/en/1.11/topics/http/middleware/)


- ##### [미들웨어 맛보기](http://uiandwe.tistory.com/1160)

##### 

### 미들웨어란?

- ##### 모든 request/response 이벤트에 대한 중간 처리 단계

  ##### ![middleware](../img/django/middleware.svg)

### 테스트 미들웨어 구현

1. ##### `MIDDLEWARE_CLASSES` 등록(settings.py)

   `qna.middleware.QuestionMiddleware`

   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'django.contrib.sessions.middleware.SessionMiddleware',
       'django.middleware.common.CommonMiddleware',
       'django.middleware.csrf.CsrfViewMiddleware',
       'django.contrib.auth.middleware.AuthenticationMiddleware',
       'django.contrib.messages.middleware.MessageMiddleware',
       'django.middleware.clickjacking.XFrameOptionsMiddleware',
       'qna.middleware.QuestionMiddleware',
   ]
   ```

2. ##### 미들웨어 구현(middleware.py)

   - ##### 오늘 질문에 대한 답이 없을 경우 질문 페이지로 이동 시킨다.

   ```python
   from django.conf import settings
   from django.db import connection
   from django.template import Template, Context
   from datetime import date
   from django.shortcuts import redirect, render, get_object_or_404
   from .models import Question
   from qna.utils import get_today_id
   import re

   class QuestionMiddleware(object):

       DISALLOW_URLS = [
           r'^/accounts/profile/',
           r'^/qna/[0-9a-zA-Z]+',
           r'^/exqna/',
           r'^/diary/',
       ]

       def __init__(self, get_response):
           # 미들웨어 초기화 함수, get_response 외에 인자를 받을 수 없다.
           self.get_response = get_response
           # One-time configuration and initialization.

       def __call__(self, request):
           # Code to be executed for each request before
           # the view (and later middleware) are called.
           response = self.get_response(request)

           # Code to be executed for each request/response after
           # the view is called.

           return response

       def process_request(self, request):
           return

       def process_view(self, request, view_func, view_args, view_kwargs):
           # 장고의 view가 호출되기 전에 실행된다.

           today_id = get_today_id()

           if request.user.answer_set.filter(question_id=today_id).exists():
               # 오늘 질문에 대한 답이 있을 경우
               return None

           for pattern in self.DISALLOW_URLS:
               if re.match(pattern, request.path):
                   return redirect('qna:question')

           return None

       def process_template_response(self, request, response):
           # 장고의 view가 실행이 끝난 후 실행된다.
           return response


       def process_response(self, request, response):
           return response
   ```

