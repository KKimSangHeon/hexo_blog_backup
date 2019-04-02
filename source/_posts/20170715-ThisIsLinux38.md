---
title: 15장. Samba 서버 설치와 운영
date: 2017-07-15 14:37:36
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
NFS 유닉스와 유닉스 유닉스와 리눅스간 파일공유이고
삼바는 유닉스(리눅스)와 윈도우즈간에 공유하는것.

### 1.윈도우가 삼바 서버
#### 1.1 윈도우 클라이언트를 삼바 서버로 만들자(윈클라이언트실행)
C 드라이브에 smbShare 폴더만든다. 그 폴더 속성 들어가서 공유탭의 공유버튼 누르고 Everyone 선택하고 추가 그 후 Everyone은 읽기/쓰기로 선택

명령프롬프트 관리자 모드로 열고 net user root 1234 /add 입력.
    //윈도우즈 사용자 추가
ipconfig
192.168.11.131임을 기억하자

#### 1.2 삼바 클라이언트로 접속해보자(서버에서 실행)  
{% codeblock %}
# rpm -qa | grep samba
# smbclient -L 192.168.111.131
1234입력

# mkdir /sambaMount
# mount -t cifs //192.168.111.131/smbShare /sambaMount
        //윈도우는 //로 해야함
1234입력

# ls -l /sambaMount

# cp /boot/vmlinuz-* /sambaMount
{% endcodeblock %}

### 2. 리눅스가 삼바 서버
윈도우는 삼바 클라이언트라는 개념이 없다. 그래서 리눅스가 윈도우인척 폴더를 공유해 놓으면 윈도우 클라이언트는 해당폴더가 윈도우가 공유했는지 리눅스가 공유한지 모르고 그냥 쓰게 된다.
#### 2.1 리눅스 서버를 삼바 서버로 만들자(서버에서 실행)
{% codeblock %}
# yum -y install samba
관련파일 카페에서 받자
# cd 다운로드
# yum -y localinstall system-config-samba*
여기서 에러날 경우 카페 자료실을 참고하자

# systemctl restart smb
# systemctl enable smb

# ls /share     / //share 폴더를 공유하자
# chmod 707 /share
# system-config-samba
설정- 서버설정 - workgroup/centOS 7 입력-
              보안탭가서 인증모드 공유로 하고 확인
workgroup은 윈도우의 그룹이다.

+를 누르고 폴더는 /share로 하고 쓰기가능,보이기 체크
접근탭을 눌러서 모든사용자에게 접근부여 선택

설정 - samba사용자 눌러서 사용자 추가.
유닉스 사용자명은 centOS / winuser /1234 /1234 입력 후 확인

# systemctl stop firewalld

# firewall-config //삼바, 삼바 클라이언트 열기
          //지금은 방화벽을 꺼둔상태라 하지 않아도 된다.

# systemctl restart smb
{% endcodeblock %}

#### 2.2 윈도우에서 접속해보자
컴퓨터 -> 네트워크 드라이브 연결
드라이브 Q로 선택  
폴더에  \\\\192.168.111.100\\share 입력
centos
1234

#### 2.3 서버에 접속한 사용자를 보자(서버에서 실행)
{% codeblock %}
# smbstatus
{% endcodeblock %}
