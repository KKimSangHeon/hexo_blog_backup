---
title: RAID 0,1,5 의 고장과 원상 복구
date: 2017-07-06 12:33:07
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
SCSI 2,4,6,9를 고장내 놓고 잘동작하는 지 확인 해 보겠다.

잘 동작하나 확인하기 위해 고장내기 전에 파일을 옮긴다.
{% codeblock %}
# cp /boot/vmlinuz-3*  /raidLinear/testFile
# cp /boot/vmlinuz-3*  /raid0/testFile
# cp /boot/vmlinuz-3*  /raid1/testFile
# cp /boot/vmlinuz-3*  /raid5/testFile

# halt -p
{% endcodeblock %}
vmplayer을 켜고 SCSI 2,4,6,9 디스크를 제거
쫌 지나면 응급복구모드로 실행이됨
암호를 입력해주자

ls -l /dev/sd* 을 하니까 당겨져 있다. sdc가 sdb 이런식으로

RAID 1, 5 는 데이터가 살아있어야 한다.
#### RAID 1 확인
{% codeblock %}
# df    //마운트가 다 끊겨있다.
# mdadm --run /dev/md1   //강제로 가동시키는 것
시작되었다.
# df  //raid1가 마운트 된것을 확인.
# ls -l /raid1    //데이터가 잘 있는지 확인
# mdadm --detail /dev/md1   //하나는 살아있고 하나는 removed인것을 확인

{% endcodeblock %}
#### RAID 5 확인
{% codeblock %}
# mdadm --run /dev/md5   //강제로 가동시키는 것
시작되었다.
# df  //raid5가 마운트 된것을 확인.
# ls -l /raid5    //데이터가 잘 있는지 확인
# mdadm --detail /dev/md5   //하나는 살아있고 하나는 removed인것을 확인
{% endcodeblock %}

#### RAIDLinear  확인
{% codeblock %}
# mdadm --run /dev/md9   //강제로 가동시키는 것
실행할 수 없다. 결함을 허용하지 않음
{% endcodeblock %}

#### RAID 0 확인
{% codeblock %}
# mdadm --run /dev/md0   //강제로 가동시키는 것
실행할 수 없다. 결함을 허용하지 않음
{% endcodeblock %}


#### 시스템이 재대로 실행되도록 해보자
{% codeblock %}
# mdadm --stop /dev/md9     //종료
# mdadm --stop /dev/md0     //종료

# vi /etc/fstab
md9,md0 주석 하고
# reboot

리부트를 했는데 복구모드로 실행이 된다면 컨트롤 D를 누르고
실행한 다음에 재 마운트를 해주면 된다.

# df    //마운트가 되어있는것 확인.
{% endcodeblock %}


#### 새로운 디스크를 장착하여 원상복구를 시켜보자
Linear RAID, RAID 0 은 데이터를 살릴 순 없다.
그러나 RAID 1, RAID 5는 데이터를 살릴 수 있다.

vmplayer에서 디스크 4개를 1기가씩 주고 추가하자. 알아서 빈곳을 잘 찾아간다.

우리가 조금전 추가했던것은 sdc, sde, sdg, sdi 이다.
2,4,6,9 로 들어갔으므로..

RAID 0, Linear Raid는 새로 만들어주어야 한다.
{% codeblock %}
# ls -l /dev/sd*    //
# fdisk /dev/sdc~sdi 까지
  n 입력
  p 입력
  엔터 엔터
  t 입력
  fd 입력
  w 입력

# mdadm --stop /dev/md9   //다시만들기위해 stop
# mdadm --create /dev/md9 --level=linear --raid-devices=2 /dev/sdb1 /dev/sdc1
#mdadm --detail /dev/md9 //확인해보자

# mdadm --stop /dev/md0   //다시만들기위해 stop
# mdadm --create /dev/md0 --level=0 --raid-devices=2 /dev/sdd1 /dev/sde1
# mdadm --detail /dev/md9 //확인해보자
{% endcodeblock %}

RAID 1, RAID 5는 새로만들면 안되고 추가해주어야 한다.
{% codeblock %}
# mdadm /dev/md1 --add /dev/sdg1

# mdadm /dev/md5 --add /dev/sdi1

# vi /etc/fstab

/dev/md9    /raidLinear   ext4    defaults    1 2
/dev/md0    /raid0   ext4         defaults    1 2
/dev/md1    /raid1   ext4    defaults    1 2
/dev/md5    /raid5   ext4    defaults    1 2

이렇게 바꿔준다.
# reboot
# df      //마운트 확인.
{% endcodeblock %}

데이터를 한번 확인해보자.

{% codeblock %}
# ls -l /raid0
drwx------ 2 root root   16384  7월  6 17:26 lost+found
-rwxr-xr-x 1 root root 4902656  7월  6 17:31 testFile

# ls -l /raid1
drwx------ 2 root root   16384  7월  6 17:27 lost+found
-rwxr-xr-x 1 root root 4902656  7월  6 17:31 testFile

# ls -l /raid5
drwx------ 2 root root   16384  7월  6 17:29 lost+found
-rwxr-xr-x 1 root root 4902656  7월  6 17:31 testFile

# ls -l /raidLinear
drwx------ 2 root root   16384  7월  6 17:25 lost+found
-rwxr-xr-x 1 root root 4902656  7월  6 17:31 testFile
{% endcodeblock %}
raid 1그리고 raid5 는 데이터가 있을것이라 예상했지만 raid0 그리고 raidLinear도 데이터가 존재한다. 어떻게 된 것일까? 운이 좋게 지워지지않은 영역에 존재하는 것일 것이다. 그러므로 데이터를 신뢰 할 수 없으며 이는 포멧을 해주는 것이 좋다.

{% codeblock %}
# umount /dev/md0   //포멧하기전에 마운트를 끊음
# umount /dev/md9

# mkfs.ext4 /dev/md0  //포멧
# mkfs.ext4 /dev/md9

# reboot    //리부트를 하지않고 바로 마운트를 해줘도됨.
          //어차피 리부트하면 마운트되어있으니까 아무거나 선택!
{% endcodeblock %}


raid 1, raid 5 의 고장난 디스크에 대해서 따로 작업을 해 줘야할줄 알았는데 --add만 하면 되어서 생각보다 간단하다고 생각했다.
