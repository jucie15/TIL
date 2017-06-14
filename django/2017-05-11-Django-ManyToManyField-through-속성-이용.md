# Django ManyToManyField through 속성 이용

### 구현 기능

- 각 공약별 좋아요/싫어요를 위한 좋아요/싫어요 모델 추가
- 기본 `ManyToMany` 필드 이용시 장고가 알아서 중간 테이블을 만들어주지만 중간테이블에 추가 필드가 필요할 경우 중간테이블을 직접 커스텀하고  `through` 속성을 이용하여 커스텀한 중간테이블을 이용할 수 있다.





### LikeOrDisLike 관계 테이블 추가

- 좋아요/싫어요를 위해 N:M 관계테이블에 `like`와 `dislike`가 추가된 중간 테이블 생성
- models.py

```python
class LikeOrDislike(models.Model):
    # 좋아요/싫어요를 위한 N:M 유저와 공약간의 관계모델
    user = models.ForeignKey(settings.AUTH_USER_MODEL, related_name='like_dislike') # 유저와 1:N 관계 설정
    pledge = models.ForeignKey(Pledge) # 공약과 1:N 모델 설정
    like = models.BooleanField(default=False) # 좋아요
    dislike = models.BooleanField(default=False) # 싫어요

    def __str__(self):
        return '{}공약의 좋아요 {},싫어요 {}'.format(self.pledge, self.like, self.dislike)
```



- Pledge 모델에서 `ManyToMany`관계인 `like_dislike` 필드를  `through` 속성을 이용하여 추가

```python
class Pledge(models.Model):
	like_dislike = models.ManyToManyField(settings.AUTH_USER_MODEL, through='LikeOrDislike') # 좋아요/싫어요 모델 LikeOrDisLike 모델을 통해 User와 N:M 관계 설정
```



### 커스텀 중간 테이블 이용 시 모든 모델 관계 매칭은 중간테이블을 통해 이루어 진다.

- `user` 모델에서 리버스 매칭을 편하게 하기 위해 `related_name = 'like_dislike'`로 지정

```Python
if user.like_dislike.filter(pledge_id=pledge.id).exists():
    # 좋아요/싫어요 중 하나라도 눌렀을 경우
    # 둘 중 한개만 선택 가능
    like_instance = user.like_dislike.get(pledge_id=pledge.id) # 좋아요/싫어요 인스턴스 생성
```



### Through 속성 이용 시 add, create, set을 지원하지 않는다.

- `ManyToManyField`에서 `through` 속성 이용시 add, create, set을 지원하지 않는다.
- 추가 삭제 시 `LikeOrDislike`모델로 직접 접근
- views.py

```python
LikeOrDislike.objects.filter(pledge=pledge, user=user).delete() # 인스턴스 삭제
LikeOrDislike.objects.create(
    user=user,
    pledge=pledge,
    like=True,
    dislike=False,
) # 인스턴스 생성

```



