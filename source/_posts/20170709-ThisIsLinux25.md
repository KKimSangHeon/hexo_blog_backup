---
title: 9장. 도메인 이름 체계 개념, 캐싱 전용 네임서버 구축
date: 2017-07-09 10:29:00
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
초창기 인터넷에서는 1대의 네임 서버만으로 충분히 IP주소와 이름의 관리가 가능했다.

하지만 인터넷이 확장되면서 그것이 도메인 이름 체계가 고안되었다.

원칙은 www.nate.com. 이다. 맨 뒤에 .을 붙여줘야 한다.

#### 로컬 네임서버가 작동하는 순서
##### www.nate.com. 접속과정
1./etc/hosts 를 보고 없으면 /etc/resolv.conf 에 있는 로컬 네임서버를 참고한다.
2.하지만 이 로컬네임서버도 많은것을 알지 못하므로 얘도 루트 네임서버한테 물어본다. 이렇게. "내가지금 www.nate.com으로 가려한다."
3.그러면 루트네임서버가 "그건 내가 모르지만 내가 관리하는 com의 네임서버 ip를 줄게"
4.그러면 로컬네임서버는 다시 com네임서버에게 다시 물어본다
5.그러면 com네임서버가 nate ip를 알려준다. 그러면 nate.com아이피는 www의 ip를 응답해준다.

##### www.daum.net. 접속과정
루트네임서버 -> net 네임서버 ->daum네임서버 ->www네임서버

#### 이제 우리가 할 실습!
/etc/resolv.conf  에 있는 로컬네임서버를 만드는것.
서버컴퓨터 : 캐싱전용 네임서버 (PC에서 URL로 IP주소를 얻고자 할 때 해당하는 URL의 IP주소를 알려주는 네임 서버를 말함)

클라이언트,서버B 에서 /etc/resolv.conf의 주소를 서버컴퓨터로 한다.

#### 서버 설정

##### 1. 관련 패키지 설치
{% codeblock %}
# yum -y install bind bind-chroot
{% endcodeblock %}

##### 2. 누가 서버컴퓨터로 물어보든 응답해주게 설정
{% codeblock %}
# vi /etc/named.conf     //캐싱정보관련 설정파일
:set number
11행 보면 127.0.0.1 지우고 any로 변경 //누가 서비스를 요청하던 제공하겠다
12행 ::1 지우고 none로 변경
17행 localhost any로 변경
named.conf 파일을 수정함으로서 누가 서버컴퓨터가 누가물어보든 응답을 해준다.
{% endcodeblock %}

##### 3. 서비스 재시작 및 환경설정
{% codeblock %}
# systemctl restart named //네임서버의 서비스 이름은 named
# systemctl status named    //작동여부 확인
disable 가 있다는건 껏다켜도 동작하지 않는다는것.
# systemctl enable named   //껏다켜도 작동하도록 설정

# firewall-config
영구적 선택 -> dns 선택-> 옵션/firewalld다시불러오기

# nslookup    //내부적으로 잘돌아가는지 테스트
>server 192.168.111.100
>www.daum.net
>exit
{% endcodeblock %}

#### 클라이언트 설정
##### Client 켜고 실행
{% codeblock %}
# su -c 'vi /etc/resolv.conf'
password 입력

192.168.111.100 으로 수정

파이어폭스로 아무데나 접속해보자
{% endcodeblock %}
##### Server B 켜고 실행
{% codeblock %}
# vi /etc/resolv.conf
password 입력

192.168.111.100 으로 수정

# ping -c 3 www.yahoo.com

# yum -y install elinks //텍스트모드의 웹브라우져
# elinks    //실행
{% endcodeblock %}
##### 윈도우에서 설정
제어판 열기
네트워크 상태 및 작업보기
로컬영역 연결 클릭
속성
IPv4 속성
다음 dns 서버 주소 사용
기본 설정 DNS 서버에 192.168.111.100 입력

##### 윈도우에서 원래대로 돌리기

cmd 열고
ipconfig 입력
이더넷 어댑터 뒤에있는 글자 복사.

netsh interface ip set dns "로컬 영역 연결" dhcp
//로컬 영역 연결은 (이더넷 어댑터 뒤에있는 글자)
