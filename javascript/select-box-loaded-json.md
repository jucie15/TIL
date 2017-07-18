# Json 파일 불러와 시/군/구 선택하기

1. ##### jquery로 json 파일을 불러온다

2. ##### 임시 파일에 저장해 놓는다.

3. ##### select box option 값으로 파싱하여 사용한다.



### Code

- ##### html

```html
<select id="city" name="city">
</select>
<p>
<select id="district" name="district">
</select>
```

- ##### Json

```json
{
	"서울특별시" : [
	{
		"id": "11010",
		"name": "종로구"
	},
	{
		"id": "11020",
		"name": "중구"
	},
	{
		"id": "11030",
		"name": "용산구"
	}],
	[...]
}
```

- ##### javascript

```Javascript
<script>
  var address; // json 데이터를 불러와 담아놓을 변수
  $(document).ready(function(){
    /*
    페이지 로딩 시 JSON 파일을 불러와 address 변수에 담아 놓고,
    city select box에 시/도 값 표시 default(서울특별시)
    district select box에 서울특별시 행정구 값 표시
    */
    $.getJSON( "{% static 'cast/json/address.json' %}",
      function( data ) {
        address = data;
        city_options = $('#city');
        $.each(address, function( city, val ){
          city_options.append($('<option>',
              { value: city, text: city }
            )) // select box에 option 값 추가
        });
        $('#city').html(city_options.html()); // city select box에 표시

        district_options = $('#district');
        $.each(address['서울특별시'], function( idx, val){
          district_options.append($('<option>',
            { value: val.name, text: val.name }
          ))
        });
        $('#district').html(district_options.html()); // district select box에 표시
      });
  });
  $('#city').change(function(){
    // city select box가 변경 될 경우
    city = $('#city').val();
    district_options = $('#district');
    district_options.empty() // 기본값 설정이 되어있어 append 시 중복되므로 값을 비운다.
    $.each(address[city], function( idx, val){
      district_options.append($('<option>',
          { value: val.name, text: val.name }
        ))
    });
    $('#district').html(district_options.html());
  });

</script>
```