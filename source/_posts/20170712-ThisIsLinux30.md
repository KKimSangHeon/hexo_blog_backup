---
title: 11장. MariaDB 설치/운영 , Windows에서 접속
date: 2017-07-12 18:21:35
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 마리아 DB 운영
#### DBMS 설치
{% codeblock %}
카페에 올려놓은 마리아 db를 세개 다 받자.
# yum -y remove mariadb-libs    // 버전이 달라 새로 설치하면 충돌우려..

# yum -y localinstall Maria*
# systemctl restart mysql //mysql이 오라클로 들어가자
  //개발자들이 마리아를 만들어냄..  

# systemctl status mysql
# checkconfig mysql on  // systemctl enable mysql 과 동일
              //상시가동하도록 설정

# firewall-config   //방화벽 열기
영구적 탭 -> 다시불러오기
# mysql   // 마리아디비 실행
> showdatabases;    //그냥 확인차

원칙은
# mysql -u root -p 라고 입력 후 비번입력해야함
지금은 비번을 설정 하지 않았으므로 엔터만 누르면 된다.
{% endcodeblock %}
#### 마리아 디비 암호 설정
{% codeblock %}
# mysqladmin -u root password '1234'
마리아 디비 사용자인 root는 비번이 1234

# mysql -u root -p //로그인
1234 입력
>exit
{% endcodeblock %}

#### 윈도우 클라이언트에서 마리아DB 접속
카페 접속해서 마리아디비 설치
리눅스는 서버 클라이언트 따로지만 윈도우용은 하나다.
다른건 설치하지 말고 클라이언트만 설치하자

##### 서버에서 실행
{% codeblock %}
# mysql -u root -p
> USE mysql
>SELECT user,host FROM user WHERE user NOT LIKE '';
루트 사용자는 외부에서 접속할 수 없다는 것을 확인.

>GRANT ALL PRIVILEGES ON *.* TO winuser@'192.168.111.%' IDENTIFIED BY '4321';
    //192.168.111.XXX 는 허용하겠다.(윈도우 유저의 아이피의 마지막이 변동가능성이 있기 때문에 %로 처리)
{% endcodeblock %}

##### 윈도우에서 실행
cmd 열기
cd "c:\Program Files (x86)"
cd "MariaDB 10.0"
cd MariaDB 10.0
cd bin

mysql -h 192.168.111.100 -u winuser -p
4321 입력
show databases;
