# 네이버 웹툰 크롤링 하기

- ### 개발 목표 

  - `Python requests`와 `BeautifuleSoup`을 이용해 크롤링
  - 네이버 웹툰 중 하나를 골라 모든 에피소드를 다운 받는다.


- ### BeautifulSoup란?

  - `HTML`을 `parsing`할떄 쓰는 `Python` 라이브러리
  - `HTML`로 부터 데이터를 받아와 객체화시킨다.

- ### 전체코드

  1. 해당 웹툰의 전체 에피스트 리스트 페이지에서 `BeautifulSoup`객체 `toon_soup`를 생성한다.
  2. 해당 `Soup` 객체를 통해 각 에피소드의 url을 받아온다.
  3. 받아온 `url`을 통해 각 에피소드 별 `BeautifulSoup`객체 `ep_soup`를 생성한다.
  4. `ep_soup`를 통해 웹툰 이미지 크롤링
  5. 해당 에피소드의 디렉토리 혹은 이미지 파일이 있을 경우 패스

```python
import os
import requests
from bs4 import BeautifulSoup

page_num = 0 # TOON_PAGE_NUM

while(1):
# 웹툰 페이지 순회
    page_num += 1

    toon_url = "http://comic.naver.com/webtoon/list.nhn?titleId=662774&weekday=wed&page=" + str(page_num) # 웹툰 페이지 URL

    toon_html = requests.get(toon_url).text
    toon_soup = BeautifulSoup(toon_html, 'html.parser') # 웹툰 페이지 HTML_PARSER 생성

   #  for main_img_tag in toon_soup.select('tr td a img'):
 		# # 각 에피소드의 메인 이미지 크롤링
   #      ep_main_img = main_img_tag['src']
    for a_tag in toon_soup.select('.title a'):
    	# 웹툰 페이지에서 각 에피소드의 이름과 URL 크롤링
        ep_url = a_tag['href']
        ep_url = 'http://comic.naver.com/' + ep_url # 각 에피소드의 url
        ep_title = a_tag.get_text() # 각 에피소드의 title

        ep_html = requests.get(ep_url).text
        ep_soup = BeautifulSoup(ep_html, 'html.parser') # EP 페이지 HTML_PARSER 생성

        for idx, img_tag in enumerate(ep_soup.select('.wt_viewer img'),1):
        	# 웹툰의 이미지를 모두 긁어온다.(wt_viewer class에서 img tag검색)
            img_url = img_tag['src'] # img tag의 src 속성 값

            filename = os.path.basename(img_url) # basename은 url에서 /구분 했을 떄 마지막 부분
            filepath = os.path.join("data", "고수", ep_title, filename) # 웹툰 name, EP name, 이미지name
            dirpath = os.path.dirname(filepath) # dirname은 url에서 /구분 헀을 때 마지막 부분만 뺸 나머지 부분

            # print(filepath)
            # break

            if not os.path.exists(dirpath):
            	# 디렉토리가 존재하지 않으면 생성
                os.makedirs(dirpath)

            if os.path.exists(filepath):
            	# 파일이 존재하지 않으면 생성
                print('이미 존재 : ', filename)
            else:
                print('다운받으라 : ', filename)
                headers = {
                	# 요청자에 따라 서버에서 거부될 수 있다.
                	# 원하는 응답을 받기 위해 헤더에 요청자 정보를 임의로 설정하여 보낼 수 있다.
                	# Referer : 요청 페이지에 대한 정보
                	# User-Agent : 요청 기기에 대한 정보

                    'Referer' : ep_url,
                    #'User-Agent' : 'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.7 (KHTML, like Gecko) Chrome/7.0.517.44 Safari/534.7',
                }
                image_data = requests.get(img_url, headers = headers).content
                filename = '{}.jpg'.format(idx)


                with open(filepath, 'wb') as f: # 이미지 파일 생성
                    f.write(image_data)

    if not toon_soup.select('.next'):
    	# 웹툰 페이지 다음 버튼이 없을 경우 종료!
        break
```

- ### 웹툰 페이지 PARSER 생성(BeautifulSoup객체)

  1. 웹툰 고수의 url에 `python requests`를 통해 요청을 보낸다.
  2. `Response`를 통해 `html`문서를 받아온다.
  3. 받아온 문서와 `html.parser`를 통해 `BeautifulSoup`객체를 생성한다.

```python
toon_url = "http://comic.naver.com/webtoon/list.nhn?titleId=662774&weekday=wed&page=" + str(page_num) # 웹툰 페이지 URL

toon_html = requests.get(toon_url).text
toon_soup = BeautifulSoup(toon_html, 'html.parser') # 웹툰 페이지 HTML_PARSER 생성
```

- ### 각 에피소드 페이지 PARSER 생성(BeautifulSoup객체)

  1. `toon_soup`를 통해 각 에피소드의 `url`을 받아와 `ep_soup`을 생성한다.

```Python
for a_tag in toon_soup.select('.title a'):
    # 웹툰 페이지에서 각 에피소드의 이름과 URL 크롤링
    ep_url = a_tag['href']
    ep_url = 'http://comic.naver.com/' + ep_url # 각 에피소드의 url
    ep_title = a_tag.get_text() # 각 에피소드의 title

    ep_html = requests.get(ep_url).text
    ep_soup = BeautifulSoup(ep_html, 'html.parser') # EP 페이지 HTML_PARSER 생성
```

- ### 웹툰 이미지 크롤링하기

  1. `ep_soup`을 통해 웹툰 이미지 태그를 찾아 다운받는다.
  2. 이미 해당 에피소드 디렉토리가 있거나, 이미지 파일이 있을 경우 패스한다.

```python
for idx, img_tag in enumerate(ep_soup.select('.wt_viewer img'),1):
    # 웹툰의 이미지를 모두 긁어온다.(wt_viewer class에서 img tag검색)
    img_url = img_tag['src'] # img tag의 src 속성 값
    filename = os.path.basename(img_url) # basename은 url에서 /구분 했을 떄 마지막 부분
    filepath = os.path.join("data", "고수", ep_title, filename) # 웹툰 name, EP name, 이미지name
    dirpath = os.path.dirname(filepath) # dirname은 url에서 /구분 헀을 때 마지막 부분만 뺸 나머지 부분

    # print(filepath)
    # break

    if not os.path.exists(dirpath):
        # 디렉토리가 존재하지 않으면 생성
        os.makedirs(dirpath)

        if os.path.exists(filepath):
            # 파일이 존재하지 않으면 생성
            print('이미 존재 : ', filename)
            else:
                print('다운받으라 : ', filename)
                headers = {
                    # 요청자에 따라 서버에서 거부될 수 있다.
                    # 원하는 응답을 받기 위해 헤더에 요청자 정보를 임의로 설정하여 보낼 수 있다.
                    # Referer : 요청 페이지에 대한 정보
                    # User-Agent : 요청 기기에 대한 정보

                    'Referer' : ep_url,
                    #'User-Agent' : 'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.7 (KHTML, like Gecko) Chrome/7.0.517.44 Safari/534.7',
                }
                image_data = requests.get(img_url, headers = headers).content
                filename = '{}.jpg'.format(idx)


                with open(filepath, 'wb') as f: # 이미지 파일 생성
                    f.write(image_data)
```

- ### 크롤링 종료

  1. 종료 시점을 웹툰 페이지에서 다음 버튼이 없을 경우 종료 한다.

```python
if not toon_soup.select('.next'):
    # 웹툰 페이지 다음 버튼이 없을 경우 종료!
    break
```

