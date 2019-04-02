---
title: 13장. FTP 서버 설치와 운영
date: 2017-07-15 11:04:42
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
네임서버, 이메일서버, 웹서버 보다는 훨신 간단하다.
FTP 는 file transfer protocol의 약자로 파일을 전송하기 위한 서비스. 대용량 파일을 전송 할 때 FTP 서버는 성능이 좋다. ftp서버의 종류는 몇가지가 있는데 CentOS에서는 vsftpd를 제공한다. 페도라, 레드헷 엔터프라이즈 쪽에서 인기가 좋다.
proftpd는 주로 대형 사이트에서 오래동안 인기가 많았다.
pure-ftpd는 Troll-FTPd를 기반으로 프랭크 데니스가 2001년에 제작함.
사실 사용자입장에서는 어떤 ftp이든 관계가 없다.
### 1.vsftpd
#### 실습1.1 vsftpd 설치 및 운영(서버실행)
{% codeblock %}
# yum -y install vsftpd
      //165kb 밖에 안되는데 성능이 좋다!
# cd /var/ftp    //vsftpd의 홈 디렉토리
# ls -l     //일반적으로 pub라는 디렉토리에 파일을 넣어놓는다.
            //우리회사가 A라는 파일을 배포해야하면 일반적으로 pub폴더에 넣음.
# cd pub
# ls
# cp /boot/vmlinuz-3* file1

# systemctl restart vsftpd   //ftp 시작
# systemctl enable vsftpd    //상시가동
# firewall-config     //방화벽 열기
영구적탭 . ftp 선택 . 옵션 firewalld다시불러오기  
{% endcodeblock %}

#### 실습1.2 vsftpd 윈도우에서 접속해보자(윈클라이언트실행)
알드라이브 설치
192.168.111.100 입력
포트 21
익명로그인 체크
연결
파일 업로드가 안된다. 이유 : ftp는 기본적으로 다운로드만 가능하다. 아무나 업로드 가능하면 악성코드를 올릴 수 도 있다!
하지만 허용할 수 있다.

#### 실습1.3 vsftpd 업로드 가능하게 변경(서버에서 실행)
{% codeblock %}
# vi /etc/vsftpd/vsftpd.conf
29행 어나니머스가 업로드하는것을 허락한다. 주석해제
33행 어나니머스가 디렉토리를 만드는것을 허락한다. 주석해제

# chown ftp.ftp /var/ftp/pub  //펍 소유주 변경
# ls -l

# systemctl restart vsftpd //서비스재시작
{% endcodeblock %}

#### 실습1.4 vsftpd 명령어로 파일을 올리고 받아보자(서버B에서 실행)
{% codeblock %}
# yum -y install epel-release   //레드헷에 접속해서 다운받을수 있도록 설치
# yum -y install ncftp

# ncftp 192.168.111.100
>cd pub
>get file1    //file1 다운로드

>put anaconda-ks.cfg  //아나콘다파일 업로드

>ls -l  

>bye //종료
{% endcodeblock %}

### 2.proftpd
#### 실습2.1 proftpd 설치(서버실행)
{% codeblock %}
# yum -y remove vsftpd //충돌 나니까 제거

# yum -y install epel-release //proftpd도 레드헷에 있어서 해줘야함.

# yum -y install proftpd    //proftpd 설치

# vi /etc/proftpd.conf    //설정파일 열기
:set nu   
:356 주석처리
:425 주석처리 </IfDefine>
:383 DenyAll을 AllowAll로 변경
//이제 어나니머스도 접속 가능

# systemctl restart proftpd   
# systemctl enable proftpd

  방화벽은 아까 열었으므로 안열어도 된다.
{% endcodeblock %}
#### 실습2.2 윈클라이언트에서 접속(윈클라이언트 실행)

### 3. pure-ftpd
#### 실습3.1 pure-ftpd 설치(서버실행)
{% codeblock %}
# yum -y remove proftpd
# yum -y install pure-ftpd  //레드햇에서 제공한다.
# vi /etc/pure-ftpd/pure-ftpd.conf
:set nu
:77 익명사용자가 허용이 되어있다.
:286 yes를 no로 변경

# systemctl restart pure-ftpd
# systemctl enable pure-ftpd
{% endcodeblock %}
