# django 감정표현 기능



### 구현 기능

1. ##### 각 컨텐츠내에 여러 감정표현 기능을 추가한다.




### Url Conf(cast/urls.py)

```python
url(r'^contents-emotion/(?P<contents_pk>\d+)/$', views.contents_emotion, name='contents_emotion')
```



### 감정 표현 모델 추가(cast/models.py)

- ##### 감정표현을 위한 유저와 컨텐츠간의 M:N 관계를 위해 `ContentsEmotion` 모델 추가

- ##### 컨텐츠 모델에 감정표현 갯수 카운팅 함수 추가

```python
class ContentsEmotion(models.Model):
    # 컨텐츠내 감정 표현 관계 모델

    CONTENTS_EMOTION_CHOICE = (
        ('0', '선택안함'),
        ('1', '역겨워요'),
        ('2', '놀라워요'),
        ('3', '기뻐요'),
        ('4', '슬퍼요'),
        ('5', '화나요'),
        ('6', '멋져요'),
    ) # 감정 표현 종류

    user = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='contents_emotion_set') # 유저와 1:N 관계 설정
    contents = models.ForeignKey(Contents) # 컨텐츠와 1:N 관계 설정
    name = models.CharField(max_length=2, default='0', choices=CONTENTS_EMOTION_CHOICE) # 감정의 이름

    def __str__(self):
        return self.get_name_display() # name 필드의 Choice Value 값을 보여 준다.
    
    
class Contents(models.Model):
    # 컨텐츠(뉴스/영상) 모델
	
    [...]
    
        def get_count_emotion(self, emotion):
        # 해당 컨텐츠의 각 감정들의 개수 카운트
        emotion_count = ContentsEmotion.objects.filter(contents_id=self.id, name=emotion).count()
        return emotion_count
```



### 감정 표현 처리(cast/views.py)

- ##### 유저가 컨텐츠내의 감정표현을 표현한다.

- ##### 감정정보를 GET으로 받아온다.

- ##### 이미 감정표현 한적이 있는 경우 감정정보가 같은지 비교하여 처리한다.

- ##### 처음인 경우 받아온 감정정보를 db에 추가한다.

```Python
def contents_emotion(request, contents_pk):
    # 컨텐츠의 감정표현 처리
    if request.is_ajax():
        # ajax 요청일 경우
        user = request.user # 현재 유저의 정보를 받아온다.
        contents = get_object_or_404(Contents, pk=contents_pk) # 현재 콘텐츠 인스턴스 생성
        emotion_name = request.GET.get('emotion_name','') # url GET 정보에 담겨있는 감정정보를 받아온다.
        before_emotion_name = ''
        if user.contents_emotion_set.filter(contents=contents).exists():
            # 해당 컨텐츠에 감정 표현을 이미 해놓은 경우
            user_emotion = user.contents_emotion_set.get(contents=contents) # 해당유저가 컨텐츠에 해놓은 감정표현을 정보를 받아와
            before_emotion_name = user_emotion.name
            if user_emotion.name == emotion_name:
                # 같은 감정을 한번 더누르면 삭제.
                ContentsEmotion.objects.filter(contents=contents, user=user).delete() # 인스턴스 삭제
            else:
                # 다른 감정을 누를 경우
                user_emotion.name = emotion_name # 감정을 바꿔준 후 저장
                user_emotion.save()
        else:
            # 감정표현을 처음 하는 경우 새롭게 생성
            ContentsEmotion.objects.create(
                user=user,
                contents=contents,
                name=emotion_name,
            )
        emotion_count = ContentsEmotion.objects.filter(contents=contents, name=emotion_name).count()
        before_emotion_count = ContentsEmotion.objects.filter(contents=contents, name=before_emotion_name).count()
        context = {}
        context['status'] = 'success'
        context['emotion_count'] = emotion_count
        context['before_emotion_name'] = before_emotion_name
        context['before_emotion_count'] = before_emotion_count
    else:
        context = {}
        context['message'] = '잘못된 접근입니다.'
        context['status'] = 'fail'

    # dic 형식을 json 형식으로 바꾸어 전달한다.
    return HttpResponse(json.dumps(context), content_type='application/json')
```



### 커스텀 템플릿 태그(cast/templatetags/cast_extras.py)

- ##### 템플릿에서 함수에 인자를 넘겨주기 위해 커스텀 템플릿 태그 정의

```python
from django import template
from cast.models import Contents


register = template.Library()

@register.filter(name='emotion_count')
def emotion_count(objects, emotion):
    return objects.get_count_emotion(emotion)

```

### 템플릿(cast/contents_detail.html)

- ##### 모델에 있는 함수를 호출해주는 커스텀 템플릿 태그를 추가해 사용함.

  `{{ contents|emotion_count:"3" }}`

- ##### 간단하게 기능만 보기위해 버튼 6개 추가

  ```html
  {% load cast_extras %}
  <ul class="emotion_box">
    <li class="emotion"><a class="emobtn" role="button" id="glad" name="기뻐요" value="3">
      <img src="{% static 'cast/img/glad.png' %}" alt="" style="width:23px;"><span class="emotion_name">기뻐요</span><span class="emotion_score" id="emo_3">{{ contents|emotion_count:"3" }}</span></a></li>
    <li class="emotion"><a class="emobtn" role="button" id="disgusting" name="역겨워요" value="1">
      <img src="{% static 'cast/img/discusting.png' %}" alt="" style="width:23px;"><span class="emotion_name">역겨워요</span><span class="emotion_score" id="emo_1">{{ contents|emotion_count:"1" }}</span></a></li>
    <li class="emotion"><a class="emobtn" role="button" id="angry" name="화나요" value="5">
      <img src="{% static 'cast/img/angry.png' %}" alt="" style="width:23px;"><span class="emotion_name">화나요</span><span class="emotion_score" id="emo_5">{{ contents|emotion_count:"5" }}</span></a></li>
    <li class="emotion"><a class="emobtn" role="button" id="nice" name="멋져요" value="6">
      <img src="{% static 'cast/img/nice.png' %}" alt="" style="width:23px;"><span class="emotion_name">멋져요</span><span class="emotion_score" id="emo_6">{{ contents|emotion_count:"6" }}</span></a></li>
    <li class="emotion"><a class="emobtn" role="button" id="sad" name="슬퍼요" value="4">
      <img src="{% static 'cast/img/sad.png' %}" alt="" style="width:23px;"><span class="emotion_name">슬퍼요</span><span class="emotion_score" id="emo_4">{{ contents|emotion_count:"4" }}</span></a></li>
    <li class="emotion"><a class="emobtn"role="button" id="surprising" name="놀라워요" value="2">
      <img src="{% static 'cast/img/surprising.png' %}" alt="" style="width:23px;"><span class="emotion_name">놀라워요</span><span class="emotion_score" id="emo_2">{{ contents|emotion_count:"2" }}</span></a></li>
  </ul>
  ```

- ##### ajax 요청을 보내 처리한다.

  ```javascript
      $('.emobtn').click(function(){
          var emotion_name = $(this).attr('value'); // 클릭한 요소의 attribute 중 value의 값을 가져온다.

          $.ajax({
              url: "{% url 'cast:contents_emotion' contents.pk %}?emotion_name=" + emotion_name, // 통신할 url을 지정한다.
              data: {'csrfmiddlewaretoken': '{{ csrf_token }}'}, // 서버로 데이터를 전송할 때 이 옵션을 사용한다.
              dataType: "json", // 서버측에서 전송한 데이터를 어떤 형식의 데이터로서 해석할 것인가를 지정한다. 없으면 알아서 판단한다.

              success: function(data){
                  // 요청이 성공했을 경우 눌려있는 버튼 모양을 바꿔준다.
                  $('#emo_' + emotion_name).text(data.emotion_count)
                  $('#emo_' + data.before_emotion_name).text(data.before_emotion_count)
              },
              error:function(error){
                  // 요청이 실패했을 경우
                  console.log(error.status + error.message)
              }
          });
      });
  ```