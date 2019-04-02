---
title: 5장. X윈도 테마, GRUB 배경화면
date: 2017-07-05 11:57:03
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
이 챕터는 책에서 중요도가 가장 떨어진다!
서버관리자가 되었을 때 리눅스를 PC로 사용하는 사람에게 도움을 주기위해 알아둘 필요는 있다.



#### 테마 설치하기

{% codeblock %}
$ rpm -qa gnome-tweak-tool  //설치되어있나 확인
$ gnome-tweak-tool    //툴 켜서 설정변경
{% endcodeblock %}


#### GRUB 이미지 설정
{% codeblock %}
$ su -c 'yum -y install grub2-starfield-theme'
  //암호입력    //su -c는 슈퍼바이저 권한으로 실행하는것
$ vi /etc/default/grub

GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
#GRUB_TERMINAL_OUTPUT="console"     //여기 주석처리
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/swap vconsole.font=latarcyrheb-sun16 rd.lvm.lv=centos/root crashkernel=auto  vconsole.keymap=us rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
GRUB_THEME="/boot/grub2/themes/system/theme.txt"  //여기추가

vi 저장후 종료
{% endcodeblock %}
이미지 파일을 centos폴더에 저장 (ball.png)
{% codeblock %}
mv /home/centos/ball.png /boot/grub2/themes/system
{% endcodeblock %}
실행하여 그림 이동

{% codeblock %}
# vi /boot/grub2/themes/system/theme.txt //
:set number

34행가서 이미지 파일이름 변경
# grub2-mkconfig -o /boot/grub2/grub.cfg  //적용
{% endcodeblock %}
