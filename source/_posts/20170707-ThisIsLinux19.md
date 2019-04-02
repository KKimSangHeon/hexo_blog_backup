---
title: 6장. LVM 개념과 구현, RAID에 CentOS 설치
date: 2017-07-07 12:42:04
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---

#### LVM
Logical Volume Manage
여러개의 하드디스크를 합쳐서 한 개의 파일 시스템으로 사용하는것으로 필요에 따라 다시 나눌 수 있다.
예로 2테라 두개를 합친 후 1테라 3테라로 나눠서 사용한다.

##### 물리 볼륨:
sda1, sdb1 등의 파티션
##### 볼륨 그룹:
물리 볼륨을 합쳐서 1개의 물리그룹으로 만드는것.
##### 논리 볼륨:
볼륨 그룹을 1개 이상으로 나눠서 논리 그룹으로 나눈 것

#### 실습
3기가, 2기가를 볼륨 그룹으로 만들고
3기가, 1기가, 1기가 논리 볼륨으로 만들어보자

##### 1. 2기가, 3기가 디스크 추가
버츄어머신 에딧에서..
##### 2. 파티션 설정
{% codeblock %}
# fdisk /dev/sdb
n
p
1
엔터
엔터
t
8e
w

# fdisk /dev/sdc
n
p
1
엔터
엔터
t
8e
w
{% endcodeblock %}
##### 3. 피지컬 볼륨으로 만들기
{% codeblock %}
# pvcreate /dev/sdb1
# pvcreate /dev/sdc1
{% endcodeblock %}

##### 4. 볼륨 그룹으로 만들기
{% codeblock %}
# vgcreate myVG /dev/sdb1 /dev/sdc1

# vgdisplay   //볼륨그룹 확인
{% endcodeblock %}

##### 5. 1기가 3기가 1기가로 나누자
{% codeblock %}
# lvcreate --size 1G --name myLG1 myVG
# lvcreate --size 3G --name myLG2 myVG
# lvcreate --extents 100%FREE --name myLG3 myVG
        //나머지 다써라

# ls -l /dev/myVG   //확인
{% endcodeblock %}

##### 6. 포멧하자
{% codeblock %}
# mkfs.ext4 /dev/myVG/myLG1
# mkfs.ext4 /dev/myVG/myLG2
# mkfs.ext4 /dev/myVG/myLG3
{% endcodeblock %}

##### 7. 마운트하자
{% codeblock %}
# mkdir /lvm1  /lvm2  /lvm3
# mount /dev/myVG/myLG1 /lvm1
# mount /dev/myVG/myLG2 /lvm2
# mount /dev/myVG/myLG3 /lvm3

# df //확인
{% endcodeblock %}

##### 8. fstab에 등록하자
{% codeblock %}
# vi /etc/fstab

팁 yy 한다음에 pp하면 복붙이된다.
/dev/myVG/myLG1         /lvm1    ext4     defaults      1 2
/dev/myVG/myLG2         /lvm2    ext4     defaults      1 2
/dev/myVG/myLG3         /lvm3    ext4     defaults      1 2
{% endcodeblock %}

### RAID 1 방식으로 디스크에 CentOS를 설치하자
392p 보고 하자


{% codeblock %}
# mdadm --detail -scan //확인해보자

# mdadm --detail /dev/md/root   //자세히보자

{% endcodeblock %}
