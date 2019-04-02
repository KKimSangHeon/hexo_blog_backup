---
title: 4장. 리눅스 기본 명령어
date: 2017-07-02 15:25:50
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 기본명령어
당연히 알고있어야 하는것들이다! 자연스럽게 사용할줄 알아야함!

폴더 == 디렉토리
리눅스는 숨김파일일 경우 파일명 앞에 . 이 붙는다. (폴더도 마찬가지)

$ ls -l 명령어를 실행하였을 때
-로 시작하면 파일 d로 시작하면 디렉토리

#### ls 파일 목록보기

{% codeblock %}
$ ls    // 파일이나 폴더의 목록을 보는것 윈도우의 dir.

$ ls /etc/sysconfig   //특정 폴더내의 목록을 보는것

$ ls -a   //숨김파일까지 보는것.

$ ls -l  // 자세히 보는것
         //-로 시작하면 파일 d로 시작하면 디렉토리

$ ls *.cfg //확장자가 cfg인것

$ ls ls my* // my로 시작하는것들 조회

$ ls -al //숨김파일까지 자세히 조회
          // -a, -l 의 결합
{% endcodeblock %}

#### cd 디렉토리 이동
x윈도가 있으면 편하겠지만 없으면 이동이 명령어로만 가능하기 때문에 익숙해지자.

cd 명령어를 쓴 후에는 pwd 를 통해 현재 디렉토리를 보자. 리눅스의 디렉터리 구조를 이해 할 수 있게된다.

{% codeblock %}
$ cd /etc/sysconfig/
$ pwd     //현재 디렉터리 위치 출력

$ cd /    // 최상위폴더로 이동(루트 디렉토리라함)
          // $ cd / 명령어의 경우 다음 명령창이 아래와 같이 나온다.
          // [root@localhost /]#


$ cd      //  "/루트 디렉토리"(현재 사용자의 홈폴더)로 이동
          //  $ cd 명령어의 경우 다음 명령창이 아래와 같이 나온다.
          //  [root@localhost ~]



$ cd /etc       //루트 디렉토리의 etc로 이동(절대경로)
$ cd etc        //현재 디렉토리에서 etc로 이동 (상대경로)
$ cd ..         //현재 디렉토리의 바로 앞 디렉토리로 이동
$ cd .          //현재 디렉토리 반환

$ cd ~centos  //해당 사용자의 홈 디렉토리로 이동
              //즉 $ cd~사용자 == $ cd


{% endcodeblock %}

#### 여기서 주의할점!
우리가 서버에서 사용하는 계정은 root이라는 점을 잊지말자.
$ cd / 명령어를 실행하면 루트 디렉토리 즉 최상위 폴더로 이동하는데 여기서 ls를 입력하면 root 폴더가 하나 있다. 이 두가지를 혼동하는 경우가 많은데 주의 하도록 하자. 루트 디렉토리 그리고 루트 디렉토리에 존재하는 root 폴더는 다르며 루트 디렉토리안의 root 폴더는 root계정이기 때문에 존재하는것이다. 강의에서는 루트 디렉토리 그리고 /root 디렉토리 로 구분한다.

#### ..에 대한 이해
다음을 이해해보자
{% codeblock %}
[root@localhost /]# cd /etc/sysconfig/network-scripts/
[root@localhost network-scripts]# cd ../../../root
[root@localhost ~]# pwd
/root
{% endcodeblock %}

#### .에 대한 이해
다음을 이해해보자
이거를 어디다 쓸까 싶지만 현재 디렉토리의 어떤것을 실행하거나 어떤 것을 현재 디렉토리로 옮길 때 사용된다.
{% codeblock %}
[root@localhost ~]# cd .
[root@localhost ~]# pwd
/root
{% endcodeblock %}

#### touch 비어있는 파일 생성
{% codeblock %}
$ touch        //비어있는 파일 생성
{% endcodeblock %}


#### rm 파일 삭제
{% codeblock %}
$ rm file1    // 삭제명령어 (삭제할것인지 재확인함)

$ rm -f file1   // 삭제 명령어(삭제할것인지 확인하지 않고 삭제)
{% endcodeblock %}

#### cp 복사
{% codeblock %}
$ cp file3 file4  //file3가 file4로 복사됨

$ cp -r /etc/sysconfig .  //  /etc/sysconfig폴더를 현재 폴더에 복사한다. -r은 디렉토리 복사일 때 필요함
{% endcodeblock %}

#### mv 이동 및 파일명 변경
{% codeblock %}
$ mv myfile3.txt /root/sysconfig    //myfile3.txt파일을 /root/sysconfig 로 이동

$ mv file file3     //파일이름을 file에서 file3로 변경
{% endcodeblock %}

#### mkdir 디렉토리 생성

{% codeblock %}
$ mkdir folder1   //folder1 디렉토리 생성

$ mkdir -p dir1/dir2/dir3     // dir1/dir2/dir3 디렉토리를 생성
                      //  디렉토리가 3개가 만들어지는것이 아니라 dir1 안에 dir2 안에 dir3가 만들어진다.
{% endcodeblock %}

#### rmdir 디렉토리 제거
{% codeblock %}
$ rmdir folder1   //디렉토리 제거 (폴더가 비어있을 경우 가능)

$ rmdir -r folder1  //내부에 파일이 있는 디렉토리 제거
              //(안에 들어있는 파일 하나하나 지울것인지 다물어본다.)

$ rm -rf dir1   //묻지도 따지지도 않고 dir1 디렉토리 제거
                //매우 위험하다!! 주의해서 사용
{% endcodeblock %}

#### cat 파일 읽기
사실 vi를 써도 되지만 그게 귀찮을 떄 사용
{% codeblock %}
$ cat anaconda.ks.cfg
{% endcodeblock %}

#### head 파일 읽기
맨 위의 10줄만 보기
{% codeblock %}
$ head anaconda-ks.cfg
{% endcodeblock %}

#### tail 파일 읽기
맨 밑의 10줄만 보기
{% codeblock %}
$ tail anaconda-ks.cfg
{% endcodeblock %}

#### more,less 페이지 단위로 읽기
페이지 단위로 끊어 읽기 스페이스는 다음페이지 B는 이전페이지 종료하려면 Q
{% codeblock %}
$ more anaconda-ks.cfg

$ less anaconda-ks.cfg  //less more보다 몇가지 명령어가 더 존재함
{% endcodeblock %}

#### file 파일이 어떤 파일인지 확인
{% codeblock %}
$ file file1    //디렉터리인지, 링크인지, 비었는지 등을 알려준다.
{% endcodeblock %}

$ file /dev/cdrom 은 링크이다. 내 환경에서는 sr0을 가르키지만 다를 수 있다는 점을 알고있자.

#### clear
{% codeblock %}
$ clear     //화면을 깨끗하게 해준다.
{% endcodeblock %}
