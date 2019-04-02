---
title: 8장. SSH서버, VNC서버
date: 2017-07-08 18:16:00
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### SSH서버
텔넷과 용도는 동일하지만 보안이 강화되어있음
명령문을 입력 시 암호화 되어서 날라간다. SSH서버를 쓰는것이 권장사항이다. CentOS에서 쓰는 SSH서느는 openSSH 서버라 함.

#### 실습

##### 1.서버가 설치되었는지 확인
{% codeblock %}
# rpm -qa | grep openssh
설치되어있다.
{% endcodeblock %}
##### 2.가동되고 있는지 확인
{% codeblock %}
# systemctl status sshd
Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled)      //enable는 상시가동이라는 뜻
Active: active (running) since 토 2017-07-08 17:43:06 KST; 1h 5min ago //돌고 있다.
즉 ssh는 기본적으로 centos에서 깔려있고 가동되어 있다.
기본적으로 쓰인다!!!
{% endcodeblock %}

##### 3.방화벽 열렸는지 여부 확인
{% codeblock %}
# firewall-config
쭉 내리면 ssh가 켜져있다.
{% endcodeblock %}

##### 4.새로운 리눅스(Client)에서 접속해보자
클라이언트 켜자.
{% codeblock %}
# rpm -qa | grep openssh  //깔려있나확인

# ssh teluser@192.168.111.100   //telnet와 접속방법이 다름
# ifconfig  //서버컴퓨터의 아이피 확인
{% endcodeblock %}

##### 5.윈도우에서 접속해보자
Putty 설치
192.168.111.100 접속
참고로 ssh는 포트 22 telnet는 23
한글이 깨지면 오른쪽 누르고 유니코드로 전환
abc.txt를 하나 만들고 gedit abc.txt를 실행하면 되질 않는다.
왜냐하면 텔넷,SSH는 텍스트 모드로만 전송이 되기 때문에.
즉 그래픽 명령어는 아얘 쓸 수 없다.

#### 그럼 telnet ssh 다른점이 없는건가?
그렇다. 기능은 같으나 ssh가 보안이 추가된 것이다.

### VNC 서버
X 윈도우 환경으로 원격접속을 사용하고 싶을 때.
telnet ,ssh 보다 많이 느리다. 그래픽이 왔다갔다하기 때문에!

#### 1.VNC 설치 및 설정

{% codeblock %}
# rpm -qa | grep tiger  //설치유무 확인
//미니멈 버전이 깔려있다. 다시깔자
# yum -y install tigervnc-server
{% endcodeblock %}
VNC는 사용자한테 너 접속해, 너 화면번호 몇번 써 를 지정해야한다.
#### 2.센토스 사용자 너 1번화면으로 접속해를 설정
{% codeblock %}
# cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
//1번화면을 쓰기위한 작업
# vi /etc/systemd/system/vncserver@\:1.service
 :set number
40행,41행 의 <USER>을 지워버리고 centos로 고침
 그리고 저장 후 종료
{% endcodeblock %}

#### 3.방화벽 열기
{% codeblock %}
# firewall-config
{% endcodeblock %}
설정에서 영구적 선택
쭉 내려서 vnc-server체크
옵션에서 firewalld 다시불러오기 클릭

#### 4.centos사용자가 1번화면으로 들어올 때 전용 비밀번호를 생성
즉 vnc 전용 비밀번호 생성
{% codeblock %}
# su - centos
# vncserver
암호는 123456으로 설정
{% endcodeblock %}

#### 5.접속해보자
리눅스 클라이언트에서 접속
{% codeblock %}
# rpm -qa | grep tiger  //깔려있지 않은것을 확인

# su -c 'yum -y install tigervnc'   //루트 권한으로 실행하는것.
# vncviewer 192.168.111.100:1   //1은 디스플레이 번호
  password 입력
{% endcodeblock %}
윈도우 클라이언트에서 접속
카페이서 tigervnc 설치파일 깔기
옵션해서 해상도를 낮추면 사용할만 함(color level)
tigervnc vncviewer을 켜고
192.168.111.100:1 입력하고 암호 123456
