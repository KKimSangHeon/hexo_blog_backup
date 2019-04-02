---
title: 12장. APM 개념과 설치, XE 설치와 운영
date: 2017-07-14 11:14:09
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
리눅스에서 가장 많이 활용하는것중 하나가 웹서버이다.
APM = Apache 웹서버 + 프로그래밍언어 PHP + 데이터베이스 MaraiaDB
리눅스 환경에서 사용될 경우 LAPM(Linux Apache,PHP,MariaDB)라고도 부름.
APM 이라는 소프트웨어는 존재하지 않으며 이 3가지가 서로 잘 연동되어 운영되도록 만든 환경을 APM이라 함
### APM 설치
#### 설치를 해보자
서버에서 진행
{% codeblock %}
# rpm -qa httpd php mariadb-server
httpd만 깔려있으므로 나머지를 깔자
# yum -y install mariadb-server mariadb php php-mysqlnd
# rpm -qa httpd php mariadb-server //깔린버전 확인해보자
# yum -y install php-gd
{% endcodeblock %}

#### 가동해보자
{% codeblock %}
# systemctl restart httpd
# systemctl restart mariadb
# systemctl restart php // php는 서비스가 아니라
                httpd에 포함되는 기능이라 시작이 안된다.

# systemctl enable httpd    //상시가동
# systemctl enable mariadb        

# firewall-config
영구적 선택 - http,https
mysql은 안열어도 됨. 어차피 안에서만 접근하고
외부에서 접근하지 않을것이므로   
옵션- 다시불러오기       
{% endcodeblock %}

#### 접속해보자
파이어폭스 키고 localhost

#### php 하나를 만들어보자
{% codeblock %}
# cd /var/www/html
# pwd
# ls
# touch phpinfo.php
# vi phpinfo.php
아래 한줄 입력
<?php   phpinfo();      ?>

{% endcodeblock %}
파이어 폭스 켜고 http://localhost/phpinfo.php 접속해보자

#### mariadb 확인해보자
{% codeblock %}
# mysql
{% endcodeblock %}

### XE 설치
{% codeblock %}
# vi /etc/httpd/conf/httpd.conf   //XE 외부에서 접근 가능하게
:set number
151 none 를 All로 변경
# systemctl restart httpd

# cd /var/www/html
# mv /root/다운로드/xe.zip .
# unzip xe*

# chmod 707 xe  //권한 변경
# cd /var/www/html/xe/modules/
# ls  //board는 게시판임
{% endcodeblock %}

### XE 실습
{% codeblock %}
# mysql
>GRANT ALL PRIVILEGES ON xeDB.* TO xeUser@localhost IDENTIFIED BY '1234';
>exit

# mysql -u xeUser -p1234
> CREATE DATABASE xeDB;
{% endcodeblock %}

### XE 설정
외부에서 해도되고 내부에서 해도된다. 즉 관계 없다.
윈 클라이언트로 하자.
192.168.111.100/xe 접속
한국어 . 다음
동의. 다음
다음
mysql 선택 다음
xeUser /1234 /xeDB 다음
kora선택 다음
aa@aa.com / 4321 /4321 관리자임. / admin

게시판 추가는 책을보자
