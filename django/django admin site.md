## [django admin site](http://wiki.dano.me/display/DJAN/django+admin+site)

### 1.ModelAdmin ( from django.contrib import admin.ModelAdmin)

장고 db models 를 관리하는 admin 인터페이스 클래스.

보통 장고 프로젝트 내의 각 앱내 admin.py에 기술함.

| ModelAdminOptions & Method            | value type &method params                | description                              | example                                  | etc                                      |
| ------------------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| **Options**                           |                                          |                                          |                                          |                                          |
| actions                               | 액션 메서드 리스트문자열과 메서드 자체 모두 가능.             | 목록 페이지에서 사용 할 수 있는 액션 목록.여기서 액션이라함은, 개발자가 정의한 메서드로, 목록페이지에서 선택 한 쿼리셋을 가지고, 특정 행동을 취할 수 있도록 하는 것이다.활용 예로, 배송상태를 변경하는 기능을 목록페이지에서 구현이 가능 한 것 | def make_published(modeladmin, request, queryset):     queryset.update(status='p') make_published.short_description = "Mark selected stories as published"  class ArticleAdmin(admin.ModelAdmin):     list_display = ['title', 'status']     ordering = ['title']     actions = [make_published] | <https://docs.djangoproject.com/en/1.10/ref/contrib/admin/actions/>예제의 설명은, actions 에 등록한 make_published 메서드는, 목록 페이지에서 선택한 row 쿼리셋들을 특정 필드 여기서는 status 가 되고, 이를 update 하는 기능이다. |
| actions_on_top,actions_on_bottom      | boolean                                  | 액션바가 목록페이지에서, 상단 또는 하단에 표시할지에 대한 여부      |                                          |                                          |
| actions_selection_counter             | boolean                                  | 액션바 옆에 선택된 row 들의 카운트를 표기해줄지에 대한 여부 ( default is True ) |                                          |                                          |
| date_hierarchy                        | DateField or DateTimeField               | 모델어드민에 대응되는 모델의 datefield, datetimefield 중 대응해두는 값에 따라 지정된 날짜로, 계층 필터링이 목록페이지에 표기되고, 이를 선택하면 자동으로 표시되는 리스트가 변경된다. |                                          | 표준시간대를 사용하는 경우 <https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-USE_TZ>해당 문서를 확인해야함. 요는 프로젝트 settings 의 USE_TZ = True 로 해두어야, 현지시간이 아닌 표준시간을 기반으로 처리가 된다는 것. |
| empty_value_display                   | string                                   | 빈 필드인 경우 대체 표기될 문구 기본은 '-' 로 표기된다.       | 아래와 같이 지정된 필드에 따로 설정도 가능하다.`class AuthorAdmin(admin.ModelAdmin):    fields = ('name', 'title', 'view_birth_date')    def view_birth_date(self, obj):        return obj.birth_date    view_birth_date.empty_value_display = '???'` | django >= 1.9                            |
| exclude                               | models field tuple                       | 모델의 추가 / 수정 되는 폼에 제외될 모델의 필드를 나열하면, 폼에 표시가 되지 않는다. |                                          |                                          |
| fields                                | models field tuple                       | 모델의 추가 / 수정 되는 폼에 표기될 모델의 필드를 나열하면, 폼에 표시가 되며, 그 순서와 그룹을 간단히 조절 할 수있다. 여기서 그룹은, 기본설정에서 한라인에 표기되도록 하는 것을 의미한다.또한 readonly_fields 의 필드와 함께 표기된다. fieldsets 옵션에 쓰이는 fields 딕셔너리 키와 혼돈해서는 안됨. fields 와 fieldsets 옵션이 없는 ModelAdmin 인 경우, django는 수정이 가능한(editable=True) 필드들을 single fieldset 으로 기본적으로 표시한다. 그 순서는 models 에 정의된 순서대로이다. |                                          |                                          |
| fieldsets                             | fieldset tuple                           | 모델의 추가 / 수정 되는 폼(페이지)의 레이아웃을 제어하도록 fieldset(필드셋)을 설정하는 옵션.<fieldset >은 폼에서의 하나의 'section' 이다. 이 'section'의 구성을 fieldset 옵션에 설정하는 것.fieldsets은 tow-tuples 리스트로 담기는데, 그 포멧은 (name, field_options) 이고, 여기서 name 은 <fieldset> 의 제목이다. field_options는 fieldset에 대한 정보 딕셔너리이다. 이 정보 딕셔너리에는 표시될 model 필드가 표기된다.field_options 딕셔너리에는 다음과 같은 key가 있을 수 있음.fields : 해당 fieldset에 표기할 필드명의 튜플이며, 반드시 작성해야하는 key이다. 마찬가지로 한 라인에 표기하기 위해서는 한번더 튜플로 감싸면 된다. readonly_fields에 정의된 필드 값들도 포함하여 읽기전용으로 표시할 수 있다.classes : fieldset에 적용 할 추가 css 클래스가 포함된 튜플. 보통 쓰이는 클래스는 이미 정의된 collapse 와 wide 이며, 각각 세로확장, 가로확장의 기능이다.description : fieldset의 설명 문구를 표기 할 수 있음. TabularInline 에는 레이아웃 문제로 랜더링 되지 않는다. | `from django.contrib import adminclass FlatPageAdmin(admin.ModelAdmin):    fieldsets = (        (None, {            'fields': ('url', 'title', 'content', 'sites')        }),        ('Advanced options', {            'classes': ('collapse',),            'fields': ('registration_required', 'template_name'),        }),    )` |                                          |
| filter_horizontal                     | ManytoManyField                          | ManyToManyField 에 의해 다중 셀렉트 박스로 필터링을 할 수 있는 필터링 정의 옵션. |                                          |                                          |
| filter_vertical                       | ManytoManyField                          | filter_horizontal 과 동일한데, 수직 표시가된다.      |                                          |                                          |
| form                                  | ModelForm                                | 모델을 위해 동적으로 생성되는 ModelForm 을 대입하여, 모델의 추가 / 수정시에 기본 폼의 행동을 재정의하여 제공한다.또는 해당 옵션을 사용하지않고, get_form() 메서드를 오버라이딩 하여, 폼을 구성할때 호출되는 get_form() 메서드를 재정의할때, 커스터마이징 할 수 있다. ModelForm의 Meta.model 를 정의 할 경우, 반드시 Meta.fields (또는 Meta.exclude) 또한 정의해야한다. 하지만 ModelAdmin 이 필드를 가지고 있으면, Meta.fields 는 무시된다. 만일 관리를 위해서만 사용되는 경우라면, Meta.model 속성을 생략하면 된다. ModelAdmin이 제공하기 때문. 또는 ModelForm 의 Meta 클래스 안에 fields을 정의하여, 안전하게 유효성 검사를 할 수 있다.만일 ModelForm 과 ModelAdmin 이 둘다 exclude 옵션을 정의하면, ModelAdmin 이 우선 적용 된다. |                                          | <https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#admin-custom-validation> |
| formfield_overrides                   | dictionary                               | 일부 폼 필드의 옵션을 (빠르고, 쉽게) 재정의 할 수 있는 옵션. 딕셔너리로 정의하며, 매핑되는 필드 클래스에 대해 딕셔너리로 설정한다.가장 일반적인 용도로 지정되는 특정 유형의 필드에 대해 커스텀 위젯을 설정하는 것. 딕셔너리의 키는 스트링이 아닌, 실제 필드 클래스이다. 키의 값은 또다른 딕셔너리이며, 여기에 전달되는 값들이 폼 필드 클래스의 __init__ 메서드로 전달된다. 자세한건 <https://docs.djangoproject.com/en/1.10/ref/forms/api/> 참조. | `# Import our custom widget and our model from where they're definedfrom myapp.widgets import RichTextEditorWidgetfrom myapp.models import MyModelclass MyModelAdmin(admin.ModelAdmin):    formfield_overrides = {        models.TextField: {'widget': RichTextEditorWidget},    }` | 만일 커스텀 위젯을 관계 필드(foreignkey, manytomanyfield)에 사용하려면, 해당 필드명이 raw_id_fields 또는 radio_fields 에 포함하지 않았는지 확인해라. 해당 옵션을 통해서는 앞서말한 옵션에 설정한 필드의 위젯을 변경 할 수 없다.raw_id_fields, radio_fileds는 자체 커스텀 위젯이다. |
| inlines                               |                                          | 아래 2.1 항목을 참조.                           |                                          | See [`InlineModelAdmin`](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.InlineModelAdmin) objects below as well as [`ModelAdmin.get_formsets_with_inlines()`](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.ModelAdmin.get_formsets_with_inlines). |
|                                       |                                          |                                          |                                          |                                          |
| list_display                          | tuple                                    | 모델의 목록 페이지에 표시되는 필드를 제어 할 수 있는 옵션.지정 하지 않으면, Model 의 __unicode__, __str__ 메서드의 리턴 값이나, 이도 정의 되지않으면 Object로 표시되며,튜플로 지정 할 수 있는 값의 타입은 4가지가 있다.필드명, 모델 인스턴스에 대한 하나의 파라미터를 응답받는 메서드, 모델어드민에 정의된 앞에 기술한것과 같은 메서드 스트링, 모델에 정의한 메서드 스트링 (이때는 ModelAdmin이 아닌 Model 이 self로 갖고있는 차이가 있다)ForeignKey 의 경우 __unicode__ 가 표시되고, ManyToManyField 의 경우 지원되지 않는다 이는 테이블의 각 행에 대해 별도의 쿼리문을 실행해야하기 때문이고, 표시하기를 원한다면 맞춤 메서드를 만들어 넣어야한다.BooleanField, NullBooleanFiled 인 경우 True / False 가 아닌 아이콘으로 표기됨. 주어진 스트링이 모델의 메서드나 ModelAdmin 의 메서드라면, 기본적으로 표기되는 string 말고도, format_html() 메서드를 이용해서 html 태그로 묶인 내용을 표기 할 수 도있다. 메서드에 short_description 속성을 지정하여 설명을 추가 할 수 있다.메서드에 empty_value_display 속성을 지정하여 빈값이나 널값인 경우 표기해줄 문구를 지정 할 수 있다.메서드의 boolean 속성을 True로 지정하면 아이콘으로 표기됨tuple에 __unicode__ 메서드명을 넣으면, 모델의 __unicode__가 이용된다.일반적으로 실제 디비의 필드가 아닌 요소는 정렬을 못한다. 단, 메서드화 하여, admin_order_field 에 정렬 요소를 담으면 가능하다. |                                          |                                          |
| list_display_links                    | tuple                                    | 해당 옵션을 통해, 목록에서 수정(상세)페이지로 이동하는 링크를 어떠한 모델의 필드에 링크를 걸지를 설정 할 수 있다.기본적으로 첫번재 열에 있는 필드가 링크로 이동되게 되는데, 지정하는 모든 list_display에 속성들에 지정도 가능하고, 아예 링크 이동이 불가하도록 None 설정을 할 수 도있다. |                                          |                                          |
| list_editable                         | tuple                                    | 수정(상세)페이지에서 수정을 할 수 있는 list_display 옵션을 지정할 수 있다.이 뜻은, 한 필드 한필드의 수정이 아닌, 통째로의 수정이 가능한 형식을 제공한다는 이야기이다. first_name, last_name 이 존재할때, full_name 이라는 메서드를 만들어 list_display와 list_editable에 지정하면, 한꺼번에 수정이 가능하다는 이야기.주의할 것은 list_editable 과 list_display_links 에는 동일한 필드가 들어가서는 안된다. 즉, 수정가능한 옵션이라고 설정한 필드는 링크가 들어가선 안됨. |                                          |                                          |
| list_filter                           | tuple or list                            | 목록화면 우측에 모델 목록을 필터링 하는 사이드바를 생성하는 기능이다.들어갈수있는 요소로는 - 필드명, 관계필드의 관계된 값도 이용 가능- django.contrib.admin.SimpleListFilter 를 상속받은 클래스 ( title, parameter_name 속성을 제공해야하고, lookups 와 queryset 메서드를 오버라이딩 해야함. -  튜플로 들어갈 수도있다. 첫번째 요소로 필드명, 두번째 요소로 FieldListFilter 클래스를 상속받는 클래스가 들어 갈 수 있다. (이는 관계요소를 첫번째 요소에 두고, RelatedOnlyFieldListFilter 를 이용하면 모들 관계테이블의 요소가 보이는게 아니라, 실제 관계된 값들만 표기된다) |                                          | SimpleListFilter 에 대한 설명중 체크하면 좋을 정보로는 lookups에 HttpRequest와 ModelAdmin 이 전달된다. 이는 request에서 슈퍼유저인지를 확인하는 request.user.is_superuser 값으로 작동여부를 판가름 할수있고, model_admin 에서 필터링할 값에 대한 필드가 실제 존재하는지 체크하여 없는 값은 필터 메뉴에 표기조차 않을 수 있다.기본적으로 필터는 두가지 이상의 선택항목이 존재하는 경우에 나타난다. 이를 필터클래스의 has_output() 메서드가 표기 여부를 제어한다.필터의 템플릿도 커스텀이 가능하며, 이를 위해서는 필터 클래스의 template 속성에 템플릿을 지정하면 된다. (장고 기본템플릿의 admin/filter.html 을 참조할것) |
| list_max_show_all                     | int                                      | 리스트 페이지의 "모두 표시 (Show all)"에 의해 표시될 항목의 수. 리스트의 결과 수가 해당 설정보다 작거나 같은 경우에 "모두 표시" 링크가 표시되며, 기본값은 200이다. |                                          |                                          |
| list_per_page                         | int                                      | 한 페이지당 표기될 목록의 수. 기본값은 100이다.            |                                          |                                          |
| list_select_related                   | boolean, tuple or list                   | 해당 옵션을 이용하면, django 에서 모델리스트를 가지고올때, [select_related()](https://docs.djangoproject.com/en/1.10/ref/models/querysets/#django.db.models.query.QuerySet.select_related) 를 호출하여 가지고온다. 이는 db의 중복 쿼리를 막을 수 있는 메서드이다.기본적으로 False로 셋팅되어있고, 이는 장고에서는 list_display 에 존재하는 관계 모델만 select_related()로 가져온다. True로 처리해두면, 모든 관계 모델을 select_related()로 가지고온다.세분화된 조정을 위해서 tuple, list로 입력을 해두면 된다.요청에 따른 동적 조정이 필요하다면, get_list_select_related() 메서드를 구현하면 된다. | `# Hits the database.e = Entry.objects.get(id=5)# Hits the database again to get the related Blog object.b = e.blog````# Hits the database.e = Entry.objects.select_related('blog').get(id=5)# Doesn't hit the database, because e.blog has been prepopulated# in the previous query.b = e.blog` |                                          |
| ordering                              | tuple                                    | 목록의 표시에 정렬할 필드를 지정하여 표시가 가능하며, 요청에 따른 동적 정렬이 필요한경우 get_ordering() 메서드를 이용하면 됨. |                                          |                                          |
| paginator                             | Paginator                                | 페이지네이션을 커스터마이징 하여 사용할때 필요한 옵션.           |                                          |                                          |
| prepopulated_fields                   | dictionary                               | modles.SlugField 과 함께 사용되는 옵션.<https://docs.djangoproject.com/en/1.10/ref/models/fields/#slugfield>[DateTimeField 나 관계 필드들은 허용 하지 않음.](https://docs.djangoproject.com/en/1.10/ref/models/fields/#slugfield) | `class ArticleAdmin(admin.ModelAdmin):    prepopulated_fields = {"slug": ("title",)}` |                                          |
| preserve_filters                      | boolean                                  | 필터를 보존할지에 대한 옵션. 생성 수정 삭제 후에 필터를 보존할지에 대한 여부이다. True 로하면 필터가 유지되어있다. False는 필터가 초기화되어있게 된다. |                                          |                                          |
| radio_fields                          | dictionary                               | 장고에서는 포링키와 초이스 세트에 대해서는 셀렉트박스를 지원하는데, 해당 옵션에 이들을 설정해두면, 라디오버튼 인터페이스로 처리된다.필드에 admin.VERTICAL, admin.HORIZONTAL 값을 할당하여 정렬 방식을 지정. | `class PersonAdmin(admin.ModelAdmin):    radio_fields = {"group": admin.VERTICAL}` |                                          |
| raw_id_fields                         | tuple                                    | 장고에서는 기본적으로 ForeignKey, ManyToManyField 에 대해 select 박스 인터페이스를 제공하는데, drop-down으로 모든 관계된 인스턴스를 보여주는 것에 오버헤드가 발생하는것을 원치 않을때, 해당 옵션을 이용하여 Input 위젯을 변경할 수 있다.primary key 로 입력하게되는 input widget 으로 표기되고, manytomanyfield 인경우에는 comma(,) 로 지정 할 수 있게 된다. |                                          |                                          |
| readonly_fields                       | tuple                                    | 지정된 필드는 읽기전용으로 표시된다. 단, fields 나 fieldsets 에 해당 필드가 포함되어있어야한다.list_display 와 마찬가지로 메서드를 가지고도 읽기전용 필드를 지정 할 수 있다. |                                          |                                          |
| save_as                               | boolean                                  | True 로 설정하면,"Save and add another" 버튼이 "save as new" 버튼으로 교체된다. 기본값은 False |                                          |                                          |
| save_as_continue                      | boolean                                  | False 로 설정하면, 저장후 목록 페이지로 리다이렉트 된다.기본은 True로 저장후 계속 편집화면으로 유지된다. |                                          |                                          |
| save_on_top                           | boolean                                  | True 로 설정하면, 저장버튼이 상단에 노출된다. 기본은 False.  |                                          | 하단에는 True로 해두어도 무조건 저장버튼이 표시된다. 즉 상단, 하단 모두 표시된다는 이야기. |
| search_fields                         | tuple                                    | 목록 페이지에서 검색 기능을 지원하기위해서 사용되는 옵션으로,지정되는 CharField, TextField 에 대해 검색을 할 수 있다. 관계 모델의 필드들도 지정 가능하다. 실제 SQL 의 where 절에는, 지정된 필드만큼의 or 구문으로 이어지고,검색시 단어만큼의 and 구문으로 묶인다. 쿼리의 성능 개선을 위해 제한을 둘 수가 있는데,^ : %를 제거한다. ex) '^first_name' 으로 search_fields에 지정하면, where 절에는 ILIKE 'john%' 로 실행된다.= : 양옆의 %를 제거. ex) '=first_name' , where ILIKE 'john'@ :  full text match 이다. 기본 검색 메서드를 이용하지만, 인덱스를 사용하는 것에 차이가 있고, 현재는 MySql 에서만 적용이 된다.[ModelAdmin.get_search_results()](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.ModelAdmin.get_search_results) 를 통해 커스터마이징을 하여 검색 동작을 제공 할 수도있다. | `ex_1)search_fields = ['foreign_key__related_fieldname']search_fields = ['user__email']``ex_2)WHERE (first_name ILIKE '%john%' OR last_name ILIKE '%john%')AND (first_name ILIKE '%lennon%' OR last_name ILIKE '%lennon%')` | 옆의 예에서 where 절을 설명하자면, search_fields = ['first_name', 'last_name'] 으로 검색 필드를 지정후에,목록페이지 검색 input 에 john lennon 으로 검색을 한 쿼리의 where 절이다.필드 수만큼 or 절로 묶이고, 검색 단어 수만큼 and 단어로 묶인다. 때문에 쿼리 성능 이슈가 발생할 가능성이 있음. |
| show_full_result_count                | boolean                                  | 조회한 로우 행의 카운트를 표시할지에 대한 여부. 기본으로 True로 셋팅되어있고, True 시에 테이블에 행이 많이 포함된 경우, 비용이 많이 들수있는 테이블에 대해 풀카운트를 조회하는 쿼리를 생성하여 사용한다. |                                          |                                          |
| view_on_site                          | boolean, 메서드구현                           | True, False 로 model 의 get_absolute_url(self) 메서드에서 리턴되는 링크로 이동하는 (사이트에서보기) 버튼을 상세 페이지에서 표시할수있다.admin 내에 view_on_site 메서드를 구현하면 자동적으로 대응되는 링크로 버튼이 생성된다. | `class Person(admin.Model):    def get_absolute_url(self):        return 'https://example.com'``class PersonAdmin(admin.ModelAdmin):    def view_on_site(self, obj):        url = reverse('person-detail', kwargs={'slug': obj.slug})        return 'https://example.com' + url` |                                          |
| 아래 6개 항목은 커스텀 템플릿을 위한 옵션들             |                                          | [Overriding admin templates](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#admin-overriding-templates) 하는 방법에 대한 문서를 자세히보아야함. |                                          |                                          |
| add_form_template                     |                                          | admin 의 add_view() 에서 사용되는 템플릿.          |                                          |                                          |
| change_form_template                  |                                          | admin 의 change_view() 에서 사용되는 템플릿.       |                                          |                                          |
| change_list_template                  |                                          | admin 의 changelist_view() 에서 사용되는 템플릿.   |                                          |                                          |
| delete_confirmation_template          |                                          | admin 의 delete_view() 에서 사용되는 템플릿. 하나 이상의 모델을 삭제하는 것을 확인하는 페이지용. |                                          |                                          |
| delete_selected_confirmation_template |                                          | admin 의 delete_selected() 에서 사용되는 템플릿.   |                                          |                                          |
| object_history_template               |                                          | admin 의 history_view() 에서 사용되는 템플릿.      |                                          |                                          |
|                                       |                                          |                                          |                                          |                                          |
| **Methods**                           |                                          |                                          |                                          |                                          |
| save_model                            | request, model_obj, form, change         | 저장 요청에 따라 호출되는 메서드.총 4가지의 파라미터와 함께 모델의 저장(수정)시에 호출되는 메서드로, 오버라이딩 시에 반드시 모델의 저장 동작을 진행해야하며, 저장 전/후 추가 작업을 진행하기 위해 사용되어야한다.super().save_model()을 호출하여, Model.save() 메서드를 이용하여 저장한다. |                                          |                                          |
| delete_model                          | request, model_obj                       | 삭제 요청에 따라 호출되는 메서드.request 와 model 만 전달되며, 삭제 전후 처리시 필요한 작업을 위해 오버라이딩 하는 메서드.super().delete_model()을 호출하여, Model.delete() 메서드로 이용하여 삭제. |                                          |                                          |
| save_formset                          | request,form,formset,change              | 부모? ModelForm 과 formset 의 변화에 따라 전후처리를 할 수 있다?[Saving objects in the formset](https://docs.djangoproject.com/en/1.10/topics/forms/modelforms/#saving-objects-in-the-formset) | ModelForm 과 FormSet 에 대해 확인 후 보다 정확히 정리 필요 |                                          |
| get_ordering                          | request                                  | 정렬 요청에 따라 호출되는 메서드, 리턴 시키는 데이터를 ordering 속성에 지정하는 것과 같은 형식으로 처리하게되면 해당 정렬 요청에 대해 커스텀이 가능하다. |                                          |                                          |
| get_search_results                    | request, queryset, search_term           | 검색시 검색에 사용될 전체 쿼리셋과 검색어가 전달된다. 여기에 커스텀한 결과 쿼리셋을 리턴 하면, 검색결과가 표시된다.디비 조회 쿼리에 대한 성능 개선을  할 수 있는 부분임. |                                          |                                          |
| save_related                          | request, form, formsets, change          | 모델의 정보가 저장 후 호출되는 related 오브젝트의 변화, 생성에 호출되는 메서드로, 이 related 오브젝트의 저장 전/후에 처리할 수있음. formsets 에는 inline formset 리스트가 전달되는 것이고, 이 메서드가 호출되는 시점에는 이미 부모 즉 ModelAdmin 이 갖는 모델의 변화가 저장된 후임을 명심. |                                          |                                          |
| get_readonly_fields                   | request,obj=None                         | 읽기 전용으로 표시될 필드를 리턴 시키면, 읽기 전용으로 표기된다. 리턴 형태는 물론 tuple or list. |                                          |                                          |
| get_prepopulated_fields               | request,obj=None                         | prepopulated_fields 옵션을 재정의 해줄수있는 메서드 리턴 형태는 dictionary |                                          |                                          |
| get_list_display                      | request                                  | list_display 옵션에 확장하여, 리턴 형태를 조정하여, 목록화면에서 표기될 항목을 수정 할 수 있다.이들은, request 에 따라 달리 보여야하는 부분들에대해서 재정의하는 용도가 일반적인 사용 형태이다. |                                          |                                          |
| get_list_display_links                | request, list_display                    | ModelAdmin.get_list_display() 메서드에 의해 리턴되는 list_display (tuple or list) 와 request가 전달되고, 이들 중 링크를 포함시킬 부분을 수정 할 수 있다. |                                          |                                          |
| get_fields                            | request,obj=None                         | fields 부분을 편집할수있는 메서드이다.                 |                                          |                                          |
| get_fieldsets                         | request,obj=None                         | fieldsets 부분을 편집 할 수 있는 메서드.             |                                          |                                          |
| get_list_filter                       | request                                  | list_filter 부분을 편집 할 수 있는 메서드.           |                                          |                                          |
| get_list_select_related               | request                                  | list_select_related 부분을 편집 할 수 있는 메서드.   |                                          |                                          |
| get_search_fields                     | request                                  | search_fields 부분을 편집 할 수 있는 메서드.         |                                          |                                          |
| get_inline_instances                  | request,obj=None                         | InlineModelAdmin 객체들의 리스트 또는 튜플을 리턴하여, inlines 옵션을 수정 할 수 있는 메서드.[InlineModelAdmin](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.InlineModelAdmin) 참조 문서의 예로는 request 를 통해 권한을 확인하여, 편집 할수있는 InlineModelAdmin을 지정해주는 역할에 쓰인다고함. 해당 메서드를 오버라이딩 할때, inlines 속성에 정의한 클래스의 인스턴스인지 확인해야한다 정의되지 않은 관계된 객체를 추가하면 Bad Request 에러를 볼 수 있다. |                                          |                                          |
| get_urls                              | not exists                               | URLConf 방식으로 ModelAdmin 에 쓰일 url 을 리턴한다.페이지가 열릴때 호출되는 것이 아니라 장고가 로드 될때 호출됨. function 을 이용해서 URLConf 를 설정할경우, 권한에 대한 이슈와 캐싱이 될 수 있는 이슈를 기억해야한다. 이를 위해 AdminSite.admin_view() 메서드를 지원하고있는데. 옆의 예제가 그러하다.만일 캐시를 허용할건데, 권한 검사는 해야하는 경우에 admin_view 메서드의 인자로 cacheable=True로 전달하면 된다. | `class MyModelAdmin(admin.ModelAdmin):    def get_urls(self):        urls = super(MyModelAdmin, self).get_urls()        my_urls = [            url(r'^my_view/$', self.admin_site.admin_view(self.my_view))        ]        return my_urls + urls`` def my_view(self, request):        # ...        context = dict(           # Include common variables for rendering the admin template.           self.admin_site.each_context(request),           # Anything else you want in the context...           key=value,        )        return TemplateResponse(request, "sometemplate.html", context)` |                                          |
| get_form                              | request,obj=None,kwargs                  | 모델이 추가 혹은 변경되는 페이지에서 쓰일 ModelForm 클래스를 리턴 하여 커스텀이 가능하다.이는 add_view(), change_view() 메서드와 함께 참조하면 된다. 일반적인 사용법은 경우에 따라 폼의 양식을 변경해야할경우에 쓰인다, 예로 슈퍼유저에게는 더 변경이 가능하도록 fields를 수정한 폼을 제공함으로써 수정이 가능토록 하는것이 그 예이다. | `class MyModelAdmin(admin.ModelAdmin):    def get_form(self, request, obj=None, **kwargs):        if request.user.is_superuser:            kwargs['form'] = MySuperuserForm        return super(MyModelAdmin, self).get_form(request, obj, **kwargs)` |                                          |
| get_formsets_with_inlines             | request,obj=None                         | (FormSet, InlineModelAdmin) 를 가지고 표기해야할 인라인을 지정 할수있다.우측 예제는 모델을 추가하는 페이지에서는 MyInline을 표기하지 않도록 처리한 예제이다. | `class MyModelAdmin(admin.ModelAdmin):    inlines = [MyInline, SomeOtherInline]    def get_formsets_with_inlines(self, request, obj=None):        for inline in self.get_inline_instances(request, obj):            # hide MyInline in the add view            if isinstance(inline, MyInline) and obj is None:                continue            yield inline.get_formset(request, obj), inline` |                                          |
| formfield_for_foreignkey              | db_field,request,kwargs                  | 모델의 foreignkey 필드의 기본 formfield 를 '대체' 할 수 있다.formfield를 대체 할수있다는 말이 와닿지 않지만, kwargs 에 담아서 처리할수있는 속성들에 대해 변경이 가능하다고 생각하면됨. db_field 는 포링키와 관련된 각 필드의 정보가 오는 것임.간단히 이와같이 이해하면 됨. → kwargs 의 쿼리셋(queryset)을 지정하여 표기할수있음. | `class MyModelAdmin(admin.ModelAdmin):    def formfield_for_foreignkey(self, db_field, request, **kwargs):        if db_field.name == "car":            kwargs["queryset"] = Car.objects.filter(owner=request.user)        return super(MyModelAdmin, self).formfield_for_foreignkey(db_field, request, **kwargs)` |                                          |
| formfield_for_manytomany              | db_field,request,kwargs                  | 모델의 manytomanyfield와 연관되어있고 이들의 formfield를 재정의 할 수 있다.마찬가지로 쿼리셋을 조정하여 표시될 목록을 바꿀 수 있다. | `class MyModelAdmin(admin.ModelAdmin):    def formfield_for_manytomany(self, db_field, request, **kwargs):        if db_field.name == "cars":            kwargs["queryset"] = Car.objects.filter(owner=request.user)        return super(MyModelAdmin, self).formfield_for_manytomany(db_field, request, **kwargs)` |                                          |
| formfield_for_choice_field            | db_field,request,kwargs                  | 모델의 필드에 choices 속성으로 지정한 formfield 를 재정의 할 수 있음.단, 주의할 점은 모델에서 정의해준 값들에 한해서만 choices를 재정의 할 수 있다. 그렇지 않으면 ValidationError 를 저장시 발생시킨다. | `class MyModelAdmin(admin.ModelAdmin):    def formfield_for_choice_field(self, db_field, request, **kwargs):        if db_field.name == "status":            kwargs['choices'] = (                ('accepted', 'Accepted'),                ('denied', 'Denied'),            )            if request.user.is_superuser:                kwargs['choices'] += (('ready', 'Ready for deployment'),)        return super(MyModelAdmin, self).formfield_for_choice_field(db_field, request, **kwargs)` |                                          |
| get_changelist                        | request,kwargs                           | 목록화면을 표시하는데 사용될 ChangeList 클래스를 반환하여 오버라이딩 할 수 있다.기본적으로 django.contrib.admin.views.main.ChangeList 를 사용하고있다. |                                          |                                          |
| get_changelist_form                   | request,kwargs                           | 목록화면의 Formset 에서 사용할 ModelForm 클래스를 반환시켜 재정의 할 수 있음.**위 ChangeList와의 연관관계 파악 필요** | `from django import formsclass MyForm(forms.ModelForm):    passclass MyModelAdmin(admin.ModelAdmin):    def get_changelist_form(self, request, **kwargs):        return MyForm` | 만일 ModelForm 에서 Meta 클래스의 model 을 지정한다면, 반드시 Meta 클래스내에 fields 또는 exlcude 속성도 정의해야한다.하지만, 이보다 더 우선순위에있는 ModelAdmin 이 무시하게된다, list_editable 속성에 의해서.때문에 ModelForm 의 Meta.model 을 제거하는 것이 가장 올바른 사용법이며, ModelAdmin 이 이 모든걸 처리해줄것이다. |
| get_changelist_formset                | request,kwargs                           | list_editable 을 목록 페이지에서 사용하는 경우, 이를 위한 ModelFormSet 클래스를 재정의 할 수 있다. | `from django.forms import BaseModelFormSetclass MyAdminFormSet(BaseModelFormSet):    passclass MyModelAdmin(admin.ModelAdmin):    def get_changelist_formset(self, request, **kwargs):        kwargs['formset'] = MyAdminFormSet        return super(MyModelAdmin, self).get_changelist_formset(request, **kwargs)` |                                          |
| has_add_permission                    | request                                  | 객체 추가를 허용한다면 True, 아니면 False             |                                          |                                          |
| has_change_permission                 | request,obj=None                         | 객체 변경을 허용한다면 True, 아니면 False             |                                          |                                          |
| has_delete_permission                 | request,obj=None                         | 객체 삭제의 허용 여부를 리턴.                        |                                          |                                          |
| has_module_permission                 | request                                  | 모듈을 관리자 인덱스 페이지에 표시할지에 대한 여부와 이에 엑세스를 허용할지에 대한 여부 지정.기본적으로 User.has_module_perms() 에 의해 True, False가 리턴되고 있고,이 메서드를 오버라이딩 하더라도, 추가, 변경, 삭제가 안되는 것이 아니다. 이들은 위에 말한 3가지의 퍼미션에 의해 결정되는 것. |                                          |                                          |
| get_queryset                          | request                                  | 해당 메서드는 모델의 모든 인스턴스를 리턴하는데, 이를 조정 할 수 있는 것. | `class MyModelAdmin(admin.ModelAdmin):    def get_queryset(self, request):        qs = super(MyModelAdmin, self).get_queryset(request)        if request.user.is_superuser:            return qs        return qs.filter(author=request.user)` |                                          |
| message_user                          | request,message,level=messages.INFO,extra_tags='',fail_silently=False | 사용자에게 메시지를 보내는 메서드. See the [custom ModelAdmin example](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/actions/#custom-admin-action). |                                          |                                          |
| get_paginator                         | request,queryset,per_page,orphans=0,allow_empty_first_page=True | 현재의 목록페이지를 위한 paginator 인스턴스를 리턴한다.      |                                          |                                          |
| response_add                          | request,obj,post_url_continue=None       | add_view() 단계를 위한 HttpResponse 를 결정.이 메서드는 객체(모델)이 추가 된 직후에 호출되는 메서드로, 저장 후에 동작을 변경하려면 재정의 할 수 있다. (음.. 저장 후 리다이렉트 되는 화면을 목록페이지나 현 페이지가 아닌 다른곳도 가능할듯?) |                                          |                                          |
| response_change                       | request,obj                              | change_view() 단계를 위한 HttpResponse 결정     |                                          |                                          |
| response_delete                       | request,obj_display,obj_id               | delete_view() 단계를 위한 HttpResponse 결정obj_display 는 삭제된 객체에 대한 이름(스트링)이고,obj_id 는 삭제된 객체에 대한 일련번호(디비 id가 아니라면, 장고 내부적으로 사용되는 특정 시리얼라이즈된 식별자 일듯) 이다. |                                          |                                          |
| get_changeform_initial_data           | request                                  | 변경 페이지의 폼이 초기 데이터를 후크 하는 곳.              |                                          |                                          |
| 페이지 렌더링아래 5항목                         |                                          | 모델 인스턴스에 대해 CURD 처리를 위해 랜더링되는 장고 뷰(페이지)를 핸들링 하기 위한 함수들이다.이들 메서드들을 재정의하는 한가지 일반적인 이유로는 뷰에 컨텍스트를 보강하는 것에 있다. |                                          | see the [TemplateResponse documentation](https://docs.djangoproject.com/en/1.10/ref/template-response/). |
| add_view                              |                                          | 추가 페이지                                   | `class MyModelAdmin(admin.ModelAdmin):    # A template for a very customized change view:    change_form_template = 'admin/myapp/extras/openstreetmap_change_form.html'    def get_osm_info(self):        # ...        pass    def change_view(self, request, object_id, form_url='', extra_context=None):        extra_context = extra_context or {}        extra_context['osm_data'] = self.get_osm_info()        return super(MyModelAdmin, self).change_view(            request, object_id, form_url, extra_context=extra_context,        )` |                                          |
| change_view                           |                                          | 변경 페이지                                   |                                          |                                          |
| changelist_view                       |                                          | 목록 페이지                                   |                                          |                                          |
| delete_view                           |                                          | 삭제 페이지                                   |                                          |                                          |
| history_view                          |                                          | 히스토리 페이지                                 |                                          |                                          |
| ModelAdmin.Media                      |                                          | 간단한 CSS / JS 를 추가할수있는 방법 [링크참조](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#modeladmin-asset-definitions) |                                          |                                          |

## 1.1 관리자에 커스텀 유효성 검사를 추가하는 방법

ModelAdmin 은 자신만의 폼을 재정의 할수있고, 이 정의된 폼에서 폼의 모든 필드에 대해 커스텀 유효성을 체크 할 수 있다.

```python
class ArticleAdmin(admin.ModelAdmin):
    form = MyArticleAdminForm
```

```python
class MyArticleAdminForm(forms.ModelForm):
    def clean_name(self):
        # do something that validates your data
        return self.cleaned_data["name"]
```

[model form validation notes](https://docs.djangoproject.com/en/1.10/topics/forms/modelforms/#overriding-modelform-clean-method) 참조

ModelForm 에는 유효성을 검사하기위한 두가지 주요 단계가 있다.

첫번째로 [폼에 대한 유효성 검사](https://docs.djangoproject.com/en/1.10/ref/forms/validation/)와, 두번째로 [모델 인스턴스](https://docs.djangoproject.com/en/1.10/ref/models/instances/#validating-objects)에 대한 유효성 검사이다.

일반 Form의 유효성 검사와 마찬가지로, ModelForm 유효성검사는 is_valid() 를 호출하거나 errors 속성에 엑세스 할때, 암시적으로 트리거되고, 
모델의 full_clean() 이 호출될때 명시적으로 트리거된다. 

모델의 유효성 검사는 폼의 clean() 메서드가 호출된 직후, 폼의 유효성 검사 단계에서 트리거된다.

ModelForm.clean() 메서드 오버라이드 - 이 clean 메서드를 재정의 함으로써, 추가 유효성 검사를 할 수 있다. ModelForm 에서는 붙여진 Model 메서드에 접근 할 수 있는 인스턴스가 주어진다.(간단히 ModelForm 에 연관된 Model 인스턴스에 접근이 가능하단말..)

ModelForm.clean() 메서드에는 모델 유효성 검사 단계에서 고유성(unique, unique_together, unique_for_date|month|year)이 담긴 필드에 대한 고유성을 검사하는 플래그를 설정한다.
이러한 고유성(유효성)을 유지하고자한다면 상위의 clean() 메서드를 호출하여야한다.  

[모델 인스턴스](https://docs.djangoproject.com/en/1.10/ref/models/instances/#validating-objects)에 대한 유효성 검사 내용은 아래와 같다.

우선 모델(Model)에는 3가지의 자신의 필드에 대해 유효성을 검사하는 메서드가 존재한다.

Model.clean_fields(), Model.clear(), Model.validate_unique() 들이 그 것이고, 이 세가지 단계를 모두 수행하는 full_clean() 메서드가 존재한다.

ModelForm 을 사용한다면, ModelForm 의 is_valid()를 호출하면 폼에 포함된 모든 필드에 대해 이들 유효성 검사 단계가 수행된다.

유효성 검사 오류를 직접 처리할 계획이거나, 유효성 검사가 필요한 ModelForm 에서 필드를 제외한 경우에는, Models.full_clean() 메서드만 호출하면 된다.

Model.full_clean(exclude=None, validate_unique=True) 가 호출되면, clean_fields() 와 clean() 메서드가 순차적으로 호출되고, validate_unique 가 True 라면, validate_unique()가 마지막으로 호출된다.

exclude 에는 유효성 검사를 제외할 필드 이름 목록을 제공하는데 사용된다.

ModelForm 은 사용자(유저)가 발생한 오류를 '(값-)수정' 할 수 없으므로, exclude 인자를 사용하여 필드의 유효성 검사에서 제외한다.

모델의 save()가 호출 될때, 자동으로 full_clean() 이 호출되지 않는다. 직접 만든 모델에 대해서는 수동으로 full_clean() 메서드를 호출하여 유효성 검사를 해야한다.

full_clean() 메서드는 먼저 clean_fields() 를 호출하여, 개별 필드에 대해 유효성을 검사한다.

clean_fields(exclude=None)는 모델의 모든 필드에 대해 유효성을 체크한다. 인자로 exclude 를 조작하여, 유효성 체크를 제외할 필드를 설정 할 수 있다. 검사에 실패한 필드에대해 ValidationError가 발생된다.

이후 clean() 메서드를 호출하게 되는데, 모델 자체에서 사용자 정의 유효성 검사를 수행하려면 이 메서드를 재정의 해야한다.

Model.clean() 이 메서드는 커스텀 모델 유효성 검사를 제공하거나, 원하는 경우 모델의 속성을 수정하는데 이용되어야한다.

예를들어, 자동적으로 필드의 내용을 체워주거나, 하나이상의 필드에 엑세스가 필요한 유효성 검사를 수행하는데 이용된다.

```python
class Article(models.Model):
    ...
    def clean(self):
        # Don't allow draft entries to have a pub_date.
        if self.status == 'draft' and self.pub_date is not None:
            raise ValidationError(_('Draft entries may not have a publication date.'))
        # Set the pub_date for published items if it hasn't been set already.
        if self.status == 'published' and self.pub_date is None:
            self.pub_date = datetime.date.today()
```

이 clean() 메서드 역시 모델의 save()가 호출된다고해서 자동으로 호출되지 않는다.

위의 예에서 ValidationError 가 Model.clean() 에 의해 문자열과 함께 발생되고 있고, 이 에러는 특수한 딕셔너리 키 NON_FIELD_ERRORS 에 저장된다. 이 NON_FIELD_ERRORS 는 특정 필드가  아닌 전체 모델에 연결된 오류에 사용된다.

```python
from django.core.exceptions import ValidationError, NON_FIELD_ERRORS
try:
    article.full_clean()
except ValidationError as e:
    non_field_errors = e.message_dict[NON_FIELD_ERRORS]
```

ValidationError 에 스트링으로 raise를 이 키를 이용하여 필드의 에러를 확인할 수있다는 것.

특정 필드에 대해 예외를 지정하려면, Dictionary 를 사용하여 ValidationError 를 인스턴스화 하여 사용해야한다. 여기서 key 는 필드 이름이다.

```python
class Article(models.Model):
    ...
    def clean(self):
        # Don't allow draft entries to have a pub_date.
        if self.status == 'draft' and self.pub_date is not None:
            raise ValidationError({'pub_date': _('Draft entries may not have a publication date.')})
        ...
```

위처럼 pub_date 에 대한 모델 필드명으로 해당 필드에 대한 예외를 발생시킬 수 있다. 

Model.clean() 도중에 여러키에 대해 에러를 발생하고자한다면, 딕셔너리의 매핑하여 여러개를 발생할수있다.

```python
raise ValidationError({
    'title': ValidationError(_('Missing title.'), code='required'),
    'pub_date': ValidationError(_('Invalid date.'), code='invalid'),
})
```

이후 마지막으로 full_clean() 은 모델에 대한 고유한 제약 조건을 체크하는 validate_unique(exclude=None) 을 호출한다.

clean_fiedls() 와 비슷하지만, 모델의 모든 유니크 제약에 대해 체크하는 곳으로, exclude를 이용하면 유효성 검사에 제외된다.

exclude 속성을 validaate_unique() 에 제공한다면, 제공된 필드에 대해 unique_together 제약 조건은 체크하지 않는다.

unique_together = ('field_1', 'field_2') 이렇게 지정한 쌍으로된 유니크 제약조건을 검사하지 않는 다는 것,

### 2.InlineModelAdmin ( from django.contrib.admin import TabularInline, StackedInline)

관리 인터페이스에서는 상위(ModelAdmin에 지정된) 모델과 동일한 페이지에서 (다른) 모델을 편집 할 수 있는 기능이 있다. 이들을 인라인(Inline) 이라 부른다.

다음 두가지의 모델이 있다고 예를 들자.

```Python
from django.db import models

class Author(models.Model):
   name = models.CharField(max_length=100)

class Book(models.Model):
   author = models.ForeignKey(Author, on_delete=models.CASCADE)
   title = models.CharField(max_length=100)
```

위와 같이 저자와 저자가 쓴 책이라는 모델이 존재한다면, 저자페이지에서 저자가 쓴 책을 편집 할 수 있다.
ModelAdmin.inlines 에 Book 의 Inline 을 만들어 지정하여 표시할수있음.

```python
from django.contrib import admin

class BookInline(admin.TabularInline):
    model = Book

class AuthorAdmin(admin.ModelAdmin):
    inlines = [
        BookInline,
    ]
```

 InlineModelAdmin 에는 [TabularInline](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.TabularInline) 과 [StackedInline](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.StackedInline) 두가지가 서브클래스 제공된다. 이둘의 차이는 랜더링에 사용되는 템플릿의 차이뿐이다.

### 2.1 InlineModelAdmin 의 옵션 및 메서드 설명

ModelAdmin 과 많은 속성을 공유하며, 일부 추가된 속성이 존재. 공유된 속성은 BaseModelAdmin 이라는 보다 상위 클래스로부터 정의된 것이다.

| InlineModelAdminOptions & Method         | value type &method params | description                              | example                                  | etc                                      |
| ---------------------------------------- | ------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| BaseModelAdmin 과 공유된 속성들                 |                           |                                          |                                          |                                          |
| form, fieldsets, fields, formfield_overrides, exclude, filter_horizontal, filter_vertical, ordering, prepopulated_fields, get_queryset, radio_fields, readonly_fields, raw_id_fields, formfield_for_choice_field, formfield_for_foreignkey, formfield_for_manytomany, has_add_permission, has_delete_permission, has_module_permission |                           |                                          |                                          |                                          |
| 다음은 InlineModelAdmin 에 추가되는 옵션들          |                           |                                          |                                          |                                          |
| model                                    | Model                     | inline 으로 사용될 모델, 반드시 필요하다.              |                                          |                                          |
| fk_name                                  | foreignkey name           | foreign key 의 이름을 작성하는 속성, 대부분 자동으로 해당 테이블의 포링키를 가리키는데, 동일한 상위(포링키로 연결된 모델) 모델에, 두개이상으로 foreignkey 를 가리킨다면, 명시적으로 작성 해주어야 한다. |                                          |                                          |
| formset                                  | FormSet                   | 기본적으로 [BaseInlineFormSet](https://docs.djangoproject.com/en/1.10/topics/forms/modelforms/#django.forms.models.BaseInlineFormSet) 으로 폼셋이 할당되어있는데, 커스터마이징한 폼셋을 만들어 사용 하고자하면 해당 속성에 설정하면 된다. | `from django.db import modelsclass Author(models.Model):    name = models.CharField(max_length=100)class Book(models.Model):    author = models.ForeignKey(Author, on_delete=models.CASCADE)    title = models.CharField(max_length=100)``from django.forms import inlineformset_factoryBookFormSet = inlineformset_factory(Author, Book, fields=('title',))author = Author.objects.get(name='Mike Royko')formset = BookFormSet(instance=author)` | 예제를 보면, 인라인에 들어가는 폼셋을 구성하는데 있어서, 특정 저자가 작성한 book 을 수정할 수 있는 폼셋을 만드는 코드이다. |
| form                                     | From                      | 기본적으로 ModelForm 이 기본값이며, 인라인 폼셋을 만들때, inlineformset_fectory() 에 전달되는 값이다.InlineModelAdmin 폼에 대한 사용자 커스터마이징 유효성을 추가할때, 상위 모델 기능에 의존하는 유효성 검사작성에 유의 할것. 상위 모델의 유효성 검사에 실패하면, ModelForm 의 유효성 검사에서 이야기한 것처럼, 일관되지 않은 상태가 유지될수있다. |                                          |                                          |
| classes                                  | list or tuple             | 인라인으로 렌더링 되는 fieldset 에 추가로 적용할 CSS 클래스 지정한다.디폴트는 None 이고, fieldsets 에 구성된 classes 와 마찬가지로, collapse 클래스를 지정하면, 인라인은 처음 접혀있고, 헤더부분에 '표시' 링크가 표시된다. |                                          | version >= 1.10                          |
| extra                                    | number                    | formset에 표시할 초기 폼 외에, 추가 폼의 수를 제어한다. [formsets document](https://docs.djangoproject.com/en/1.10/topics/forms/formsets/)여기서 설정한 값 외에도, 인라인을 추가 할 수 있도록, 항목 추가 버튼이 존재한다. 단, 현재 표시된 폼 수가 max_num을 초과하거나 자바스크립트가 안되는 브라우저에서는 버튼이 표시되지 않는다.get_extra() 메서드를 사용(재정의)하면, 커스텀 할 수 있다. |                                          |                                          |
| max_num                                  | number                    | inline에 표시할 최대 form 수를 제어한다.  See [Limiting the number of editable objects](https://docs.djangoproject.com/en/1.10/topics/forms/modelforms/#model-formsets-max-num) for more information.get_max_num() 메서드를 커스터마이징 할 수 있음. |                                          |                                          |
| min_num                                  | number                    | inline에 표시할 최소 form 수를 제어한다. See [`modelformset_factory()`](https://docs.djangoproject.com/en/1.10/ref/forms/models/#django.forms.models.modelformset_factory) for more information.get_min_num() 메서드를 커스터마이징 할 수 있음. |                                          |                                          |
| raw_id_fields                            | tuple or list             | ModelAdmin.raw_id_fields 와 마찬가지로, foreignkey 혹은 manytomany 로 설정된 필드들에 대해 select 박스로 인터페이스가 구성되는데, 오버헤드가 발생 할 수 있는 경우, 이를 input 위젯으로 변경하여 표시할수있다. |                                          |                                          |
| template                                 |                           | 페이지에서 인라인을 랜더링 할 때 사용할 템플릿.              |                                          |                                          |
| verbose_name                             | str                       | Model 의 내부 Meta 클래스에 정의되어있는 verbose_name을 재정의한다. |                                          |                                          |
| verbose_name_plural                      | str                       | Model 의 내부 Meta 클래스에 정의되어있는 verbose_name_plural을 재정의한다. |                                          |                                          |
| can_delete                               | boolean                   | 기본값은 true 이며, inline 에서 object를 삭제 할 수 있는지의 여부를 담는 값 |                                          |                                          |
| show_change_link                         | boolean                   | admin 에서 변경할 수 있는 인라인 객체의 변경폼 링크의 제공여부를 맡는다. 기본 값은 False 이며, True로 지정하면, 해당 모델의 수정폼으로 이동할 수 있는 페이지로 이동할 링크가 각 인라인 객체에 표시된다. |                                          |                                          |
| get_formset                              | request,obj=None,kwargs   | admin 에서 추가/변경 뷰에서 사용할, BaseInlineFormSet 클래스를 리턴하는 메서드.[ModelAdmin.get_formsets_with_inlines](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#django.contrib.admin.ModelAdmin.get_formsets_with_inlines) 를 참조. |                                          |                                          |
| get_extra                                | reqeust,obj=None,kwargs   | 사용할 추가 인라인 폼 수를 리턴. 기본적으로 extra 속성에 지정한 수를 리턴함.obj 는 모델의 인스턴스. | `class BinaryTreeAdmin(admin.TabularInline):    model = BinaryTree    def get_extra(self, request, obj=None, **kwargs):        extra = 2        if obj:            return extra - obj.binarytree_set.count()        return extra` |                                          |
| get_max_num                              | request,obj=None,kwargs   | 추가 인라인 폼 수의 최대 수를 반환. 기본적으로 max_num 속성에 지정한 수를 리턴함. | `class BinaryTreeAdmin(admin.TabularInline):    model = BinaryTree    def get_max_num(self, request, obj=None, **kwargs):        max_num = 10        if obj and obj.parent:            return max_num - 5        return max_num` |                                          |
| get_min_num                              | request,obj=None,kwargs   | 추가 인라인 폼 수의 최저 수를 반환. 기본적으로 min_num 속성에 지정한 수를 리턴함. |                                          |                                          |

### 2.2 2개이상의 ForeignKey 를 한 부모 모델로 지정한 모델을 인라인 모델로 사용하는 방법

때때로 같은 모델에 두개이상의 ForeignKey를 갖는 모델이 정의 될 수 있다.

```python
from django.db import models

class Friendship(models.Model):
    to_person = models.ForeignKey(Person, on_delete=models.CASCADE, related_name="friends")
    from_person = models.ForeignKey(Person, on_delete=models.CASCADE, related_name="from_friends")
```

이러한 경우 Person 모델에의 Admin 에서 Friendship 을 인라인으로 사용 할 경우, Friendship 인라인 모델이, Persone 의 FK 자동으로 찾아 줄 수 없다. (두개이기에 무얼 가리킬지를 모르는 것)

때문에 FK를 아래와 같이 지정해야한다.

```python
from django.contrib import admin
from myapp.models import Friendship

class FriendshipInline(admin.TabularInline):
    model = Friendship
    fk_name = "to_person"

class PersonAdmin(admin.ModelAdmin):
    inlines = [
        FriendshipInline,
    ]
```

### 2.3 many-to-many 모델을 사용 하는 방법

기본적으로, ManyToManyField에 대해 실제 참조를 포함하는 모델에 다 대 다 관계에 대한 관리 위젯이 표시된다.

ModelAdmin 에서 정의하는 대로, many-to-many field 는 select multiple 또는 수평/수직 필터 또는 raw_id_admin 위젯으로 나타낼수있다.

그러나 이 위젯을 Inline 으로도 표현 할 수 있다.

```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, related_name='groups')
```

위와 같은 모델이 정의 되어있고, 다대다 관계를 인라인으로 표시하고자한다면, 그 관계에 대한 InlineModelAdmin 객체를 정의하여 표시 할 수 있다.

```python
from django.contrib import admin

class MembershipInline(admin.TabularInline):
    model = Group.members.through

class PersonAdmin(admin.ModelAdmin):
    inlines = [
        MembershipInline,
    ]

class GroupAdmin(admin.ModelAdmin):
    inlines = [
        MembershipInline,
    ]
    exclude = ('members',)
```

위 코드를 보면, InlineModel 에 model 지정으로 다대다 필드에 대해 through 로 지정하였고,

이렇게 정의된 MembershipInline 을 PersonAdmin 과 GroupAdmin 에서 쓸 수 있는 것.

단, Group 에서 다대다 관계를 만들었기에, Group 모델의 members 를 exclude 한 GroupAdmin 이다.

위 코드에서 주의깊게 확인해야할 포인트는 다음 두가지이다.

첫째. MembershipInline 모델의 model은 Group.members.through 모델이다. through 속성은 다대다 관계를 관리하는 모델의 레퍼런스이다. 
이 모델은 장고가 다대다 필드를 정의할때 자동으로 생성해주는 모델임.

둘째. GroupAdmin 은 members 필드를 제외해야한다. 장고는 다대다 필드의 관계에 대한 관리 위젯을 표시한다.
인라인 모델을 사용하여, 다대다 관계를 표현하려면, 장고 관리자에게 표시하지 말라고 정의해두어야 하는 것. 
그렇게 하지 않으면, 인라인과, 어드민에서 생성해주는 위젯 두개의 위젯이 표시되기 때문.

- 이 기술(다대다 필드 인라인 표현)을 사용 할 때, m2m_changed 신호는 트리거되지 않는다. 관리자에 관한 through 는 다 대 다가아닌, 두개의 외래키가 있는 모델일뿐이기 때문.
  m2m_changed가 무엇인지 알아야함.
- 이 외 다른건 모두 InlineModelAdmin의 무엇과도 같다. 일반 ModelAdmin 속성(프로퍼티)중 하나를 사용하여 외관을 커스터마이징 할 수 있다.

### 2.4 many-to-many 중개자 모델 작업

모델의 ManyToManyField 에 대해 through 인수를 Inline 모델에 지정하면, 기본적으로 admin 에서는 위젯을 표시하지 않는다.

이는 중개 모델의 각 인스턴스(row) 가 단일 위젯에 표시 할 수 있는 것보다 많은 정보를 필요로 하고, 여러 위젯들에 필요한 레이아웃이 중간 모델에 따라 다를 수 있기 때문이다.

하지만, 그 정보들을 인라인으로 편집 할 수 있기를 원한다면, 인라인 관리 모델을 사용하면, 처리가 가능하다.

```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')

class Membership(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)
```

위 코드를 보면, Group 에 다대다 필드에 through 를 Membership 으로 지정하였고, 이는 중간 모델을 자동으로 생성하는 것이 아니라, 직접 정의한것.

이후, Membership 모델(중개 모델)을 인라인으로 표시하기위해 Inline 클래스로 정의하면 된다.

```python
class MembershipInline(admin.TabularInline):
    model = Membership
    extra = 1
```

InlineModelAdmin 에서 Membership 모델을 사용하고, 기본적으로 추가 할 수 있는 추가 폼을 하나(extra)로 제한한 코드.

이후 Membership 이라는 중간 모델을 인라인으로 사용하기위해 Membership 의 InlineModelAdmin 로 만든 MembershipInline 을 사용하고자하는 AdminModel 에 inlines 로 사용.

```python
class PersonAdmin(admin.ModelAdmin):
    inlines = (MembershipInline,)

class GroupAdmin(admin.ModelAdmin):
    inlines = (MembershipInline,)
```

### 2.5 일반적인 관계를 인라인으로 사용하기

직접적인 Relation 이 없는 모델들 간에도, GenericForeignKey 와 (GenericInlineModelAdmin 의 서브클래스들인 )GenericTabularInline, GenericStackedInline 을 이용하여, 인라인으로 사용 할 수 있다.

```python
from django.db import models
from django.contrib.contenttypes.fields import GenericForeignKey

class Image(models.Model):
    image = models.ImageField(upload_to="images")
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey("content_type", "object_id")

class Product(models.Model):
    name = models.CharField(max_length=100)
```

```python
from django.contrib import admin
from django.contrib.contenttypes.admin import GenericTabularInline

from myproject.myapp.models import Image, Product

class ImageInline(GenericTabularInline):
    model = Image

class ProductAdmin(admin.ModelAdmin):
    inlines = [
        ImageInline,
    ]

admin.site.register(Product, ProductAdmin)
```

보다 자세항 정보는 [contenttypes document](https://docs.djangoproject.com/en/1.10/ref/contrib/contenttypes/) 참조.

### 2.6 [템플릿 오버라이딩](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/#overriding-admin-templates)

# 3.ModelForm ( from django.forms import ModelForm) with FormSet

장고 db models 를 사용하는 경우에 매핑하여 사용할 수 있는 폼 양식.

보통 장고 프로젝트 내의 각 앱내 admin.py에 기술함.

장고에서 장고 모델을 사용 할때, Form 클래스를 만드는데 도움을 주는 클래스를 제공하는데, 이게 바로 ModelForm.

```python
>>> from django.forms import ModelForm
>>> from myapp.models import Article

# Create the form class.
>>> class ArticleForm(ModelForm):
...     class Meta:
...         model = Article
...         fields = ['pub_date', 'headline', 'content', 'reporter']

# Creating a form to add an article.
>>> form = ArticleForm()

# Creating a form to change an existing article.
>>> article = Article.objects.get(pk=1)
>>> form = ArticleForm(instance=article)
```

생성된 폼 클래스는 필드 속성에 지정된 모델의 모든 필드에 대해 form field 를 갖는다.

## 3.1 FormSet ( models.BaseModelFormSet )

폼셋은, 동일 페이지에서 여러 Form으로 작업하는 추상 계층이다. Form 의 Set 인것.

FormSet 을 만들때, 모델의 어떤 필드가 폼의 필드로 보일지 지정이 가능하고,  FormSet 인스턴스를 만들때, queryset 을 지정하여, 원하는 row만 긁어다가 폼셋을 구성 할 수 있다.

FormSet 을 만들때, 모델을 지정해서가 아닌, 모델이 지정된 ModelForm 을 지정해서도 FormSet을 구성할수있고, 이러한 경우 유효성 검사에 용이하다.

ModelForm 의 필드의 위젯을 변경 할 수 있는 것처럼, CharField 를 TextField 처럼 위젯 변경도 가능하다.

FormSet에도, ModelForm 가 마찬가지로 모델 객체로 데이터를 저장 할 수 있다. FormSet.save() 메서드가 그것이며, save() 메서드는 디비에 저장된 인스턴스를 반환한다.
save() 메서드에 인자로 commit=False 를 전달하면, 저장되지 않은 모델의 인스턴스가 리턴된다.
저장되지 않은 인스턴스를 체크하여 무언갈 다시 할 수 있다.
또한 FormSet 에 ManyToManyField 가 포함되어있으면, 해당 관계도 저장될 수 있도록 FormSet.save_m2m() 을 호출 해야한다.

추가로 Formset 은 request.POST 데이터로 생성도 가능하다.

기본적으로 FormSet에서 제공하는 is_valid() 메서드로 데이터가 유효한지 확인이 가능.

ModelFormSet 의 clean() 메서드를 오버라이딩 하여 커스텀 유효성을 체크 할 수 있다.

```python
from django.forms import BaseModelFormSet

class MyModelFormSet(BaseModelFormSet):
    def clean(self):
        super(MyModelFormSet, self).clean()
        # example custom validation across forms in the formset
        for form in self.forms:
            # your custom formset validation
            ...
```

위처럼 super 로 검사를 한번 진행하면, 유니크한 속성들에 대해 유효성 조사를 유지할 수 있고, 
이 단계에서 이미 각 폼에 대한 개별 모델 인스턴스가 생성되어있다.

clean() 단계에서 폼의 값을 수정하려면, form.instance 를 수정해야한다.

```python
from django.forms import BaseModelFormSet

class MyModelFormSet(BaseModelFormSet):
    def clean(self):
        super(MyModelFormSet, self).clean()

        for form in self.forms:
            name = form.cleaned_data['name'].upper()
            form.cleaned_data['name'] = name
            # update the instance value.
            form.instance.name = name
```

## 3.2 Inline formsets

인라인 폼셋은 모델 폼셋 위의 작은 추상화 계층(레이어)이다. 이것은 외래 키를 통해 관련 객체로 작업하는 경우를 단순화한다.

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    title = models.CharField(max_length=100)
```

```python
from django.forms import inlineformset_factory
BookFormSet = inlineformset_factory(Author, Book, fields=('title',))
author = Author.objects.get(name='Mike Royko')
formset = BookFormSet(instance=author)
```

inlineformset_factory 는 modelformset_factory() 를 사용하고, can_delete=True 로 지정된다.

## 3.3 InlineFormset 오버라이딩 메서드.

InlineFormSet 의 메서드를 재정의하는 경우, BaseModelFormSet 대신, BaseInlineFormSet 을 서브 클래스화 해야한다.

```python
from django.forms import BaseInlineFormSet

class CustomInlineFormSet(BaseInlineFormSet):
    def clean(self):
        super(CustomInlineFormSet, self).clean()
        # example custom validation across forms in the formset
        for form in self.forms:
            # your custom formset validation
            ...
```

```python
from django.forms import inlineformset_factory
BookFormSet = inlineformset_factory(Author, Book, fields=('title',),
...     formset=CustomInlineFormSet)
author = Author.objects.get(name='Mike Royko')
formset = BookFormSet(instance=author)
```

이후, inline formset 을 만들때, 선택 인수로 formset을 전달해야한다. 

- 외래키 두개가 동일한 모델을 가리키는 모델의 InlineFormSet 을 만들땐, fk_name 을 지정해주어야함.


