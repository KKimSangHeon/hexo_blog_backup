---
title: 19장. PXE 서버, 킥스타트
date: 2017-07-16 21:22:55
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
Centos를 100개 깔아야 하는 상황에는 여러가지 방법이 있겠지만 PXE 설치서버를 구성해놓는다면 쉽게 설치가 가능하다!
1.DHCP 서버, TFTP 서버, syslinux부팅파일, FTP 또는 웹서버를 PXE 설치 서버에 구성해 놓는다. 즉 PXE는 하나의 프로그램이 아니라 여러개의 프로그램을 잘 조합해 놓은것을 의미
2.Centos를 설치할 PC의 전원을 켠다.
3.설치할 파일을 네트워크로 전송해서 자동으로 설치

PXE는 초기 설치화면 즉 언어설정 전 까지만 완료해 주지만 킥스타트는 초기설정까지 해준다.

### 1.PXE 설치
#### 1.1 서버에서 설정
{% codeblock %}
# yum -y install syslinux dhcp tftp-server vsftpd //관련패키지 설치
{% endcodeblock %}

#### 1.1 DHCP 설정
{% codeblock %}
# systemctl stop firewalld
# systemctl disable firewalld

# vi /etc/dhcp/dhcpd.conf
맨아래 다음 입력
subnet 192.168.111.0 netmask 255.255.255.0 {
    option routers 192.168.111.2;
    option subnet-mask 255.255.255.0;
    range dynamic-bootp 192.168.111.120  192.168.111.199;
    option domain-name-servers 192.168.111.2;
    allow booting;
    allow bootp;
    next-server 192.168.111.100;
    filename "pxelinux.0";
}
입력 후 종료
{% endcodeblock %}
#### 1.2 TFTP 설정
{% codeblock %}
# vi /etc/xinetd.d/tftp
disable = no 로 변경
저장후 종료
{% endcodeblock %}
#### 1.3 ftp로 iso이미지 접근가능하게
오른쪽위 cd모양 우클릭 - 세팅 - Use ISO image... - Centos이미지 찾고 위에 Connected, Connect at power on 체크 - OK
{% codeblock %}
# umount /dev/cdrom
# mount /dev/cdrom /var/ftp/pub
  ftp를 접속할 경우 cd롬파일을 접근가능
{% endcodeblock %}

#### 1.4 부팅에 필요한 파일 준비
tftp는 부팅파일을 전송하는 역할이다.
{% codeblock %}
# cp /var/ftp/pub/images/pxeboot/vmlinuz /var/lib/tftpboot/
# cp /var/ftp/pub/images/pxeboot/initrd.img /var/lib/tftpboot/
# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
# ls -l /var/lib/ftpboot

# mkdir /var/lib/tftpboot/pxelinux.cfg
# cd /var/lib/tftpboot/pxelinux.cfg
# touch default   //부팅에 관련된 파일 생성
# vi default   다음을 입력
DEFAULT         CentOS7_Auto_Install

LABEL           CentOS7_Auto_Install
        kernel  vmlinuz
        APPEND  initrd=initrd.img  repo=ftp://192.168.111.100/pub

# ps -ef | grep dnsmasq   //충동하는 프로세스 kill위해
# kill -9 1696
# systemctl disable dnsmasq
{% endcodeblock %}

#### 1.5 관련 서비스 시작
{% codeblock %}
# systemctl restart dhcpd     //서비스 시작
# systemctl restart vsftpd
# systemctl restart xinetd   //tftp임
# systemctl enable dhcpd    //상시가동
# systemctl enable vsftpd
# systemctl enable xinetd
{% endcodeblock %}
#### 1.6 VMware에서 제공하는 DHCP를 끄자
워크스테이션 키고 - Edit - Virtual Network Editor - change setting 누르고-VMnet8선택 - Use local DHCP .. 체크 해제 후 - OK
이제 서버에서만 DHCP 서버를 제공한다.

#### 1.7 새로운 PC를 만들자
버츄어 머신에서 진행하자
버츄어머신 네임을 TestCom으로
로케이션을 Centos 폴더 내 TestCom으로 하자
그 후 부팅하면 설치가 된다.


### 2.킥스타트 설치
킥스타트는 교재를 참고하자.
