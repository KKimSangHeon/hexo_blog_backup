---
title: 8장. 텔넷서버
date: 2017-07-08 17:32:45
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
이번장은 상당히 중요하다. 앞으로의 모든 장은 네트워크 서버를 구축하는데 8장이 첫 구축이다. 그러므로 8장을 잘 이해하면 큰 흐름을 이해 할 수 있다.

#### 텔넷서버
오랫동안 전통적으로 사용되어 온 원격 접속 방법이다.
텔넷 서버에 접속하려면 텔넷 클라이언트를 쓰면 된다. 각 서버마다 해당되는 클라이언트가 존재한다.

#### 텔넷 서버 구축 단계
1.텔넷 서버 설치
2.텔넷 서비스 시작
3.텔넷 전용 사용자 생성
4.방화벽 설정(포트 열기)
5.텔넷 서비스 상시가동
6.클라이언트에서 접속

### 실습
모든 가상머신을 초기화하자!
##### 1.텔넷 서버 설치
{% codeblock %}
# rpm -qa  | grep telnet     //텔넷이 설치되었나 확인
# yum -y install telnet-server    //설치되어있지 않아 설치
{% endcodeblock %}

##### 2.텔넷 서버 서비스 시작
{% codeblock %}
# systemctl restart telnet.socket
    //restart는 시작이 안되어있으면 새로 시작하고 시작되어있으면 재시작함.

# systemctl status telnet.socket  
      //가동여부 확인
{% endcodeblock %}

##### 3.텔넷 전용 사용자 생성
{% codeblock %}
# adduser teluser
# passwd teluser  //1234로 설정
{% endcodeblock %}

##### 4.방화벽 설정(포트 열기)
외부에서 접속이 가능하기 위해서 여는 것이므로 일단 내부에서 잘 접속이 되는지 확인 후 에 방화벽을 열자
{% codeblock %}
# yum -y install telnet //텔넷 클라이언트 프로그램 설치
# telnet 127.0.0.1    //자기 자신으로 접속
  teluser / 1234 입력
# exit //종료  

 이제 방화벽 설정하자
# firewall-config   //설정차을 띄우자
 설정 영구적 탭 선택  //서버는 한번 쓰는게 아니라 영원히 쓰기 때문에.
 영역 탭의 public 선택 후 서비스에서 telnet 체크
 옵션->Firewall 다시불러오기
 이제 포트가 열렸다.
{% endcodeblock %}


##### 5.텔넷 서비스 상시가동
포트가 열렸지만 컴퓨터가 껏다 켜면 제대로 작동안하기 때문에 다음을 입력
{% codeblock %}
# systemctl enable telnet.socket    //껏다 켜도 유지됨
{% endcodeblock %}
##### 6.클라이언트에서 접속

이제 윈 클라이언트를 부팅하고 접속해보자.
접속을 위해서는 서버의 IP주소가 필요하다. 서버에서 다음을 입력
{% codeblock %}
# ifconfig //192.168.111.100
{% endcodeblock %}
IP주소 확인 했으니 윈 클라이언트로 가서 제어판-프로그램-프로그램 및기능
Windows 기능 사용/사용안함 가서 맨아래 텔넷 클라이언트 기능 설치

윈도우 cmd를 열자
그 후 telnet 192.168.111.100
teluser /1234 입력 후 엔터
접속이 된다.
exit로 종료
