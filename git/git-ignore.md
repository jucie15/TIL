## Git igonre

- ### Refer

  - ##### [Best-practice](https://github.com/github/gitignore/blob/master/Python.gitignore)


- ### Git ignore 적용 안될 때
  - git ignore에 파일명을 추가한 후에도 이미 푸쉬되어 있는 파일들은 지워지지 않는다.

```
git rm --cached . 
# 캐시에 남아있는 내용 삭제 옵션 -r, -rf 등

git add .
# ignore에 포함되어 있는 파일들을 뺀체 다시 stage 영역에 추가

git commit -m "fixed untracked files”
# 커밋!
```
