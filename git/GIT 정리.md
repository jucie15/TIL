## GIT 정리

- #### Knowledge

  - ##### tag

  - ##### cherrypick

- #### Refer

  - ##### <https://blog.panter.ch/2016/08/31/5-rules-to-git-better.html>

    <https://hackernoon.com/git-merge-vs-rebase-whats-the-diff-76413c117333>

    <http://softwareswirl.blogspot.kr/2009/04/truce-in-merge-vs-rebase-war.html>

    <https://lwn.net/Articles/328436/>

    <http://scottchacon.com/2011/08/31/github-flow.html>

    <http://www.darwinweb.net/articles/the-case-for-git-rebase>

    <https://www.digitalocean.com/community/tutorials/how-to-rebase-and-update-a-pull-request>

    <https://blog.carbonfive.com/2017/08/28/always-squash-and-rebase-your-git-commits/>

    <https://blogg.bekk.no/why-you-should-stop-using-git-rebase-5552bee4fed1>

    <https://blog.mwaysolutions.com/2015/07/23/git-merge-and-rebase-part-2-of-3/>

    <http://www.bitsnbites.eu/a-tidy-linear-git-history/>

    <http://www.bitsnbites.eu/git-history-work-log-vs-recipe/>

    <http://codeinthehole.com/tips/pull-requests-and-other-good-practices-for-teams-using-github/>

- #### Git Flow

  1. ##### Refer :

     - #####  [Git-flow-cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/index.ko_KR.html)

     - ##### [Git-flow의 종류](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)

  2. ##### master는 Release용

  3. ##### 태그를 달아서 버전 관리

  4. ##### develop branch에서 기능 관리

  5. ##### feature branch를 파서 새로운 기능 개발 시작

- #### 명령어 정리

```python
git init
# 리포지토리 초기화. '.git'폴더 생성. 이 폴더에 현재 폴더와 관련된 관리 정보가 저장. working tree라고 부른다. 변경 내역 등 관리.

git status
# 현재 레포의 상태 표시 

git add 파일명
# 스테이지 영역(커밋 전 임시 영역)에 파일 추가. 

git add -p 파일명
# -p 옵션 선택한 파일들의 변경 내역들 중 hunk 단위로 추가

git commit -m '커밋 메시지'
# 스테이지 영역에 기록된 시점들 파일을 실제 리포지토리 변경 내역에 반영.

git commit --amend
# 바로 전에 작성했던 커밋에 덮어쓰기(메시지 수정 가능)

git commit -am "message"
# add, commit 한번에 하기

git commit -v
# 커밋시 변경 내역 다시 확인

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

git diff branch1 branch2
# working tree, 스테이지 영역, 최신 커밋 사이 변경 확인

git diff --staged
# stage 영역에 있는 변경 내역들만 확인

git fetch --all
# 모든 정보 받아오기

git fetch origin
# 리모트 정보와 동기화

git branch
# 브랜치 목록 표시

git branch -va
# 리모트 브랜치 포함 모든 브랜치 목록 표시

git push origin --delete <branch>
# 리모트에 있는 <branch> 삭제

git checkout temp
# temp branch로 이동

git checkout -b temp
# temp branch생성 후 이동

git checkout HEAD~1 -> git checkout master
# 한단계전으로 돌아갔다 오기

git checkout <commit-log>
# 해당 커밋 상태로 돌아가기

git remote update
# 리모트 정보가 필요할 때

git remote add origin https://github.com/jucie15/Piggies.git
# 주소의 저장소를 원격 저장소의 주소로 지정

git remote set-url origin https://github.com/jucie15/Piggies.git
# 이미 설정되어 있는 원격 저장소의 주소를 해당 주소로 변경

git remote rename origin <name>
# 이미 설정되어 있는 원격 저장소의 이름을 해당이름으로 변경 

git stash/pop
# 스택구조의 임시저장소 stash를 하면 임시저장을 하고 pop을 하면 빼온다. !stash한것을 다른 브랜치에서도 pop할 수 있다.
```