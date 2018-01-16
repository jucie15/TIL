## python json 모듈 사용시 not JSON serializable 에러를 피하는 방법

1. ### Refer

   1. ##### [not JSON serializable에러 시](http://dgkim5360.tistory.com/entry/not-JSON-serializable-error-on-python-json)

   2. ##### [스택오버플로우](https://stackoverflow.com/questions/11875770/how-to-overcome-datetime-datetime-not-json-serializable)

2. ### 목표

   1. ##### `Json serialize`시 `Datetime object`만 `type error: not JSON serializable`가 난다.

   2. ##### `Json default` 함수를 이용하여 문제를 해결한다. 

   3. ##### 지금은 `datetime` 형식만 처리했지만 다른 형식에 대해서도 함수를 수정해 처리가 가능하다.

3. ### TroubleShooting

   1. ##### `serialize`할 데이터(`datetime`형식의 `reg_time`)

      ```python
      qa = QABoard.objects.filter(member_id=member_id).values('id', 'title', 'reg_time', 'is_secret', 'is_answered')
      ```

   2. ##### json_default 함수 

      ```python
      def json_default(obj):
          """JSON serializer for objects not serializable by default json code"""
          if isinstance(obj, (datetime, date)):
              return obj.isoformat()
          raise TypeError ("Type %s not serializable" % type(obj))
      ```

   3. ##### Json.dumps 시

      ```python
      data = json.dumps(data, default=json_default)
      ```

