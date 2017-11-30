## ufw 방화벽 설정 후 ssh 접속 안되는 문제

1. #### Refer

   - ##### [EBS volume mount하기](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-using-volumes.html)

   - ##### [접속 문제 해결하기](https://stackoverflow.com/questions/21461499/how-to-recover-ssh-access-to-amazon-ec2-instance-after-ufw-firewall-activation-b)

   - ##### [EBS volume 분리하기](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-detaching-volume.html)

2. #### 문제

   - ##### ufw 방화벽 활성화를 시킨 후 ssh logout이 된 상태에서 ssh 접속이 다시 안된다.

3. #### Trouble Shooting

   1. ##### 기존 인스턴스 A를 stop한다.

   2. ##### EBS volume을 분리하여 다른 인스턴스 B에 연결한다.

   3. ##### B 인스턴스에 접속 하여 EBS volume을 mount시킨다.

      ```python
      $ lsblk # 사용 가능한 디스크 디바이스 및 마운트 포인트 확인
      $ sudo mkdir mount_point # ex) /opt/recover
      $ sudo mount device_name monut_point
      ```

   4. ##### 이 후 마운트 시킨 볼륨으로 접근하여 ufw disable 시키기

      ```python
      $ vi {your-ebs-mount}/etc/ufw/ufw.conf #에 접근하여  change enabled=yes to enabled=no
      ```

   5. ##### 후 EBS volume 마운트 해제

      ```python
      $ umount -d device_name # 마운트 시켰던 device umount
      ```

   6. ##### 콘솔에서 볼륨 분리 후 다시 A 인스턴스에 연결

   ##### 