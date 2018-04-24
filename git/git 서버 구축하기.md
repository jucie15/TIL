## Git Server 구축하기

- #### Refer

  - ##### [Git 서버 구축하기](https://git-scm.com/book/ko/v1/Git-%EC%84%9C%EB%B2%84-%EC%84%9C%EB%B2%84%EC%97%90-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)

  - ##### [bare vs non-bare](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/)

- #### 구축하기

  - ##### on server

  ```python
  $ mkdir git-test 
  $ cd git-test
  $ mkdir test.git
  $ cd test.git
  $ git init --bare # 레포 생성
  ```

  - ##### on local

  ```Python
  $ cd my_project
  $ git init
  $ git add .
  $ git commit -m 'test init'
  $ git remote add origin ssh://user@address:port/git-path
  $ git push origin master
  ```

  - ##### 추후 서버에 있는 레포를 클론 받아 사용할 수 있고 공유가 가능해졌다~

  ​

- #### 알게된 것

  - ##### git-repo에는 bare repository와 non-bare repository가 있다.

    - ##### bare repository는 공유를 위한 레포로 워킹디렉토리나 체크아웃된 복사본이 없다. 하여 .git 하위디렉토리가 없으며 bare-repo 디렉토리 자체에 git 업데이트 기록을 저장하고  일반적으로 .git 확장자를 지정한다.