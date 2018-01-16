## Django Queryset to JSON

1. ### Refer

   - ##### [django serialize queryset.values() into json](https://stackoverflow.com/questions/7650448/django-serialize-queryset-values-into-json)

2. ### 목표

   1. ##### `queryset`을 `serialize`할 때 에러가 난다.

   2. ##### `queryset`에서 원하는 필드만 쓰고싶은데 `serializer` 에러가 난다.

3. ### TroubleShotting

   ```python
   qa = QABoard.objects.filter(member_id=member_id).values('id', 'title', 'reg_time', 'is_secret', 'is_answered') # 원하는 데이터만 뽑아낸다.
   data['qa'] = list(qa) # list로 묶어준 후 
   data = json.dumps(data, default=json_default) # serialize 시켜준다.
   ```

   1. 이렇게 해도 되지만 json output이 깔끔하지 않다.

   ```python
   data = serializers.serialize('json', list(qa), fields=('title','id')) 
   ```

   ​

