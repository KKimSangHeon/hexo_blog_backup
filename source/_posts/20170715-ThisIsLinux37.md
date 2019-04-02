---
title: 14장. NFS 서버 설치와 운영
date: 2017-07-15 12:11:36
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
NFS 서버리눅스 사이에 파일이나 폴더를 공유하는것
NFS를 구현하면 다양한 클라이언트가 공유 디렉터리에 접근할 수 있다.
서버에서는 서버를 구현하고 클라이언트는 그것을 마운트하여 사용한다.

#### 서버를 NFS 서버로 구현(서버로 실행)
{% codeblock %}
# rpm -qa nfs-utils   //설치 확인(깔려있다.)
# vi /etc/exports //공유할 폴더를 지정하는 파일
/share          192.168.111.*(rw,sync) 입력
    //리드 라이트, 싱크까지 시킴
# mkdir /share
# chmod 707 /share/
# cp /boot/vmlinuz-3* /share/file1
# ls -l /share/
# systemctl restart nfs-server    //서비스 시작
# systemctl enable nfs-server   //상시가동
# exportfs -v    //공개된 폴더 보임

# systemctl stop firewalld //방화벽 끄기..
      //관련된 것이 많아서 끄는게 낫다.
{% endcodeblock %}
#### 클라이언트로 NFS 접속(클라이언트로 실행)
{% codeblock %}
# rpm -qa nfs-utils //패키지 설치여부 확인(깔려있다)

# showmount -e 192.168.111.100    //서버에서 마운트한 폴더 조회

# cd
# mkdir myShare
# su
 password 입력
# cd /home/centos
# mount -t nfs 192.168.111.100:/share myShare //마운트
# ls -l myShare

# cd myShare/
# touch abc
# touch bcd
{% endcodeblock %}
#### 클라이언트가 생성한게 서버에도 있나 확인
{% codeblock %}
# ls -l /share    //abc,bcd 파일이 존재한다
{% endcodeblock %}
#### 윈도우로 접속해보자
일반적으로는 NFS는 제공을 안하지만 엔터프라이즈의 경우엔 제공해준다.
제어판 - 프로그램 - 기능 사용/사용안함 클릭- nfs용 클라이언트 체크
확인
재부팅
관리자로 cmd를 열자
mount 192.168.111.100:/share *
내컴퓨터를 누르면 접속 가능

#### NFS서버의 그래픽 설정도구를 사용해보자
{% codeblock %}
페도라20에서 제공하므로 카페에서 다운받자
# yum -y install
# cd 다운로드
# yum -y localinstall system-config-nfs-1.4.2-1.fc20.noarch.rpm

# mkdir /share2
# system-config-nfs
{% endcodeblock %}
