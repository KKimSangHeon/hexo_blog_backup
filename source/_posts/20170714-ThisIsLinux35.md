---
title: 12장. 웹서버 설정파일,APM 컴파일 설치
date: 2017-07-14 18:45:57
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### x윈도를 통해 httpd.conf파일을 설정해보자
httpd.conf는 웹서버 설정파일이다. 책을 통해 이 내용을 알아보자.
{% codeblock %}
카페에서 system-config-httpd 파일을 받자.
# yum -y localinstall system-config-httpd-1.5.5-6.fc20.noarch.rpm

# system-config-httpd
{% endcodeblock %}

### 소스를 컴파일해서 APM을 설치
최적화된 APM을 구성할 수 있는 장점.
#### 아파치 설치
{% codeblock %}
# yum -y remove httpd   //컴파일 한것을 쓰기위해 깔린것을 지움

# yum -y install gcc gcc-c++  //컴파일러 설치

# cd 다운로드/

# tar xfj pcre*   //압축풀기
# cd pcre-8.36/
# ./configure ; make; make install //세개의 명령어 한꺼번에

# cd 다운로드/
# tar xfj httpd-2.4.10.tar.bz2
# tar xfj apr-1.5.1.tar.bz2
# tar xfj apr-util-1.5.4.tar.bz2

# mv apr-1.5.1 httpd-2.4.10/srclib/apr  
          //파일이름을 apr로 srclib에저장
# mv apr-util-1.5.4 httpd-2.4.10/srclib/apr-util
          //파일이름을 apr-util로 srclib에 저장
# ls httpd-2.4.10/srclib

# cd httpd-2.4.10/
# ./configure  --with-included-apr --with-pcre=/usr/local/bin/pcre-config --prefix=/web/httpd2/  ; make ; make install      
# vi /etc/ld.so.conf
마지막에 다음 두줄 추가
/web/httpd2/lib
/web/httpd2/modules
# ldconfig //방금 추가한 내용 적용
# cp /web/httpd2/bin/apachectl /etc/init.d/httpd2  //아파치 시작스크립트 복사
# vi /etc/init.d/httpd2
맨 아래 다음 두줄 추가
# chkconfig: -85 15
# description: 아파치 웹서버 입니다.

# chkconfig httpd2 on //부팅될때마다 시작되도록
# systemctl restart httpd2
# systemctl status httpd2
localhost로 접속해보자

APM 중 아파치 설치 완료!
{% endcodeblock %}
#### 마리아 db 설치
{% codeblock %}
두번째 마리아 db는 컴파일된 바이너리 코드를 설치하자
(카페에서 미리 받아놓자)
# cd /root/다운로드
# tar xfz mariadb-10.0.15-linux-x86_64.tar.gz
# mv mariadb-10.0.15-linux-x86_64 /web/mariadb

# groupadd mysql  //mysql 그룹 추가
# adduser -M -d /web/mariadb -g mysql -s /bin/false -r mysql
    //실제 사용하지 않는 유저 생성

# /web/mariadb/scripts/mysql_install_db --user=mysql --basedir=/web/mariadb --datadir=/web/mariadb/data
//마리아 디비 초기화

# cp -a /web/mariadb/support-files/my-huge.cnf /etc/my.cnf
y
# vi /etc/my.cnf
29행에 다음 두줄 추가
basedir         = /web/mariadb
datadir         = /web/mariadb/data

# cp -a /web/mariadb/support-files/mysql.server /etc/init.d/mariadb
  //실행스크립트 복사
# systemctl restart mariadb
# chkconfig mariadb on //상시가동되도록

# /web/mariadb/bin/mysqladmin -u root -p password
엔터
1234
1234
//마리아 디비 관리자 암호 변경

# PATH=$PATH:/web/mariadb/bin
# mysql -u root -p
1234 입력
//정상작동하는지 접속


{% endcodeblock %}
#### php 설치
{% codeblock %}
# cd /root/다운로드
# yum -y install libxml2-devel openssl-devel libjpeg-devel libpng-devel
//관련 패키지 설치

# tar xfj php-5.6.4.tar.bz2
# cd php-5.6.4
# ./configure --with-mysql=/web/mariadb --with-apxs2=/web/httpd2/bin/apxs --with-mysqli=/web/mariadb/bin/mysql_config --with-imap-ssl --disable-debug --with-iconv --with-gd --with-jpeg-dir --with-png-dir --with-libxml-dir --with-openssl ; make ; make install  
//컴파일 및 설치

# ls -l /web/httpd2/modules/libphp5.so
//제일 중요한 파일. 이파일을 위해 컴파일을 했다.
# vi /web/httpd2/conf/httpd.conf
147행에 모듈이 추가 된것을 확인
376행에 AddType application/x-httpd-php .php .php3 .php4 .php5 .htm .html .inc
아파치 웹서버가 376행의 타입까지 인식을 하도록 만듬

# cp php.ini-production /etc/php.ini
# systemctl stop httpd2
# systemctl start httpd2
# systemctl status httpd2

# firewall-config
영구적 . http 선택 옵션 firewalld 다시불러오기

# cd /web/httpd2/htdocs/ //웹서버의 홈폴더
# vi phpinfo.php      //샘플파일 만들기
다음 한줄 추가
<?php phpinfo(); ?>

http://192.168.111.100/phpinfo.php 접속해보자
{% endcodeblock %}

과연 이 과정들을 거쳐 만들어진것들이 최고의 성능을 낼까?
그것은 확실히 모른다. 현재 패키지들이 잘 만들어져 나오기 때문에 굳이 컴파일을 통해 진행된것들이 훨신 좋은 성능을 얻거나 하지는 않는다.
학습차원에서 컴파일을 진행해 보았다.

##### 12장은 활용도가 높으므로 익혀두자
