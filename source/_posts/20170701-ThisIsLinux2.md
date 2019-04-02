---
title: 4장. 시작과종료, 가상콘솔, 런레벨, 자동완성
date: 2017-07-01 22:53:50
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 참고사항
lrwxrwxrwx. 어쩌구가 있을경우 맨앞의 l은 링크파일임을 의미한다. 윈도우의 바로가기를 생각하자!

### 종료관련 명령어
리눅스는 기본개념이 멀티유저 이므로 함부러 누군가 종료하거나 재시작하면 문제의 소지가 있다. ex) 네이버 서버를 누군가 종료시킨다면?

리눅스를 서버로 사용할 경우 리눅스는 종료가 아닌 로그아웃이 일반적인 개념이다!

{% codeblock %}
$ shutdown -P now // 종료

$ shutdown -P +10 //10분후에 종료

$ shutdown -c //종료예약한것 취소

$ shutdown -r 23:00 //23시에 재부팅

$ shutdown -r +10 //10분후에 재부팅

$ shutdown -c // 리부팅예약한것 취소

$ shutdown -k +15  //20분 후에 종료된다는 메세지가 날라감
                  //루트를 제외한 모든 사용자에게..
                  //실제 종료는 안됨.
                  //필요한 이유: 서버에 많은 사용자가 접속하였을 경우 사용자들의 로그아웃을 유도.

$ init 0~6        // 0-종료모드 2-멀티유저모드 5-그래픽모드의 다중사용자모드 6-리부트모드

$ startx      //x윈도우 모드로 변경
              주의) 예로 단순히 init4에서 init1로가는것과 다르다.
              //명령어 모드에서 x윈도우 모드로 변경되는것
{% endcodeblock %}


### 가상콘솔 명령어
CentOS는 6개의 가상콘솔을 제공하는데 ctrl+alt+F2~F6으로 이동이 가능하다. F1은 X윈도우 모드이다.

{% codeblock %}
$ chvt 1~6 입력 //가상콘솔 이동
{% endcodeblock %}

### 자동완성
일부만 입력하고 탭을 누르면 자동완성된다.
{% codeblock %}
$ /abcdef
를 입력하고자 할 경우

$ /abc
만 입력하고 탭키를 누르면
$ /abcdef 가 입력된다.

먄약 abcde, abcdef 라는 디렉토리가 존재할 경우에는
$ /abc
입력 후 탭키를 누르면 자동완성이 되지않는다. 이 경우 탭키를 두번누르면 abcde, abcdef 라는 디렉토리가 있다는 사실을 알려준다.
{% endcodeblock %}


### 히스토리
{% codeblock %}
$ history //자신이 입력했던 명령어들이 출력된다.

$ history //히스토리 리스트를 제거한다.
{% endcodeblock %}
