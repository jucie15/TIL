# Django-tagging

### 구현 목표

1. ##### `django-tagging` 패키지를 이용하여 `tag cloud`구현

2. ##### 컨텐츠, 공약, 국회의원, 프로필 모델에 `TagField`를 추가하고 유저가 등록한 태그에 해당하는 여러 컨텐츠를 보여준다.

3. ##### 프로필에 등록되어있는 태그 목록을 보여주고 추가/수정할 수 있게한다.




### Refer

- ##### https://django-tagging.readthedocs.io/en/develop/




### SETTING

- ##### `FORCE_LOWERCASE_TAGS`  모든 태그를 소문자로 `default=False`

- ##### `MAX_TAG_LENGTH` 최대 글자 수 `default=50`

```python
FORCE_LOWERCASE_TAGS = True # 모든 태그 이름을 소문자로
MAX_TAG_LENGTH = 2 # 최대 글자 개수 제한
```



### Model 태그 필드

1. accounts/models.py

   ```python
   from tagging.registry import register

   class Profile(models.Model):
       # 사용자 추가 정보 모델

       [...]

       def __str__(self):
           return  self.user.username

   register(Profile)
   ```

2. cast/models.py

   ```python
   from tagging.registry import register

   class Contents(models.Model):
       # 컨텐츠(뉴스/영상) 모델
       # ---- 중략 ----
       
       tag = TagField() # 컨텐츠 태그

       def get_absolute_url(self):
           return reverse('cast:contents_detail',
               args = [self.pk])

       def __str__(self):
           return self.title

   register(Contents)
       
   class Congressman(models.Model):
       # 국회의원 모델
       # ---- 중략 ----
       tag = TagField() # 국회의원 태그

       class Meta():
           ordering =['id']

       def get_absolute_url(self):
           return reverse('cast:congressman:detail')

       def __str__(self):
           return self.name

   register(Congressman)    
       
   class Pledge(models.Model):
       # 공약 모델
   	# ---- 중략 ----
       tag = TagField() # 공약 태그
       
       def __str__(self):
          return self.title

      	def get_absolute_url(self):
          return reverse('cast:pledge_detail', args = [self.pk])

   register(Pledge)
   ```



### 태그 추가 및 수정하기

1. ##### Url conf (account/urls.py)

   ```python
   from django.conf.urls import url, include
   from accounts import views

   urlpatterns = [
       url(r'^ajax/add/tag/$', views.ajax_add_tag, name='ajax_add_tag'),
   ]
   ```


2. ##### 태그 설정 뷰(accounts/views.py)

   ```python
   def ajax_add_tag(request):
       # 태그 추가 버튼 클릭시
       if request.is_ajax():
           # ajax 요청시
           tag = request.GET.get('tag','')
           profile = request.user.profile

           Tag.objects.add_tag(profile, tag) # 해당 인스턴스에 태그 추가

           data = json.dumps({
               'status': 'success',
               }) # json 형식으로 파싱

       else:
           data = json.dumps({
               'status': 'fail',
               }) # json 형식으로 파싱

       mimetype = 'application/json'
       return HttpResponse(data, mimetype
   ```

3. ##### 태그 추가 템플릿 (accounts/tag_form.html)

   1. ##### HTML

      ```Html
      <div class="add-tag-box">
        <input type="text" id="add-tag-value" placeholder="태그를 추가해주세요.">
        <a id="add-tag"><span class="upload-btn">추가</span></a>
        <span class="cancel-btn"><i class="fa fa-times" aria-hidden="true"></i></span>
      </div>
      	<span class="add-tag-btn"><i class="fa fa-plus" aria-hidden="true"></i>태그추가</span>
      ```

   2. ##### JavaScrip

      ```javascript
      <script>
          $('#add-tag').click(function(){
              // 태그 추가 버튼 클릭시
              tag = $('#add-tag-value').val()

              $.ajax({
                  type: "GET", // GET 요청으로 한다.
                  url: "{% url 'accounts:ajax_add_tag' %}?tag=" + tag, // 통신할 url을 지정한다.
                  dataType: "json", // 서버측에서 전송한 데이터를 어떤 형식의 데이터로서 해석할 것인가를 지정한다. 없으면 알아서 판단한다.

                  success: function(data){
                      // 요청이 성공했을 경우 눌려있는 버튼 모양을 바꿔준다.
                      $("#add-tag-value").before("<span class='tag'><a href='{% url 'cast:tagged_list' %}?tag="+tag+"'>"+tag+"</a></span>");
                      $('.add-tag-box').css('display','none');
                      $('.add-tag-btn').css('display','inline');
                      //댓글 추가 버튼 위치도 변경
                      $('#cd-upload-btn').css('top','99%');
                  },
                  error:function(error){
                      // 요청이 실패했을 경우
                      console.log(error)
                  }
              });
          });
      </script>
      ```



### 태그가 등록된 목록 보여주기

1. ##### Url conf (cast/urls.py)

   ```python
   from django.conf.urls import url, include
   from cast import views

   urlpatterns = [
       url(r'^tagged/$', views.tagged_list, name='tagged_list'),
   ]
   ```

2. ##### 해당 태그가 포함 되어있는 각 컨텐츠의 리스트를 보여준다.(cast/views.py)

   ```Python
   def tagged_list(request):
       # 해당 태그가 포함 되어있는 전체 리스트
       tag = request.GET.get('tag','')

       pledge_list = TaggedItem.objects.get_by_model(Pledge, tag) # 공약 리스트
       contents_list = TaggedItem.objects.get_by_model(Contents, tag) # 콘텐츠 리스트
       congressman_list = TaggedItem.objects.get_by_model(Congressman, tag) # 국회의원 리스트
       context = {}
       context['contents_list'] = contents_list
       context['pledge_list'] = pledge_list
       context['congressman_list'] = congressman_list

       return render(request, 'cast/tagged_list.html', context)
   ```

3. ##### 간단한 리스트 뷰 (cast/tagged_list.html)

   ```html
   {% raw %}
     {% extends 'cast/layout.html' %}

     {% block content %}

         {% for contents in contents_list %}
             <iframe width="200" height="200" src="{{ contents.url_path }}" frameborder="0" allowfullscreen></iframe>
             <a href="{% url 'cast:contents_detail' contents.pk %}">
                 {{ contents.title }}</a>
         {% endfor %}
         <p>
         {% for congressman in congressman_list %}
             <a href="{% url 'cast:congressman_detail' congressman.pk %}">
                 {{ congressman }}</a>
         {% endfor %}
         <p>
         {% for pledge in pledge_list %}
             {{ pledge }}
         {% endfor %}
     {% endblock %}
   {% endraw %}
   ```

4. ##### 프로필에서 내가 등록한 태그들 보기 (accounts/profile.html)

   ```Html
   {% raw %} 
     {% extends 'cast/layout.html' %}

     {% block content %}
         <img src="{{ user.socialaccount_set.all.first.get_avatar_url }}" />
         <div class="profile_info">{{ user.socialaccount_set.first.extra_data.kaccount_email }}</div>
             {% load tagging_tags %}

         {% tags_for_object profile as tags %}
         {% for tag in tags %}
         <a href="{% url 'cast:tagged_list' %}?tag={{ tag.name }}">
             {{ tag.name }}
         </a>
         {% endfor %}
         <a href="{% url 'accounts:set_tag' %}">
             <h2>수정</h2></a>
     {% endblock %}
   {% endraw %}
   ```



### 태그 목록 정렬하기

- ##### 각 모델에 등록된 태그의 개수별로 정렬

  ```python
  tag_list = Tag.objects.usage_for_model(Contents, counts=True) # 태그아이템 개수 포함한 리스트

  tag_list.sort(key=lambda tag: tag.count, reverse=True) # 개수 기준 정렬
  ```

