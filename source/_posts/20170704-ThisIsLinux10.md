---
title: 4장. 파이프/필터, 프로세스, 서비스/소켓
date: 2017-07-04 14:52:52
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### 파이프
두개의 프로그램을 연결해주는 통로
{% codeblock %}
$ ls -l /etc | more   //앞에서 뒤로 넘김
{% endcodeblock %}

#### 필터
필요한것만 걸러주는 명령어

{% codeblock %}
$ ps -ef //현재 작동중인 프로세스 보여줌

$ ps -ef | grep bash    //bash와 관련된 프로세스만
                      //보여줌

$ rpm -qa | grep gedit  //gedit 관련된것만 보여줌
{% endcodeblock %}

#### 리다이렉션(보내는 것)
{% codeblock %}
$ ls -l > file.txt //결과를file.txt로 만드는것
              //file.txt가 존재할 경우 덮어쓰고
              //존재하지 않을경우 새로 만들고 작성

$ ls -l >> file.txt //결과를file.txt로 만드는것
                    //file.txt가 존재할 경우 뒤에다 이어서 작성하고
                    //존재하지 않을경우 새로만들고 작성

$ cat sort < file.txt > out.txt //file.txt를 정렬해서 out.txt에 넣어라
{% endcodeblock %}

#### 프로세스, 데몬
프로세스는 하드디스크에 저장된 실행코드가 메모리에 로딩되어 활성화 된것.

##### 포그라운드 프로세스
실행하면 화면에 나타나서 사용자와 상호작용을 하는 프로세스

##### 백그라운드 프로세스
화면에 나타나지 않고 실행되는 프로세스
백신 , 서버 데몬(=서비스,=서버 프로세스)등

#### 프로세스번호
각각의 프로세스는 할당된 번호가 있다.
{% codeblock %}
$ ps -ef | grep bash    //bash관련된 프로세스만 보여줌

root       693     1  0 13:43 ?        00:00:00 /bin/bash /usr/sbin/ksmtuned

693은 자기 프로세스 번호고 1은 부모프로세스 번호이다.
{% endcodeblock %}

#### 작업 번호
현재 실행되고 있는 백그라운드 프로세스의 순차번호

#### 부모프로세스 ,자식프로세스
모든 프로세스는 부모프로세스를 갖는다.
부모 프로세스를 kill하면 자식프로세스도 자동으로 죽음.

#### 프로세스 관련 명령

##### ps 프로세스 보기
{% codeblock %}
$ ps -ef | grep bash  //bash 관련 프로세스 보여줌
{% endcodeblock %}

##### kill 프로세스 종료
파이어폭스 kill하기
{% codeblock %}
$ ps -ef | grep fire  //파폭 관련 프로세스 보기
root      2637  1934  0 13:49 ?        00:00:04 /usr/lib64/firefox/firefox
//프로세스번호가 2637이다.
$ kill 2637
//가끔 킬 해도 죽지 않는 경우가 있는데 그경우 -9 옵션을 준다.
$ kill -9 2637
{% endcodeblock %}

##### pstree 프로세스 트리로 보기
{% codeblock %}
$pstree //프로세스 구조를 트리로 보여줌
{% endcodeblock %}

##### 실습 (포그라운드->백그라운드->포그라운드)

{% codeblock %}
$ yes > /dev/null   //y를 계속생성하는 프로세스 생성
ctrl+ z 입력.
$ bg //백그라운드 보는것.
    //처음에 했던 동작이 남아있다.
[1]+ yes > /dev/null &

$ fg 1    //1번작업을 다시 포그라운드로 갖고와라
{% endcodeblock %}


##### 실습 (포그라운드->백그라운드->포그라운드)

{% codeblock %}
$ gedit & //지에딧을 백그라운드로 돌려라.
        //콘솔을 계속 사용할 수 있게된다.

$ vi &  //백그라운드로 돌아가긴 하는데 의미없다.
        //vi는 포그라운드로 돌려야 하므로  
$ bg    //vi의 백그라운드 번호를 알고
$ fg 1   //vi를 포그라운드로 갖고옴
{% endcodeblock %}
#### 서비스와 소켓

##### 서비스
웹 서버 = 웹 서비스
FTP 서버 = FTP 서비스

서비스의 실행 스크립트 파일은 /usr/lib/systemd/system/ 디렉터리에 '서비스이름.service'라는 이름으로 확인할 수 있다. 예를들어 웹 서비스는 httpd.service라는 이름의 파일로 존재

##### 소켓
서비스는 항상 가동되지만 소켓은 필요할 떄만 구동된다. 외부에서 특정 서비스가 요청되었을 때. 그래서 약간 느리다.
/usr/lib/systemd/system/ 디렉터리에 소켓이름.socket 이름으로 존재
