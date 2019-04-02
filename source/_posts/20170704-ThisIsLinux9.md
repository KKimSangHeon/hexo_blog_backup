---
title: 4장. 네트워크 관련 개념과 명령어,SELinux
date: 2017-07-04 13:29:36
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### TCP/IP

우리가 사용하는 인터넷은 tcp/ip 기반이다. 가장널리사용되는 프로토콜의 한 종류

#### 호스트이름 도메인이름
호스트이름 : 컴퓨터에 지정된 이름
도메인 이름 : hanbit.co.kr rkxdms gudtlr

#### IP 주소
랜카드에 부여되는 중복되지 않는 유일한 주소

#### 네트워크 주소
같은 네트워크에 속해있는 공통된 주소

#### 브로드캐스트 주소
제일 뒤가 255인 주소
내부 네트워크의 모든 컴퓨터가 듣게 되는 주소

#### 게이트웨이, 라우터
라우터=게이트웨이
네트워크 간에 데이터를 전송하는 컴퓨터 또는 장비
#### 넷마스크, 클래스
넷마스크 : 네트워크의 규모를 결정
ex(255.255.255.0)

#### DNS 서버= 네임서버
URL을 IP로 변환해주는 서버


{% codeblock %}
$ nmtui //네트워크와 관련된 작업진행

$ rdate -s time.bora.net  //시간설정
$ nmtui     //설정 변경후 종료 네트워크매니저텍스트ui
$ systemctl restart network //네트워크 재시작 (해줘야함)

$ ifconfig  //네트워크장치에 할당된 ip주소 확인
$ ifdown ens32  //이거는 systemctl restart network
                //보다 강력하게 서버를 끄는것.
$ ifup ens32    //서버 재시작              

$ nslookup  naver.com //DNS서버의 작동을 테스트

$ ping www.yahoo.com //네트워크 응답 확인
$ ping -3 www.yahoo.com   //세번만 피을 때려라

{% endcodeblock %}

#### 주요 파일
네트워크 기본적인 정보가 설정되어 있는 파일
/etc/sysconfig/network  

/etc/sysconfig/network-scripts/ifcfg-ens32
ens32 장치에 설정된 네트워크 정보가 모두 들어있는 파일
ens32가 아니라 eno일수도 있다.
BOOTPROTO = none 고정아이피를 쓰겠다.
DNS1  DNS가 여러개 나올 수 있으므로 1이 필요
ONBOOT ="yes" 네트워크를 쓰겠다

/etc/resolv.conf
DNS 서버의 정보와 호스트 이름이 들어있다.
168.126.63.1 //kt에서 제공하는 네임서버이다
            //네임서버가 없을 때 사용가능

#### SELinux
보안에 취약한 리눅스를 보호하기 위해 탄생
책에서는 실습을 위해 기능을 꺼두었다.
/etc/sysconfig/selinux를 편집하였다.
SELINUX=disabled 이렇게..
