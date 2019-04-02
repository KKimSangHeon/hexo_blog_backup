---
title: 4장. 응급복구, GRUB, 커널 컴파일
date: 2017-07-04 16:09:17
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### 응급복구
261p 참고
이게 문제가 될 소지가 있지않냐?
그래서 처음 켤때 선택하는거에서 암호를 걸어야 한다.

#### GRUB 부트로더
/etc/default/grub 파일과 /etc/grup.d/ 디렉터리의 파일을 수정한 후 'grub2-mkconfig' 명령어를 실행해 설정함

{% codeblock %}
# vi /etc/default/grub
timeout = 30 으로 변경
distributor = "Hello" 로 변경
# grub2-mkconfig -o /boot/grub2/grub.cfg // 변경사항 반영

//여기서 부터 선택하는 곳에서 e 키 못누르게 비번 설정하는것
# vi /etc/grub.d/00_header
맨 아래 가서 다음코드 추가
cat << EOF
set superusers="thisuser"
password thisuser 4321
EOF
여기까지!
vi 빠져나와서
# grub2-mkconfig -o /boot/grub2/grub.cfg // 변경사항 반영
$ reboot
선택하는곳에서 e 누르면 암호 누르도록 함

{% endcodeblock %}
#### 커널
커널 : 하드웨어를 제어하는 기능
모든 하드웨어를 제어하는 코드를 다 넣으면 커널이 너무 커지고 무거워진다. 그러므로 잘 안쓰는 하드웨어 코드는 모듈에 빼내서 필요할때마다 사용
즉 모듈은 필요할때 쓸 수 있도록 커널의 코드를 밖으로 빼낸것이다!


{% codeblock %}
$ uname -r //커널버전확인
$ wget "https://kernel.org/pub/linux/kernel/v3.x/linux-3.17.4.tar.xz"
  //교재와 동일한 커널 설치
$ mv linux-3.17.4.tar.xz /usr/src
$ cd /usr/src
$ tar xfJ linux-3.17.4.tar.xz   //압축을 풀자
$ cd linux-3.17.4/

커널은 c 코드이므로 설치를 위해 컴파일러를 깔자
$ yum -y install gcc gcc-c++ qt qt-devel
$ make mrproper // 커널설정 초기화
$ make xconfig  //커널의 환경설정
  설정 후
$ make clean
$make ;  make modules_install ; make install  //순서대로 진행됨.


{% endcodeblock %}
