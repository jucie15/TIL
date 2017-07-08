## 구현 기능

- ##### 태그 검색 기능

- ##### 검색 시 목록 자동완성 기능



## 검색 & 자동완성(Ajax) 기능

1. #####  Urls.py

   ```python
   	url(r'^tags/$', views.tagged_list, name='tagged_list'),
       url(r'^ajax/tag/autocomplete/$', 				views.ajax_tag_autocomplete, name='ajax_tag_autocomplete'),
   ```

2. ##### Views.py

   ```python
   def tagged_list(request):
       # 해당 태그가 포함 되어있는 전체 리스트
       tag = request.GET.get('tag','')

       pledge_list = Pledge.objects.filter(tag__icontains=tag) # 공약 리스트
       contents_list = Contents.objects.filter(tag__icontains=tag) # 콘텐츠 리스트
       congressman_list = Congressman.objects.filter(tag__icontains=tag) # 국회의원 리스트

       context = {}
       context['contents_list'] = contents_list
       context['pledge_list'] = pledge_list
       context['congressman_list'] = congressman_list

       return render(request, 'cast/search.html', context)

   def ajax_tag_autocomplete(request):
       # 태그 검색 자동완성 기능
       if request.is_ajax():
           # ajax 요청일 경우 실행
           term = request.GET.get('term','') # jquery autocomplete은 GET 방식으로 term이라는 키에 값을 넣어서 요청을 보내온다.
           tags = Tag.objects.filter(name__icontains=term)[:5] # tag_name에 key값이 포함되어 있는 리스트를 받아온다.
           results = []
           for tag in tags:
               # 검색된 태그 목록들을 json형식으로 넘겨주기 위해 사전형으로 구성된 자료로 바꾼 후 리스트에 담아놓는다.
               tag_json = {}
               tag_json['id'] = tag.id
               tag_json['label'] = tag.name
               tag_json['value'] = tag.name
               results.append(tag_json)
           data = json.dumps(results) # json형식으로 변환
           mimetype = 'application/json'
           return HttpResponse(data, mimetype)
   ```

3. ##### Templates(search.html)

   ```html
   <form id="form" method="GET" action="/cast/tags">
     <input type="text" id="id_tag" class="form-control search_web"  placeholder="검색해보세요" name='tag' value="{{ request.GET.tag }}" autocomplete="off" style="width:300px;" >
     <button type="submit" class="btn btn-primary" value="검색">검색</button>
   </form>

   <script type="text/javascript">
       $(document).ready(function() {
           $('#id_tag').autocomplete({
               minLength: 2, // 자동완성 최소 글자 수
               source:"{% url 'cast:ajax_tag_autocomplete' %}",
               focus: function( event, ui ) {
                   // 목록에 포커싱 됐을 때 실행될 함수
                   return false; // 포커싱 된 목록을 선택할 것인가
               }
           });
   });
   ```

   ​

