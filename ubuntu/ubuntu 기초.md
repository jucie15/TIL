- #### Refer

  - [Bash란? Bash쉘 설정하기](http://originalchoi.tistory.com/19)
  - [환경 변수 관련 명령어 정리](http://shaeod.tistory.com/748)


- #### Basic

  - ##### Alias 등록하기

    - `alias`란 본래 별명, 가명이라는 뜻을 가지고 있다. 이 뜻 그래도 터미널에서 명령어 약칭을 정하는 명령어라고 생각하면 된다.
    - bash 쉘 프로필이나 스크립트에 원하는 alias 구문을 추가한다.

    ```
    $vi ~/.bashrc
    alias python="python3"
    ```

- #### 명령어 정리

```
sudo netstat -tulpn
# 사용 포트 확인

sudo ufw allow 8080
# UFW 방화벽 허용 포트 추가

sudo ufw delete allow 8080
# UFW 방화벽 허용 포트 삭제

sudo ufw allow 'Nginx Full'
# UFW 방화벽에 Nginx 서버에 대한 엑세스 허용


```

