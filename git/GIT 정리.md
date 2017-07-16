## GIT 정리

```
git init
# 리포지토리 초기화. '.git'폴더 생성. 이 폴더에 현재 폴더와 관련된 관리 정보가 저장. working tree라고 부른다. 변경 내역 등 관리.

git status
# 현재 레포의 상태 표시 

git add 파일명
# 스테이지 영역(커밋 전 임시 영역)에 파일 추가. 

git commit -m '커밋 메시지'
# 스테이지 영역에 기록된 시점들 파일을 실제 리포지토리 변경 내역에 반영.

git commit --amend
# 바로 전에 작성했던 커밋에 덮어쓰기(메시지 수정 가능)

git commit -am "message"
# add, commit 한번에 하기

git reset HEAD~2
# 2개 전의 커밋으로 되돌리기

git reset --hard HEAD^값 or 커밋로그 값
# HEAD, 스테이지, Working tree를 특정 커밋으로 복원.

git log 
# 커밋 로그 확인

git log <name>
# <name>폴더 혹은 파일의 로그만 확인

git push -u origin
# 현재 레포에만 존재하는 브랜치를 원격에 업로드

git push -u origin master
# 현재 레포에 있는 브랜치를 원격 레포의 master 브랜치로 설정

git push origin +master 
# +를 붙여주면 정보 손실 있어도 무시하고 푸쉬한다.

git diff
# working tree, 스테이지 영역, 최신 커밋 사이 변경 확인

git branch
# 브랜치 목록 표시

git branch -va
# 리모트 브랜치 포함 모든 브랜치 목록 표시

git checkout temp
# temp branch로 이동

git checkout -b temp
# temp branch생성 후 이동

git remote update
# 리모트 정보가 필요할 때

git remote add origin https://github.com/jucie15/Piggies.git
# 주소의 저장소를 원격 저장소의 주소로 지정

git remote set-url origin https://github.com/jucie15/Piggies.git
# 이미 설정되어 있는 원격 저장소의 주소를 해당 주소로 변경

git remote rename origin <name>
# 이미 설정되어 있는 원격 저장소의 이름을 해당이름으로 변경 
```