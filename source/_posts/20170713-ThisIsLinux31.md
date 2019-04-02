---
title: 11장. 쇼핑몰 데이터베이스 구축, Oracle 설치, Oracle에서 쇼핑몰 DB 구축
date: 2017-07-13 18:09:57
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
서버에서 실행해도 되고 윈 클라이언트에서 해도된다. 그러나 윈 클라이언트에서 해보자
#### 윈도우에서 실행
cmd 열기
cd "c:\\Program Files (x86)"
cd "MariaDB 10.0"
cd MariaDB 10.0
cd bin

mysql -h 192.168.111.100 -u winuser -p
4321 입력
쿼리문은 책을 참조하자.....


#### 오라클을 설치해보자
서버에서 실행하자

카페에서 링크타고 오라클을 들어가서 깔자
{% codeblock %}
# cd 다운로드/
# ls -l
# unzip oracle* //압축을 풀자
# ls -l Disk1/
# cd Disk1/

오라클을 설치하기 위해서는 가상메모리 2기가가 필요함.
그래서 추가적으로 2기가를 추가한다.
# dd if=/dev/zero of=/swapfile bs=1024 count=4194304
# mkswap /swapfile     //스왑메모리로 만들자
# swapon /swapfile     //스왑을 키자
# swapon -s //스왑 확인 총 (6기가 임을 확인)

스왑을 껏다 켜도 유지되도록 하자
# cd /etc/rc.d    
# chmod 755 rc.local
# vi rc.local
맨 밑에
swapon /swapfile 추가
# reboot
# swapon -s //스왑 확인 총 (6기가 임을 확인)

오라클을 본격적으로 설치하자
# cd 다운로드/Disk1/
# pwd
# ls -l
# yum -y localinstall oracle*
{% endcodeblock %}
#### 오라클을 설정하자
{% codeblock %}
# service oracle-xe configure
엔터(8080은 외부로 접속하는 포트)
엔터(1521은 sql+로 접속하는 포트)
1234엔터
1234엔터
y 엔터(자동으로 실행할꺼냐)
{% endcodeblock %}
#### 오라클 서비스를 시작하자
{% codeblock %}
# /etc/init.d/oracle-xe start   //오라클 시작

# vi /etc/bashrc    //맨아래 다음을 입력.
# . /u01/app//oracle/product/11.2.0/xe/bin/oracle_env.sh      
                  //오라클 환경설정하는 스크립트 실행하는것.
                  //껏다켜면 이 한줄을 입력해야 하지만
                  //그 과정을 없애기 위해 /etc/bashrc에 추가

# firewall-config
영구적 선택.
포트 탭 선택
추가 8080 tcp
추가 1521 tcp
(오라클을 센토스에서 만든것이 아니라 등록이 되어있지 않다.)
옵션 - Firewalld 다시불러오기
{% endcodeblock %}
#### 오라클에 접속해보자
웹브라우저 실행
192.168.111.100:8080/apex
internal
admin
1234
로그인
1234
1234
1234
apply
return
1234 로그인
#### 마리아db에서 입력한 쿼리문을 오라클에서 실행해보자.
{% codeblock %}
# mkdir /oradata    //데이터베이스가 생성될 디렉토리
# chmod 777 /oradata
# sqlplus   //오라클에서 데이터베이스 시작
system 입력 //오라클에서 가장 높은 관리자는 system
1234 입력
책보고 쿼리 입력
{% endcodeblock %}
