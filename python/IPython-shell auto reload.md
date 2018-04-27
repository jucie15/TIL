## IPython AutoReload

- ### Refer

  - ##### [ipython-docs](https://ipython.org/ipython-doc/3/config/extensions/autoreload.html)

  ### 

- ### AutoReload

  - ##### `autoreload` 확장모듈을 로드한다.

  - ##### `autoreload` 에 옵션을 추가해 실행하고

  - ##### 중간에 코드를 수정해보면 자동으로 업데이트된 것을 확인할 수 있다.

  ```powershell
  In [1]: %load_ext autoreload

  In [2]: %autoreload 2

  In [3]: from foo import some_function

  In [4]: some_function()
  Out[4]: 42

  In [5]: # open foo.py in an editor and change some_function to return 43

  In [6]: some_function()
  Out[6]: 43
  ```

  ### 

- ### Usage

  - ##### `%autoreload`

    - 모든 모듈을 자동으로 리로드한다.(`%aimport`에 의해 제외된 모듈 빼고)

  - ##### `%autoreload 0`

    - 리로드를 사용하지 않는다.

  - ##### `%autoreload 1`

    - `%aimport`로 불러온 모듈들을 수정된 코드를 실행하기 전에 리로드한다.

  - ##### `%autoreload 2`

    - `%aimport`로 불러온 모듈들을 제외하고 수정된 코드를 실행하기 전에 리로드한다.

  - ##### `%aimport` 

    - 자동으로 불러오거나, 불러오지 않을 모듈 설정

  - ##### `%aimport foo`

    - 'foo' 모듈을 가져와라.`%autoreload 1` 로 표시할 경우

  - ##### `%aimport -foo`

    - 'foo'모듈을 부러오지 마라.