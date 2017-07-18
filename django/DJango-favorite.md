# DJango 즐겨찾기 기능



### 구현 기능 

1. ##### 유저가 마음에 드는 컨텐츠를 즐겨찾기 한다.



### Urls Conf(cast/urls.py)

```python
    url(r'^ajax/favorites/(?P<pk>\d+)/$', views.ajax_favorites, name='ajax_favorites'),
```



### Model(cast/models.py)

```python
class Favorite(models.Model):
    # 즐겨찾기

    user = models.ForeignKey(settings.AUTH_USER_MODEL) # 유저와 1:N 관계 생성
    contents = models.ForeignKey(Contents, default=None, null=True) # 컨텐츠와 1:N 관계 생성
    pledge = models.ForeignKey(Pledge, default=None, null=True) # 공약과 1:N 관계 생성
    congressman = models.ForeignKey(Congressman, default=None, null=True) # 국회의원 1:N 관계 생성

    def __str__(self):
        if self.contents != None:
            return self.contents.title
        elif self.pledge != None:
            return self.pledge.title
        else:
            return self.congressman.name
```

### View(cast/views.py)

- ##### view에서 ajax 처리

```python
def ajax_favorites(request, pk):
    # 즐겨찾기 버튼 클릭 시
    if request.is_ajax():
        # ajax 요청일 경우
        user = request.user
        req_type = request.POST.get('type','')

        isFavorite = False # 즐겨찾기 상태를 확인할 변수

        if req_type == 'contents':
            # 컨텐츠에서 요청이 왔을 경우
            contents = get_object_or_404(Contents, pk=pk) # 컨텐츠 인스턴스 생성
            if user.favorite_set.filter(contents=contents).exists():
                # 유저가 이미 해당 컨텐츠를 즐겨찾기 추가해놨으면 삭제
                Favorite.objects.filter(contents=contents, user=user).delete()
                isFavorite = False # 즐겨찾기 해제
            else:
                # 추가되어 있지 않았다면 추가
                Favorite.objects.create(
                    user=user,
                    contents=contents,
                    )
                isFavorite = True # 즐겨찾기 추가
        elif req_type == 'pledge':
            # 공약에서 요청이 왔을 경우
            pledge = get_object_or_404(Pledge, pk=pk) # 공약 인스턴스 생성
            if user.favorite_set.filter(pledge=pledge).exists():
                # 유저가 이미 해당 공약을 즐겨찾기 추가해놨으면 삭제
                Favorite.objects.filter(pledge=pledge, user=uesr).delete()
                isFavorite = False

            else:
                # 추가 되어 있지 않다면 추가
                Favorite.objects.create(
                    user=user,
                    pledge=pledge,
                    )
                isFavorite = True
        else:
            # 국회의원 페이지에서 요청이 왔을 경우
            congressman = get_object_or_404(Congressman, pk=pk) # 의원 인스턴스 생성
            if user.favorite_set.filter(congressman=congressman).exists():
                # 유저가 이미 해당 국회의원을 즐겨찾기에 추가해놨으면 삭제
                Favorite.objects.filter(congressman=congressman, user=user).delete()
                isFavorite = False

            else:
                # 추가되어있지 않았다면 추가
                Favorite.objects.create(
                    user=user,
                    congressman=congressman,
                    )
                isFavorite = True

        context = {}
        context['isFavorite'] = isFavorite
        context['status'] = 'success'
        data = json.dumps(context) # json 형식으로 파싱
    else:
        data = json.dumps({
            'status': 'fail',
            }) # json 형식으로 파싱
    mimetype = 'application/json'
    return HttpResponse(data, mimetype)
```



### Templates(cast/contents_detail.html)

- ##### 즐겨찾기 버튼 

```html
{% if user_is_favorite == True %}
	<li><a id="favorite" style='color:red'>
{% else %}
	<li><a id="favorite" style='color:gray'>
{% endif %}

<i class="fa fa-heart-o fa-lg fa-fw"  aria-hidden="true" ></i></a>
```

- ##### 즐겨찾기 버튼 클릭 시 ajax처리 부

```Javascript
// 즐겨찾기 추가(ajax 처리)
    $('#favorite').click(function(){
        $.ajax({
            type: "POST", // POST 요청으로 한다.
            url: "{% url 'cast:ajax_favorites' contents.pk %}", // 통신할 url을 지정한다.
            data:
            {
                'csrfmiddlewaretoken': '{{ csrf_token }}',
                'type': 'contents',
            }, // 서버로 데이터를 전송할 때 이 옵션을 사용한다.
            dataType: "json", // 서버측에서 전송한 데이터를 어떤 형식의 데이터로서 해석할 것인가를 지정한다. 없으면 알아서 판단한다.

            success: function(data){
                // 요청이 성공했을 경우 눌려있는 버튼 모양을 바꿔준다.
                if(data.isFavorite == true)
                {
                    $('#favorite').css('color','red');
                }
                else
                {
                    $('#favorite').css('color','gray');
                }
            },
            error:function(error){
                // 요청이 실패했을 경우
                console.log(error)
            }
        });
    });
```