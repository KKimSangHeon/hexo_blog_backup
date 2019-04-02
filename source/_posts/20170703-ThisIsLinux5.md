---
title:  4장. 사용자 및 그룹 관리
date: 2017-07-03 11:29:09
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---

리눅스는 다중 사용자 시스템이다. 즉 서버 한대에 여러명이 접속한다. 그러므로 사용자를 관리하고 그룹을 관리하는게 중요한 이슈이다.
기본적으로 rootㄹ는 슈퍼유저가 관리하며 모든 사용자는 하나 이상의 그룹에 소속되어야 한다.
사용자는 /etc/passwd에 정의되어 있다.
아래의 명령문을 실행시켜 vi 에디터로 확인해보자.
{% codeblock %}
# vi /etc/passwd
{% endcodeblock %}
마지막 줄엔 다음과같은 코드가 존재한다.
{% codeblock %}
centos:x:1000:1000:centos:/home/centos:/bin/bash
{% endcodeblock %}
이에 대해 자세히 알아보자.

1.centos =
  사용자가 centos 이다.

2.x =
  비밀번호

3.1000 =
  centos라는 사용자의 ID

4.1000 =
  centos사용자는 반드시 소속된 그룹이 있어야하는데 이는 소속된 그룹의 ID 즉 그룹번호이다.
어떻게 그룹번호를 갖고 그룹명을 알까? /etc/group 파일에 정의되어 있다. 이 파일을 vi 에디터로 열면 그룹이름:그룹비밀번호:그룹의ID(그룹번호)의 형식을 갖는다.
centos:x:1000:centos  // 그룹이름:그룹비밀번호:그룹의ID(그룹번호)
이게 무슨뜻일까? centos라는 사용자가 centos그룹에 속해 있다는 것이다. 마지막 centos 는 생략이 가능하며 신경쓰지 않아도 된다.
예를 들면 홍길동이라는 부서에 홍길동 사원이 존재하는것이다. 회사에서는 부서를 만들고 사원을 뽑는다. 마찬가지로 centos도 새로운 사용자를 만들면 사용자 이름과 같은 그룹을 만들고 이 그룹에 해당시킨다.
이것과 관련해 실습을 진행해보자.
{% codeblock %}
# useradd myuser    //유저 등록
# tail -5 /etc/passwd    //해당파일의 마지막 5줄만 보기

myuser:x:1001:1001::/home/myuser:/bin/bash  //그룹번호 1001을 기억

# tail -5 /etc/group    //해당파일의 마지막 5줄만 보기

myuser:x:1001:  //사용자 이름과 같은 그룹이 만들어졌고 그 그룹에 사용자가 속해있다.
{% endcodeblock %}

5.centos =
  전체이름이다. 생략이 가능하다

6./home/centos centos =
  사용자의 홈 디렉토리

7./bin/bash =
  사용할 기본 쉘

#### 실습해보자
디렉토리 설명
/etc/passwd : 사용자 정보가 있음
/etc/shadow : 사용자 비밀번호가 있음(비밀번호는 암호화되어서 저장됨)
/etc/group  : 그룹정보가 있음

##### 실습 1
유저 생성, 유저 그룹지정, 그룹생성
{% codeblock %}
# useradd user1         //user1생성
# tail -5 /etc/passwd   //생성되었는지 확인
user1:x:1001:1001::/home/user1:/bin/bash  //잘생성됨

# tail -5 /etc/shadow     //사용자의 비밀번호를 확인해보자
user1:!!:17350:0:99999:7:::   //비밀번호를 설정하지 않았지만 암호화 되어있다.

# tail -5 /etc/group    //사용자의 그룹이 생성되었나 확인해보자
user1:x:1001:           //잘 생성되었다

하지만 바람직하지 않다.! user1의 그룹이 user1이라니 말이되나!

다시실습해보자 이번에는 그룹을 먼저 생성하고 사용자를 소속시키자

# userdel -r user1 //보통 현업에선 -r옵션을 넣어 디렉토리까진 지우지 않는다.
                  //하지만 실습이므로 과감하게 지운다.
# groupadd centosGroup    //그룹생성

# tail -5 /etc/group    //잘생성되었나 확인
centosGroup:x:1001:   //잘생성됨

# useradd -g centosGroup user1  //centosGroup에 소속된 user1생성
# useradd -g centosGroup user2  //centosGroup에 소속된 user2생성

# tail -5 /etc/passwd     //잘 생성되었나 확인.
user1:x:1001:1001::/home/user1:/bin/bash
user2:x:1002:1001::/home/user2:/bin/bash
  //잘 생성되었고 사용자 ID는 다르지만 같은 그룹ID를 갖는다.

# passwd user1   //암호지정. 이후 1234 1234입력
                //일반사용자는 암호를 8자 미만으로 지정못하지만
                //루트사용자는 8자 미만으로 지정해줄수 있다.

# passwd user2   //암호지정. 이후 1234 1234입력

# tail -5 /etc/shadow   //user1,user2의 암호를 보자
                      //user1,user2의 암호를 1234로 지정하였지만
                      //암호화된 값이 다르다!!
{% endcodeblock %}

##### 실습 2
x윈도에서 사용자권한관리 - x윈도가 없으면 안되므로 비추
{% codeblock %}
# yum -y install system-config-users  //system-config-users패키지 설치
# system-config-users   //x윈도우 상에서 사용자 관리가 가능하다!
{% endcodeblock %}









### 관련 명령어
189p 참고
{% codeblock %}
-u : ID 지정
-g : 그룹지정
-d : 홈디렉터리지정
-s : 쉘 지정

# useradd shkim //새로운 사용자를 추가

# passwd shkim //사용자의 비밀번호를 지정하거나 변경

# usermod -g root shkim  //사용자의 속성을 변경

# userdel shkim //사용자는 지우나 디렉토리를 제거하지 않음.
            //-r 옵션이 존재. 이는 사용자가 사용하던 디렉토리까지 제거

# change -m 2 shkim  //사용자의 암호를 주기적으로 변경하도록 설정
          //-m 2는 최소 이틀이상 써야한다.(189p 참고)

# groups // 사용자가 속한 그룹을 보여줌

# groupadd newgroup   //새로운 그룹을 새성          

# groupmod  -n newgroup mygroup//그룹의 속성을 변경 newgroup을 mygroup로 변경

# groupdel newgroup //그룹을 삭제

# gpasswd newgroup  //그룹의 암호설정
{% endcodeblock %}
