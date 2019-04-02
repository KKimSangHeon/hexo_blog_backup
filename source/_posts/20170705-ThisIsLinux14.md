---
title: 5장. 리눅스에 Windows 설치 KDE데스크톱 설치
date: 2017-07-05 15:37:05
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### 리눅스 안에서 윈도우를 깔아보자
CentOS에서 제공하는 가상머신을 이용하자
즉 가상머신에서 가상머신을 만드는...
램 올리고, 가상화 켜주고 311p참고

iso 파일을 받고 내폴더에 붙여넣기 해준다.

{% codeblock %}
# mkdir /iso
# mv 600* /iso    //파일이름이 600으로 시작함.
# ls -l /iso
{% endcodeblock %}
실습에서는 313p 의 2-0을 생략한다.
프로그램 -시스템 도구 -가상머진 관리자
로컬 선택 후 새가상머신 생성 왼쪽위 그림(노란색)
Win2008 입력후 로컬설치매체 선택후 앞으로
iso파일 선택하고 윈도우서버 2008 선택하고 앞으로
그대로 두고 앞으로
설치전에 사용자 설정 선택 후 완료
디스플레이를 VNC로 하고 적용
왼쪽 위 설치시작

#### KDE 데스크톱을 설치해보자
센토스는 기본적으로 그놈데스크탑이 들어있다. 윈도우와 비슷한 KDE 데스크탑을 운영할 수 있다.
{% codeblock %}
$ yum grouplist | grep KDE  //KDE 패키지그룹을 조회
su -c 'yum -y groupinstall "KDE Plasma Workspaces"'
          //조회됨

$ su -c 'yum -y groupinstall "KDE Plasma Workspaces"'
        //루트권한으로 ''안에 있는 명령어 실행(KDE 설치)
{% endcodeblock %}
자동로그인 해제 . 321p 참고
{% codeblock %}
$ reboot
{% endcodeblock %}
톱니 누르고 KDE Plasma 작업공간 선택후 암호 입력 후 로그인
