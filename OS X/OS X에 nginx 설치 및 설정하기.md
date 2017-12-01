## OS X에 nginx 설치 및 설정하기



- #### Refer

  - ##### [nginx설치 및 설정](http://devspark.tistory.com/entry/mac-%EC%97%90%EC%84%9C-nginx-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%84%A4%EC%A0%95-%ED%95%98%EA%B8%B0)

  - ##### [nginx conf 설정](http://sarc.io/index.php/nginx/61-nginx-nginx-conf)

- #### Install

  - ##### Brew install nginx

- #### Configuration

  - ##### conf 파일 위치: /usr/local/etc/nginx/nginx.conf

  - ##### Server-block 설정하기

    1. ##### conf 파일 직접 수정 - 하나의 서버일 경우

    2. ##### 여러개의 서버일 경우 

    ```python
    $ sudo vi /usr/local/etc/nginx/sites-available/mysite # 서버 설정 파일 생성
    $ ln -s /usr/local/etc/nginx/sites-available/mysite /usr/local/etc/nginx/sites-enabled/mysite # 설정 파일 링크 설정
    ```

- #### Shortcuts

  - ##### 서버 시작: sudo nginx

  - ##### 서버 종료: sudo nginx -s stop

  - ##### 서버 재시작: sudo nginx -s reload

  ​