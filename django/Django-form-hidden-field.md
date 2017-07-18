#  폼 필드 숨기기

- #####  폼 필드를 숨겨 커스텀을 쉽게한다.



### Form(accounts/forms.py)

```python
class ProfileForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ['city', 'district']
        widgets = {
            "city": forms.HiddenInput(),
            "district": forms.HiddenInput(),
        }
```



### Templates(accounts/signup_info.html)

- ##### 모델 폼에서 추가된 필드는 `input type="hidden"`으로 숨겨놓고, 원하는 필드를 추가하여 submit 한다.

```html
  <form action="" method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <select id="city" name="city">
    </select>
    <p>
    <select id="district" name="district">
    </select>
    <input type="submit" value="다음">
  </form>
```