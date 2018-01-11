## [Notebook]Cannot assign requested address

1. #### Refer

   - ##### [TroubleShooting-github](https://github.com/jupyter/notebook/issues/2213)

2. #### TroubleShooting

   1. ##### ip와 port설정 후 접속

   ```Shell
   $ jupyter notebook --ip=xxx.xxx.xxx.xxx --port=xxxx
   $ jupyter notebook --ip=0.0.0.0 --port=80 --allow-root # Running as root is not recommended 에러가 날 경우
   ```

   ​