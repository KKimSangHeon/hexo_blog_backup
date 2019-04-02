---
title: 6장. RAID6 와 RAID 1+0
date: 2017-07-07 09:47:34
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### RAID6
패리티가 2개이기 때문에 신뢰성이 높다

#### RAID 1+0
RAID 0,1 쓰는것

#### 실습
디스크 10개 스냅샷으로 돌아온 후 2기가 짜리 하나를 지워버린다.

##### RAID 6 생성

{% codeblock %}
# mdadm --create /dev/md6 --level=6 --raid-devices=4 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

# mkfs.ext4 /dev/md6    //포멧

# mkdir /raid6

# mount /dev/md6 /raid6

# df    //2기가 확인
{% endcodeblock %}

##### RAID 1+0 생성

이는 RAID 1을 두개 먼저 생성해야한다.

md2 ,md3를 RAID1으로 만들고 이 두개를 RAID 0으로 묶는다.

{% codeblock %}
# mdadm --create /dev/md2 --level=1 --raid-devices=2 /dev/sdf1 /dev/sdg1    
            //raid1 md2로 생성

# mdadm --create /dev/md3 --level=1 --raid-devices=2 /dev/sdh1 /dev/sdi1    
            //raid1 md3로 생성

# mdadm --create /dev/md10 --level=0 --raid-devices=2 /dev/md2 /dev/md3   
            //md2, md3로 raid0 생성

# mkfs.ext4 /dev/md10   //포멧

# mkdir /raid10

# mount /dev/md10  /raid10

# df
{% endcodeblock %}


##### 잘동작하나 확인

{% codeblock %}
# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 /raid6/testFile
# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 /raid10/testFile

# vi /etc/fstab

/dev/md6    /raid6   ext4   defaults    1     2
/dev/md10   /raid10  ext4   defaults    1     2
//재부팅되도 마운트 유지하기 위해 추가
{% endcodeblock %}


##### 고장내보자
SCSI 3, 5 ,8, 9 디스크 없애고 부트

RAID 6 확인
{% codeblock %}
# mdadm --run /dev/md6    //md6 실행
# df //확인
# ls -l /raid6      //데이터 확인

# mdadm --detail /dev/md6
{% endcodeblock %}

RAID 1+0 확인
{% codeblock %}
# mdadm --run /dev/md2
# mdadm --run /dev/md3

# mdadm --detail /dev/md10
{% endcodeblock %}
