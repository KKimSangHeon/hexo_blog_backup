---
title: 9장. 마스터 네임서버 구축, 라운드 로빈 방식 네임서버
date: 2017-07-09 14:49:08
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
앞에한건 캐싱전용이었다.
캐싱전용네임서버 - 자기가 관리하는건 없고 무조건 밖에 나가서 알아다가 알려줌.

마스터 네임 서버 - 자기가 관리하는 것들이 있다.


#### 마스터 네임 서버 구축

##### 1. 웹서버 하나 간단하게 만들자(서버)

{% codeblock %}
# rpm -qa httpd   //설치가 되어있다.
# systemctl status httpd  //시작되어있나 확인
# systemctl restart httpd //시작
# firewall-config
영구적 - http 체크 - firewalld 다시불러오기
# vi /var/www/html/index.html
<h1> CentOS 7 홈피 입니다</h1> //삽입
{% endcodeblock %}

##### 2. ftp서버 간단하게 만들자(서버B)
서버 B를 켠다.
{% codeblock %}
# yum -y install vsftpd
# firewall-cmd --permanent --add-service=ftp  //방화벽설정
# firewall-cmd --reload
{% endcodeblock %}

##### 3. 환경설정을 해주자(서버)
서버에서
{% codeblock %}
# vi /etc/named.conf
:set number
맨아래 가서
zone "centos.com" IN {type master;
                      file "centos.com.db";
                      allow-update{none;};
};
입력. 이 의미는 서버컴퓨터는 centos.com의 마스터 네임서버다. 그리고 상세정보는 centos.com.db(임의로 지은것)에 들어있다.

# named-checkconf   //방금작성한것 문법적으로 잘못된것 없나 확인
{% endcodeblock %}

##### 4. centos.com.db를 만들자(서버)
{% codeblock %}
# cd /var/named
# pwd
# ls
# touch centos.com.db
# vi centos.com.db
아래 추가
$TTL 3H
@    SOA  @   root. (2 1D 1H 1W 1H)
     IN   NS  @
     IN   A   192.168.111.100

www  IN   A   192.168.111.100
ftp  IN   A   192.168.111.200

위 세줄은 항상 써주는것.
네번째는 자기 자신의 ip 입력
나머지 아래 두줄은 알려줄것.

# named-checkzone centos.com centos.com.db   //오류있는지 확인
# systemctl restart named
방화벽 설정은 앞에서 해줬으니 안해도됨
{% endcodeblock %}


##### 5. 접속 테스트해보자
클라이언트 켜자
{% codeblock %}
$ su -
$ vi /etc/resolv.conf
맨 아래 192.168.111.100 으로 수정

www.centos.com 접속해보자
{% endcodeblock %}
ftp 접속해보자
서버 B에서 다음을 실행하자(ftp서비스 시작)
{% codeblock %}
# systemctl restart vsftpd
# systemctl status vsftpd
{% endcodeblock %}
클라이언트에서 접속해보자
{% codeblock %}
# ftp ftp.centos.com
 // anonymous
 //엔터
{% endcodeblock %}

#### 라운드 로빈 방식의 네임 서버
이전에 실습한 방식은 여러명이 동시에 접속할 경우 서버 하나에 부하가 걸린다. 그래서 웹 서버를 여러개 만들어서 요청이 들어올 때마다 번갈아가면서 요청처리.

##### 네이버가 이 방식을 쓴다.
{% codeblock %}
# nslookup
www.naver.com
두개가 넘어온다.
{% endcodeblock %}

#### 실습.
www.john.com 으로 접속 했을 때 각각 다른 페이지를 뿌려주는실습.(라운드로빈 방식 확인가능)

{% codeblock %}
# nslookup
> www.yes24.com     //아이피 주소를 확인하기위해
> www.danawa.com   //아이피 주소를 확인하기위해
> www.nate.com     //아이피 주소를 확인하기위해
{% endcodeblock %}
{% codeblock %}
# cd /var/named
# vi centos.com.db

아래 두줄 지우고 다음으로 대체
www          IN      CNAME       webserver.centos.com.
webserver    100     IN          A     61.111.13.51
             200     IN          A     119.205.194.11
             300     IN          A     120.50.131.112

순차적으로 주소를 알려준다. 100,200,300 은 순차번호이다.

# systemctl restart named
# nslookup
>server 192.168.111.100
>www.centos.com
결과가 세개가 온다.

{% endcodeblock %}

##### 클라이언트에서 접속해보자
{% codeblock %}
# vi /etc/resolv.conf
//192.168.111.100 이 맞나 확인
//아니면 설정하기
{% endcodeblock %}
웹 브라우져 닫고
www.centos.com 들어가보자(여러번)
