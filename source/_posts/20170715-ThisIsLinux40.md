---
title: 17장. 프록시 서버 설치와 운영
date: 2017-07-15 18:55:25
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 프록시서버?
웹서핑을 할 경우 웹브라우저가 외부로 가서 파일을 가져와 읽게 되는것이다. 두 브라우져가 같은곳을 접속하면 따로 가져오게 되는 현상이 발생할 수 있다.
 하지만 프록시 서버를 지정해놓으면 프록시 서버가 캐시에 a사이트 데이터를 저장 해놓고 다른 웹브라우져가 a서버를 요청하면 a 데이터를 보내준다.

#### 프록시서버 구현(서버에서)
{% codeblock %}
# yum -y install squid    //설치
# vi /etc/squid/squid.conf
:set nu
:26
acl centos7  src 192.168.111.0/255.255.255.0  //c클래스 지정
:54
http_access allow centos7
:62
주석 제거 하고 100을 1000으로
맨아래 다음추가
visible_hostname        centos7

# firewall-config
영구적 선택 - 포트 - 추가 3128 - 다시불러오기
# systemctl stop firewalld    //맘편하게 방화벽 끄는것
# systemctl restart squid
# systemctl enable squid
# systemctl status squid
{% endcodeblock %}

#### 웹브라우저에서 프록시서버 지정(클라이언트)
파이어폭스를 켜고 - 편집 - 환경설정 -고급 - 네트워크 - 설정 - 프록시 수동설정 192.168.111.100  포트 3128 입력 - 확인


#### 윈도우클라이언트에서 지정(윈클라이언트)
도구 -인터넷옵션 -LAN설정 프록시서버 192.168.111.100 3128 입력


{% codeblock %}

{% endcodeblock %}
