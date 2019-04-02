---
title: 10장. 메일서버 개념, 메일서버를 위한 네임서버 구현
date: 2017-07-11 00:56:35
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 메일서버
네임서버에 대한이해가 필수적이므로 이해하고 넘어올것.
E-mail의 송수신에서 사용되는 프로토콜

다음, 네이버의 메일서버 계정을 갖고있다면 kim@daum.net  /   lee.naver.com 가 있다.

kim@daum.net -> lee.naver.com 메일전송
smtp를 통해 kim 메일서버의 큐에 들어가고 여유가 있을 때 smtp 프로토콜을 이용해 lee의 메일서버로 보내놓는다. 메일서버는 메일 박스에 넣어놓고 lee는 pop3 imap프로토콜을 써서 자신의 컴퓨터로 확인한다.
보내는 프로토콜 : smtp
받는 프로토콜 : POP3 IMAP

### 실습계획
Server : naver 메일서버, 네임서버 역할을 한다.
Server(B) : daum 메일 서버
WinClient : 메일 클라이언트
Client : 메일 클라이언트

#### 1. 메일서버를 구현하기 전에 네임서버를 구현하자
##### 1.1 다음을 서버에서 실행하자
{% codeblock %}
# vi /etc/hostname    
   첫줄 지우고 mail.naver.com 입력

# vi /etc/hosts
   맨 뒤에 다음 입력
   192.168.111.100     mail.naver.com

# yum -y install sendmail   //메일서버를 구현하려면 sendmail이 설치되어 있어야함
# vi /etc/mail/local-host-names
    아래 mail.naver.com 입력

# vi /etc/sysconfig/network
    HOSTNAME=mail.naver.com 입력

# reboot
{% endcodeblock %}

##### 1.2 다음을 서버 B에서 실행하자
{% codeblock %}
# yum -y install sendmail

# vi /etc/hostname
    첫줄 지우고 mail.daum.net 입력

# vi /etc/hosts
   맨 뒤에 다음 입력
   192.168.111.200     mail.daum.net

# vi /etc/mail/local-host-names
    아래 mail.daum.net 입력

# vi /etc/sysconfig/network
    HOSTNAME=mail.daum.net 입력

# reboot    
{% endcodeblock %}
##### 1.3 서버를 마스터네임 서버로 만들자 (서버에서 실행하자)
{% codeblock %}
# yum -y install bind bind-chroot

# vi /etc/named.conf
:set number
11행 127.0.0.1 지우고 any로
12행 ::1 지우고 none로
17행 localhost 지우고 any로
여기까지만 하면 캐싱전용 네임서버이다.
우리가 만들것은 마스터 네임서버이므로 맨아래 다음을 추가
zone      "naver.com"     IN {
          type        master;
          file        "naver.com.db";
          allow-update  { none; };
};
zone 위에 커서 올리고 5yy 누르고 p(5줄 복사)
zone      "daum.net"     IN {
          type        master;
          file        "daum.net.db";
          allow-update  { none; };
};
저장 후 종료

# cd /var/named
# touch naver.com.db
# vi naver.com.db
$TTL          3H
@             SOA    @  root.  ( 2 1D 1H 1W 1H )
              IN     NS @
              IN     A  192.168.111.100
              IN     MX 10    mail.naver.com.      
              //달라진부분; mail exchange 메일이 오면 mail.naver.com한테 메일처리를 하게해라
mail          IN     A  192.168.111.100

# cp naver.com.db    daum.net.db
      //내용이 비슷하니까 복붙
# vi daum.net.db
$TTL          3H
@             SOA    @  root.  ( 2 1D 1H 1W 1H )
              IN     NS @
              IN     A  192.168.111.200
              IN     MX 10    mail.daum.net.

mail          IN     A  192.168.111.200
으로 수정

# named-checkconf   //name.conf파일을 확인
    //아무메세지가 없으면 문법문제 없음
# named-checkzone naver.com  naver.com.db
# named-checkzone daum.net  daum.net.db

포트는 여러개 열어야 하는데 복잡하므로 아얘
포트문제는 생기지 않도록 다 열어놓자. 즉 방화벽을 꺼놓자.
# systemctl  stop  firewalld    //방화벽 끄기
# systemctl  disable firewalld  //재부팅해도 꺼지도록

# systemctl restart named   //네임서버 시작
# systemctl enable named    //재부팅해도 켜지도록

# systemctl status named    //잘 돌고있는지 확인

# nslookup //실제 내부에서 잘동작하는지 확인
>server 192.168.111.100
>mail.naver.com
Address: 192.168.111.100  //성공

>mail.daum.net
Address: 192.168.111.200  //성공
{% endcodeblock %}

#### 2.각 컴퓨터의 로컬네임 서버를 생성한 네임서버로 지정하자
##### 2.1 서버의 네임서버를 지정
{% codeblock %}
# cd /etc/sysconfig/network-scripts/
# ls      //수정할 파일을 확인하자 ens....
# # vi ifcfg-ens32  // 껏다 켜도 유지되도록 설정
DNS1=192.168.111.100 으로 하자
# systemctl restart network
# cat /etc/resolv.conf    //확인
{% endcodeblock %}
##### 서버 B의 네임서버를 지정
{% codeblock %}
# cd /etc/sysconfig/network-scripts/
# ls      //수정할 파일을 확인하자 ens....
# # vi ifcfg-ens32  // 껏다 켜도 유지되도록 설정
DNS1=192.168.111.100 으로 하자
# systemctl restart network
# cat /etc/resolv.conf    //확인
{% endcodeblock %}
##### 2.2 클라이언트의 네임서버 지정
클라이언트는 고정ip가 아니기 때문에 껏다 켜면 vmware가 제공하는 2번으로 바뀌기 때문에 클라이언트는 껏다 켜지 않겠다. 그러므로 conf파일만 수정하자
{% codeblock %}
$ su -
# vi /etc/resolv.conf
nameserver 192.168.111.100
으로 수정
# exit

# nslookup
> mail.daum.net
Address: 192.168.111.200   //확인

> mail.naver.com
Address: 192.168.111.100  //확인
{% endcodeblock %}
##### 2.3 윈도우 클라이언트 설정
{% codeblock %}
cmd 열기

ipconfig    //
이더넷 어댑터 로컬 영역 연결

netsh interface ip set dns "로컬 영역 연결" static 192.168.111.100

ping mail.daum.net
{% endcodeblock %}


{% codeblock %}
#
{% endcodeblock %}
