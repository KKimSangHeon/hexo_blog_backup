---
title: 10장. 웹 메일의 설치 및 사용
date: 2017-07-12 03:40:05
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 웹 메일의 설치 및 사용
10장의 지금까지의 구현내용은 문제가 있다. 일반사용자는 POP3, SMTP 등에 대한 설정하는것에 대한 지식이 없으며 원하지도 않는다.
즉. 이메일 서버를 구축하려면 웹 메일까지 설정을 해주어야 한다.
우리는 이를 위해 다람쥐 메일이라고 불리는 squirrelmail을 사용한다.

#### 다람쥐 메일 설치(서버)
센토스에서 다람쥐는 제공하지 않으므로 페도라것을 사용해야한다. (페도라의 거의 모든 패키지는 센토스에서 돌아간다.)
{% codeblock %}
# wget http://download.hanbit.co.kr/centos/7/squirrelmail-1.4.22-13.fc20.noarch.rpm
# yum -y localinstall squi*     //로컬설치
# chown apache.apache -R /usr/share/squirrelmail/  //웹사용자인 아파치로 소유주 변경
# chown apache.apache -R /var/lib/squirrelmail/
# chown apache.apache -R /var/spool/squirrelmail/
# chown apache.apache /etc/squirrelmail/config.php

# /usr/share/squirrelmail/config/conf.pl    //설정할 수 있는 실행파일
2입력
1 (도메인 변경)
naver.com
A
4
mail.naver.com
8
other
s
r
10
1
ko_KR
2
euc-kr
s
r
s
q

간단하게 다람쥐 메일을 들어올 수 있게 설정
# vi /etc/httpd/conf/httpd.conf 맨위에 다음 한줄 추가
Alias /webmail/   /usr/share/squirrelmail/
# systemctl restart httpd  
# systemctl enable httpd   
포트를 추가해야하는데 방화벽을 아까 꺼놨기 때문에 따로 하지 않겠다.


웹을 www로 들어가기 떄문에 웹주소도 네임서버에 추가
# vi /var/named/naver.com.db    맨뒤에 다음 한줄 추가
www           IN     A  192.168.111.100
# systemctl restart named

{% endcodeblock %}
#### 클라이언트에서 테스트 해보자
파이어폭스에서 www.naver.com/webmail/ 입력
맨아래 예외 추가 -> 보안예외 확인
lee / lee 입력
편지쓰기->  kim@daum.net  ->전송
윈도우 클라이언트에서 받기를 눌러보자, 그 후 답장을 눌러 보내보자
