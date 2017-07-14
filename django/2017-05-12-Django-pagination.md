
# Django 페이지네이션

### 구현 기능

- Digg style 페이지네이션에 스타일입히기
- infinite scroll with ajax 페이지네이션



### Reference

- [django-el-pagination](http://django-el-pagination.readthedocs.io/en/latest/digg_pagination.html)
- [infinite-scroll-with-django](https://simpleisbetterthancomplex.com/tutorial/2017/03/13/how-to-create-infinite-scroll-with-django.html)
- [django-pagination-docs](https://docs.djangoproject.com/en/1.11/topics/pagination/)
- [django-pagiation](https://cjh5414.github.io/django-pagination/)


### Dependencies

We don’t need anything other than Django installed in the back-end. For this example you will need jQuery and Waypoints.

- [jQuery 3.1.1](https://jquery.com/download/)
- [Waypoints](http://imakewebthings.com/waypoints/)

After downloading the dependencies, include the following scripts in your template:

##### **layout.html**

```html
<script src="{% static 'js/jquery-3.1.1.min.js' %}"></script>
<script src="{% static 'js/jquery.waypoints.min.js' %}"></script>
<script src="{% static 'js/infinite.min.js' %}"></script>
```


### Django Pagination & infinity scrolling

- ##### Views.py

```python
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger
from pledge.models import *

def congressman_list(request):
    # 국회의원 리스트
    congressman_list = CongressMan.objects.all() # 국회의원 리스트

    page = request.GET.get('page', 1) # 페이지 번호를 받아온다.
    paginator = Paginator(congressman_list, 15) # 페이지 당 15개씩 표현

    try:
        # 페이지 번호가 있으면 해당 페이지로 이동
        congressmans = paginator.page(page)
    except PageNotAnInteger:
        # 페이지 번호가 숫자가 아닐 경우 첫페이지로 이동
        congressmans = paginator.page(1)
    except EmptyPage:
        # 페이지가 비어있을 경우 paginator.num_page = 총 페이지 개수
        # paginator.num_page = 국회의원 총 수(300) / 페이지 나눔 개수(15
        congressmans = paginator.page(paginator.num_pages)

    return render(request, 'pledge/congressman_list.html', {'congressmans': congressmans})
```



- ##### Congressman.html
  {% raw %}
```Html
{% extends "pledge/layout.html" %}

{% block content %}
    <div class="infinite-container"><!-- 페이지네이션된 항목들이 표시될 컨테이너 /.infinite-more-link가 화면에 뜰 때마다 로딩된다.-->
        <div class="infinite-item"> <!-- 표시될 항목 -->
            <div class='container'>
                <div class="congressman-list-row" style="margin:0 auto;">
                    {% for congressman in congressmans %}
  						'''
                    {% endfor %}
                </div><!-- /.congrssman-list-row -->
            </div><!-- /.container -->
        </div><!-- /.infinite-item -->
    </div><!-- /.infinite-container -->

    {% if congressmans.has_next %}
        <!-- 다음페이지가 없을때까지 .infinite-more-link 표시-->
        <a class="infinite-more-link" href="?page={{ congressmans.next_page_number }}">More</a>
    {% endif %}

    <div class="loading" style="display: none;">
        Loading...
    </div><!-- /.loading -->

    <script>
        {% load staticfiles %}

        var infinite = new Waypoint.Infinite({
          element: $('.infinite-container')[0],
          onBeforePageLoad: function () {
            $('.loading').show();
          },
          onAfterPageLoad: function ($items) {
            $('.loading').hide();
          }
        });
    </script>
{% endblock %}
```
{% endraw %}



## django EL(Endless) pagination

`django`에 기본 `pagination` 기능을 활용한 패키지가 이미 많이 나와 있었고,

- django EL(Endless) pagination
- django-bootstrap-pagination
- django-pure-pagination

그중 `django_el_pagination`을 사용해봤다.



- #### Install & setting

  - ##### 설치

    ```linux
    $ pip install django-el-pagination
    ```

  - ##### settings.py

      ```python
      INSTALLED_APPS = [
          ...
          'pledge',
          'el_pagination',
      ]
      ```

- #### Pagination Style

  Django EL Pagination에는 Digg-style, Twitter-style 두 가지 스타일이 존재한다. 이 외에도 추가적인 기능들도 있다. 자세한 내용은 아래 문서를 참고하면 된다.

  - [Digg-style](http://django-el-pagination.readthedocs.io/en/latest/digg_pagination.html)
  - [Twitter-style](http://django-el-pagination.readthedocs.io/en/latest/twitter_pagination.html)

- #### Digg-style

  - ##### views.py

    ```python
    def post_list(request):
        # 피드백 게시판 리스트
        post_list = FeedbackPost.objects.all()

        return render(request,  'feedback/post_list.html', {
                'post_list' : post_list,
            })
    ```


-   ##### post_list.html
    {% raw %}

    ```Html
    {% extends 'pledge/layout.html' %}
    {% block content %}
    <div class='container'>
        <table class='table table-striped' style='margin-top: 50px;'>
            <tbody>
                {% load el_pagination_tags %}
                {% paginate 10 post_list %} <!-- 한페이지에 10개씩 표현 -->
                    {% for post in post_list %}
    		          '''
                    {% endfor %}
            </tbody>
        </table>

        {% get_pages %}
        <div class='text-center' style='font-size: 11pt'>
            <ul class='pagination'>
                {% if pages.paginated %}
                    <li>
                        {{ pages }}
                    </li>
                {% endif %}
            </ul>
        </div>
    </div>
    {% endblock %}
    ```
    {% endraw %}
