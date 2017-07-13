# Django-tagging

### 구현 목표

1. ##### `django-tagging` 패키지를 이용하여 `tag cloud`구현

2. ##### 컨텐츠, 공약, 국회의원, 프로필 모델에 `TagField`를 추가하고 유저가 등록한 태그에 해당하는 여러 컨텐츠를 보여준다.

3. ##### 프로필에 등록되어있는 태그 목록을 보여주고 추가/수정할 수 있게한다.



### Refer

- ##### https://django-tagging.readthedocs.io/en/develop/



### Model 태그 필드

1. accounts/models.py

   ```python
   class Profile(models.Model):
       # 사용자 추가 정보 모델
       SEX_CHOICES = (
           ('1','MALE'),
           ('2','FEMALE'),
       ) # 성별 종류 명시

       user = models.OneToOneField(settings.AUTH_USER_MODEL, related_name='profile_set') # AUTH_USER 모델과 1:1 관계 설장
       sex = models.CharField(max_length=2, choices=SEX_CHOICES, verbose_name='성별') # 성별
       birth = models.CharField(max_length=16, null=True, verbose_name='생년월일', validators=[birth_validator]) # 생년월일
       city = models.CharField(max_length=16, null=True, verbose_name='시/도') # 시/구
       district = models.CharField(max_length=8, null=True, verbose_name='구') # 구
       tag = TagField() # 관심사 태그

       def __str__(self):
           return  self.user.username
   ```

2. cast/models.py

   ```python
   class Contents(models.Model):
       # 컨텐츠(뉴스/영상) 모델
       # ---- 중략 ----
       
       tag = TagField() # 컨텐츠 태그

       def get_absolute_url(self):
           return reverse('cast:contents_detail',
               args = [self.pk])

       def __str__(self):
           return self.title

   class CongressMan(models.Model):
       # 국회의원 모델
       # ---- 중략 ----
       tag = TagField() # 국회의원 태그

       class Meta():
           ordering =['id']

       def get_absolute_url(self):
           return reverse('cast:congressman:detail')

       def __str__(self):
           return self.name

   class Pledge(models.Model):
       # 공약 모델
   	# ---- 중략 ----
       tag = TagField() # 공약 태그
   ```



       def __str__(self):
           return self.title
    
       def get_absolute_url(self):
           return reverse('cast:pledge_detail', args = [self.pk])
   ```

### 태그 추가 및 수정하기

1. ##### Url conf (account/urls.py)

   ```python
   from django.conf.urls import url, include
   from accounts import views

   urlpatterns = [
       url(r'^set-tag/$', views.set_tag, name='set_tag'),
   ]
   ```

2. ##### 태그 설정 뷰(accounts/views.py)

   ```python
   def set_tag(request):
       # 태그 추가/수정 페이지

       user = request.user.profile_set # 현재 유저의 프로필 정보
       if request.method == 'POST':
           user.tag = request.POST.get('tag') # 요청 유저의 태그 정보를 받아온 태그 정보로 저장
           user.save() # 디비에 프로필 정보 저장
           return redirect('accounts:profile')
       else:
           form = TagForm(instance = user) # 유저 정보를 받아와 폼 인스턴스 생성
       return render(request, 'accounts/tag_form.html', {
           'form': form,
           })
   ```

3. ##### 태그 폼 (accounts/forms.py)

   ```python
   class TagForm(forms.ModelForm):
       class Meta:
           model = Profile
           fields = ['tag']
   ```

4. ##### 태그 설정 간단한 템플릿 (accounts/tag_form.html)

   ```html
   {% raw %}
     {% extends 'cast/layout.html' %}

     {% block content %}
         <form action="" method="post">
             {% csrf_token %}
                     <script type="text/javascript">
             </script>
             <table>
                 {{ form.as_table }}
             </table>
             <input type="submit">
         </form>
     {% endblock %}
   {% endraw %}
   ```

##### 태그가 등록된 목록 보여주기

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

       pledge_list = Pledge.objects.filter(tag__contains=tag) # 공약 리스트
       contents_list = Contents.objects.filter(tag__contains=tag) # 콘텐츠 리스트
       congressman_list = CongressMan.objects.filter(tag__contains=tag) # 국회의원 리스트

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