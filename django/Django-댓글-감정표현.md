# django 댓글 및 댓글 감정표현



### 구현 기능

1. ##### 각 컨텐츠내에 댓글 기능(쓰기/수정/삭제) 추가

2. ##### 댓글별로 좋아요/싫어요 추가



### Url Conf(cast/urls.py)

```python
url(r'^(?P<pk>\d+)/comment-new/$', views.comment_new, name='comment_new'),
url(r'^comment/(?P<comment_pk>\d+)/edit/$', views.comment_edit, name='comment_edit'),
url(r'^comment/(?P<comment_pk>\d+)/delete/$', views.comment_delete, name='comment_delete'),
url(r'^comment-emotion/(?P<comment_pk>\d+)/$', views.comment_emotion, name='comment_emotion'),
```



### 댓글 모델 추가(cast/models.py)

- ##### 각 컨텐츠별(컨텐츠, 공약, 국회의원)로 댓글 기능을 추가하기 위해 유저와 각컨텐츠 사이에 M:N 관계를 위한 모델 추가

- ##### 각 댓글별로 감정표현을 추가하기 위해 `CommentEmotion` 모델 추가

```python
class Comment(models.Model):
    # 댓글 모델

    user = models.ForeignKey(settings.AUTH_USER_MODEL) # 해당 댓글을 쓴 유저와 1:N 관계 설졍
    contents = models.ForeignKey(Contents, default=None, null=True) # 컨텐츠에 댓글이 달릴 경우 관계 설정
    congressman = models.ForeignKey(Congressman, default=None, null=True) # 국회의원에 댓글이 달릴 경우 관계 설정
    pledge = models.ForeignKey(Pledge, default=None, null=True) # 공약에 댓글이 달릴 경우 관계 설정
    message = models.TextField() # 댓글 내용

    class Meta:
        ordering = ['-id']

    def __str__(self):
        return "{}의 댓글 {}".format(self.user, self.message)
   
class CommentEmotion(models.Model):
    # 댓글의 좋아요/싫어요

    LIKE_DISLIKE_CHOICE = (
        ('0', '선택안함'),
        ('1', '좋아요'),
        ('2', '싫어요'),
    ) # 감정 표현 종류

    user = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='comment_emotion_set') # 유저와 1:N 관계 설졍
    comment = models.ForeignKey(Comment) # 해당 댓글과 1:N 관계 설정
    name = models.CharField(max_length=2, default='0', choices=LIKE_DISLIKE_CHOICE) # 감정의 이름

    def __str__(self):
        return self.get_name_display() # name 필드의 Choice Value 값을 보여 준다.
```



### 댓글 기능 추가(cast/views.py)

- ##### 여러 컨텐츠 별로 요청을 처리하기 위해 `req_type `을 GET으로 받아와 분기 처리

- ##### 분기 처리 후 각 컨텐츠로 리디렉션 하기 위해 `redirect_path` 추가 

- ##### 댓글 쓰기

  ```python
  @login_required
  def comment_new(request, pk):
      # 각 컨텐츠내 댓글 쓰기

      req_type = request.GET.get('type','') # 요청한 컨텐츠 타입이 무엇인지
      redirect_path = request.GET.get('next','') # 해당 컨텐츠로 리디렉션 하기위한 url_path

      if request.method == 'POST':
          # 포스트 요청일 경우
          form = CommentForm(request.POST, request.FILES) # 받아온 데이터를 통해 폼 인스턴스 생성

          if form.is_valid():
              # 폼에 데이터가 유효할 경우
              comment = form.save(commit=False) # 디비에 저장하지 않고 인스턴스 생성
              comment.user = request.user # 댓글을 다는 유저 정보

              # 각 컨텐츠 별로 분기하여 인스턴스 생성
              if req_type == 'contents':
                  # 컨텐츠일 경우
                  contents = get_object_or_404(Contents, pk=pk)
                  comment.contents = contents
              elif req_type == 'pledge':
                  # 공약일 경우
                  pledge = get_object_or_404(Pledge, pk=pk)
                  comment.pledge = pledge
              else:
                  # 국회의원일 경우
                  congressman = get_object_or_404(Congressman, pk=pk)
                  comment.congressman = congressman

              comment.save() # 유저와 해당 컨텐츠 연결 후 디비에 저장
              return redirect(redirect_path)
      else:
          # 포스트 요청이 아닐 경우 빈 폼 생성
          form = CommentForm()

      return render(request, 'cast/comment_form.html', {
          'form' : form,
          }) # 포스트 요청이 아닐 경우 빈 폼으로 페이지 렌더링
  ```


- ##### 댓글 수정

  ```python
  @login_required
  def comment_edit(request, comment_pk):
      # 해당 댓글 수정
      comment = get_object_or_404(Comment, pk=comment_pk) # 해당 댓글 인스턴스
      redirect_path = request.GET.get('next','') # 해당 컨텐츠로 리디렉션 하기위한 url_path

      if comment.user != request.user:
          messages.warning(request, '댓글 작성자만 수정할 수 있습니다.')
          return redirect(redirect_path)

      if request.method == 'POST':
          form = CommentForm(request.POST, request.FILES, instance=comment)
          if form.is_valid():
              comment = form.save()
              messages.success(request, '기존 댓글을 수정했습니다.')
              return redirect(redirect_path)
      else:
          form = CommentForm(instance=comment)
      return render(request, 'cast/comment_form.html', {
          'form': form,
      })
  ```

- ##### 댓글 삭제

  ```python
  @login_required
  def comment_delete(request, comment_pk):
      # 해당 댓글 삭제
      comment = get_object_or_404(Comment, pk=comment_pk)
      redirect_path = request.GET.get('next','') # 해당 컨텐츠로 리디렉션 하기위한 url_path

      if comment.user != request.user:
          messages.warning(request, '댓글 작성자만 삭제할 수 있습니다.')
      else:
          comment.delete()
          messages.success(request, '댓글을 삭제했습니다.')
      return redirect(redirect_path)
  ```

- ##### 댓글 감정표현 

  ```Python
  def comment_emotion(request, comment_pk):
      # 해당 댓글의 좋아요/싫어요
      if request.is_ajax():
          # ajax 요청일 경우
          user = request.user # 현재 유저의 정보를 받아온다.
          comment = get_object_or_404(Comment, pk=comment_pk) # 현재 댓글 인스턴스 생성
          emotion_name = request.GET.get('emotion_name','') # url GET 정보에 담겨있는 즇아요/싫어요 정보를 받아온다.

          if user.comment_emotion_set.filter(comment=comment).exists():
              # 해당 댓글에 감정 표현을 이미 해놓은 경우
              user_emotion = user.comment_emotion_set.get(comment=comment) # 해당유저가 댓글에 해놓은 감정표현을 정보를 받아와
              if user_emotion.name == emotion_name:
                  # 같은 감정을 한번 더누르면 삭제.
                  CommentEmotion.objects.filter(comment=comment, user=user).delete() # 인스턴스 삭제
              else:
                  # 다른 감정을 누를 경우
                  user_emotion.name = emotion_name # 감정을 바꿔준 후 저장
                  user_emotion.save()
          else:
              # 감정표현을 처음 하는 경우 새롭게 생성
              CommentEmotion.objects.create(
                  user=user,
                  comment=comment,
                  name=emotion_name,
              )
          context = {}
          context['status'] = 'success'

      else:
          context = {}
          context['message'] = '잘못된 접근입니다.'
          context['status'] = 'fail'

      # dic 형식을 json 형식으로 바꾸어 전달한다.
      return HttpResponse(json.dumps(context), content_type='application/json')
  ```



### 템플릿(cast/contents_detail.html)

- ##### 댓글 추가 및 댓글 리스트 보여주기

  ```html
      <form action="{% url "cast:comment_new" contents.pk %}?next={{ request.path }}&type=contents" method="post">
          {% csrf_token %}
          {{ comment_form.message }}
          <input type="submit" class="btn btn-primary btn-block" value="댓글 쓰기" />
      </form>
      <ul>
          {% for comment in contents.comment_set.all %}
          <li>
              {{ comment.message }}
              <small>by {{ comment.user }}</small>
              {% if comment.user == user %}
                  <a href="{% url "cast:comment_edit" comment.pk %}?next={{ request.path }}">Edit</a>
                  <a href="{% url "cast:comment_delete" comment.pk %}?next={{ request.path }}" class="text-danger">Delete</a>
              {% endif %}
              <button type="button" class="comment-emo-btn btn btn-primary" id={{ comment.pk }} name="좋아요" value="1" style="color:white;">좋아요</button>
              <button type="button" class="comment-emo-btn btn btn-primary" id={{ comment.pk }} name="싫어요" value="2" style="color:white;">싫어요</button>
          </li>
          {% endfor %}
      </ul>
  ```

- ##### ajax 요청을 보내 처리한다.

  ```javascript
  <script type="text/javascript">
  	$('.comment-emo-btn').click(function(){
          var emotion_name = $(this).attr('value'); // 클릭한 요소의 attribute 중 value의 값을 가져온다.
          var comment_pk = $(this).attr('id');

          $.ajax({
              url: "/cast/comment-emotion/"+comment_pk+"/?emotion_name=" + emotion_name, // 통신할 url을 지정한다.
              data: {'csrfmiddlewaretoken': '{{ csrf_token }}'}, // 서버로 데이터를 전송할 때 이 옵션을 사용한다.
              dataType: "json", // 서버측에서 전송한 데이터를 어떤 형식의 데이터로서 해석할 것인가를 지정한다. 없으면 알아서 판단한다.

              success: function(response){
                  // 요청이 성공했을 경우 눌려있는 버튼 모양을 바꿔준다.
              },
              error:function(error){
                  // 요청이 실패했을 경우
              }
          });
      });
  </script>
  ```