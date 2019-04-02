---
title: 7장. 셸 스크립트 프로그래밍
date: 2017-07-08 10:54:28
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
Centos의 기본 셸은 bash
셸은 명령어를 해석 해 주는 것이다.
 -Alias, History, 연산 등의 기능을 갖는다.
#### 환경변수
{% codeblock %}
# echo $PATH //명령어를 실행하면
          //명령어를 찾는 폴더들이 출력됨.
{% endcodeblock %}
#### 셸 스크립트 프로그래밍
C언어와 유사하다.
변수, 반복문,제어문 등의 사용이 가능
vi, gedit으로 작성 가능

{% codeblock %}
# vi name.sh  //셸의 약자로 sh
다음과 같이 입력

#!/bin/sh     //셸은 이것을 무조건 쓴다.
echo "사용자이름: "$USERNAME
exit 0

vi를 빠져나와서

# sh name.sh  //실행 방법 1

# chmod u+x name.sh   //실행방법2
# ./name.sh       //실행방법2
{% endcodeblock %}

##### 변수의 기본
변수를 사용하기 전에 미리 선언하지 않으며, 변수에 처음 값이 할당되면서 자동으로 변수가 생성
모든 변수는 '문자열(String)'로 취급
변수 이름은 대소문자를 구분
변수를 대입할 때 '=' 좌우에는 공백이 없어야함

{% codeblock %}
# testval = Hello //에러발생. 공백존재

# testval=Hello
# echo $testval   //Hello 출력됨

# testval=Yes Sir   //에러발생

# testval="Yes Sir"
# echo $testval   //Yes Sir 출력

# testval=7+5
# echo testval    //7+5 출력

# testval='expr 7+5'
# echo testval    //12 출력

{% endcodeblock %}
