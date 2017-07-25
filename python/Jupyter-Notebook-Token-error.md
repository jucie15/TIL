# Jupyter Notebook Token error

1. ##### ~/.jupyter/ 디렉터리 아래에 .jupyter_notebook_config.py 파일이 존재하는지 여부 확인

   ```
   jupyter notebook --generate-config
   ```

2. ##### ~/.jupyter/.jupyter_notebook_config.py파일을 열어서 c.Notebook.App.browser 변수를 찾아 설정해준다.

   ```
   c.NotebookApp.browser = u’safari’

   c.NotebookApp.browser에 지정하는 브라우저 이름들

   크롬
   ‘Chrome’ 

   사파리
   ‘safari'

   파이어폭스
   ‘firefox'
   ```

