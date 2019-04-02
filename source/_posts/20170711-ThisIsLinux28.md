---
title: 10장. 센드메일 서버의 구현
date: 2017-07-11 17:30:49
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
네임서버를 만들었으니 이메일을 보내는 메일서버를 만들어 보자.

### 1.서버 메일서버를 만들자(naver)

#### 1.1 서버에서 메일 서버를 만들자(서버에서 진행)
{% codeblock %}
# yum -y install sendmail-cf dovecot  
          //sendmail-cf 보내는메일서버
          //devoct 받는 메일서버

# vi /etc/mail/sendmail.cf          
:set number
85를 Cwnaver.com 으로 수정
264  Addr=127.0.0.1 를 지운다
저장

외부 호스트가 메일을 전달할 수 있도록 허가
# vi /etc/mail/access  다음을 추가
naver.com       RELAY
daum.net        RELAY
192.168.111     RELAY

# makemap hash /etc/mail/access < /etc/mail/access  //변경내용 적용

사용자의 메일박스에서 메일을 꺼내 사용자에게 전달하는 도베캇 설정
# vi /etc/dovecot/dovecot.conf
:set number
24행 30행 33행 주석제거
저장
# vi /etc/dovecot/conf.d/10-ssl.conf
:set number
8행 ssl=yes로 수정
저장
# vi /etc/dovecot/conf.d/10-mail.conf
:set number
25행 주석제거
119행 mail_access_groups =mail 으로 수정
159행 주석제거
저장
{% endcodeblock %}

#### 1.2 서버에 사용자를 만들자(서버에서 진행)
{% codeblock %}
# adduser lee
# passwd lee
암호 lee
 이 사용자는 lee@naver.com
{% endcodeblock %}
#### 1.3 서비스를 시작하자(서버에서 진행)
{% codeblock %}
# systemctl restart sendmail
# systemctl enable sendmail

# systemctl restart dovecot
# systemctl enable dovecot
{% endcodeblock %}
#### 1.4 서버에서 만든 메일서버가 잘 작동하나 확인해보자(클라이언트에서 진행)
이를 위해 클라이언트에서 메일을 보낸다.(클라이언트에서 실행)
{% codeblock %}
# su

# vi /etc/resolv.conf
nameserver 192.168.111.100

# yum -y install evolution    //이메일 클라이언트 프로그램

프로그램->오피스->에볼루션
계속->계속    
전체이름: 이네이버
전자메일 주소: lee@naver.com
계속

서버종류 pop선택
서버: mail.naver.com
사용자이름: lee
포트: 995
암호화방식: SSL특정방식사용
계속
계속

서버 mail.naver.com
계속

이름 : 네이버 메일
계속
적용

계속 허용       //꼭 해야한다.
암호: lee
로그인 암호:centos

새로만들기
받는사람 lee@naver.com
{% endcodeblock %}
새로만들기를 눌러 lee@naver.com으로 메일을 하나 보내보자. 그러면 보낸편지함에 하나 들어있고 메일박스로 들어가있는상태. 그 후 보내기/받기를 누르면 받은편지함에 메일이 하나 생성된다.

### 2.서버B 메일서버를 만들자(daum)
#### 2.1 서버B에 메일 서버를 만들자(서버B에서 진행)
{% codeblock %}
# yum -y install sendmail-cf dovecot  
          //sendmail-cf 보내는메일서버
          //devoct 받는 메일서버

# vi /etc/mail/sendmail.cf          
:set number
85를 Cwdaum.net 으로 수정
264  Addr=127.0.0.1 를 지운다
저장

외부 호스트가 메일을 전달할 수 있도록 허가
# vi /etc/mail/access  다음을 추가
naver.com       RELAY
daum.net        RELAY
192.168.111     RELAY

# makemap hash /etc/mail/access < /etc/mail/access  //변경내용 적용

사용자의 메일박스에서 메일을 꺼내 사용자에게 전달하는 도베캇 설정
# vi /etc/dovecot/dovecot.conf
:set number
24행 30행 33행 주석제거
저장
# vi /etc/dovecot/conf.d/10-ssl.conf
:set number
8행 ssl=yes로 수정
저장
# vi /etc/dovecot/conf.d/10-mail.conf
:set number
25행 주석제거
119행 mail_access_groups =mail 으로 수정
159행 주석제거
저장
{% endcodeblock %}
#### 2.2 서버B에 사용자를 만들자(서버B에서 진행)
{% codeblock %}
# adduser kim
# passwd kim
암호 kim
 이 사용자는 kim@daum.net
{% endcodeblock %}
#### 2.3 서비스를 시작하자(서버B에서 진행)
{% codeblock %}
# systemctl restart sendmail
# systemctl enable sendmail

# systemctl restart dovecot
# systemctl enable dovecot

# systemctl stop firewalld    //방화벽 끄기
# systemctl disable firewalld //방화벽 끄기
{% endcodeblock %}
### 3. 최종테스트를 해보자
#### 3.1 윈도우 클라이언트에서 해보자
썬더버드를 받자. 이때 네이버에 접속되지 않는데 아까 도메인 서버를 100으로 세팅해놔서 그렇다..
이름 : 김다음
메일주소 : kim@daum.net
암호 : kim
POP3 선택하고 완료
lee@naver.com 으로 메일을 보내보자
클라이언트로 돌아와 보내기/받기를 눌러보자 그 후 회신을 눌러보자
그 후 윈도우 클라이언트로 와서 받기를 눌러보자.
