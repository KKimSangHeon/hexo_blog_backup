---
title: 리눅스 명령어
date: 2017-07-01 13:57:06
categories:
- OS
- Linux
tags:
- 리눅스 명령어
thumbnail: /images/centoslogo.png
---
### 참고사항

 $: 일반사용자     
 #: 루트사용자

----
### 명령어
{% codeblock %}

$ halt -p 		// 종료

$ gedit 파일명 		//편집기 열기

$ ifconfig 		//윈도우 ipconfig와 동일

$ reboot			//리부트 명령어

$ su - 			//루트사용자의 권한을 얻음(폴더가 이동됨)

$ su  		//루트사용자의 권한을 얻음(폴더 이동되지 않고)

$ setfont sun12x22	//글자크기 변경

$ vi 파일명		//vi 에디터 열기

$ ip addr		 //ip 확인

$ clear           //콘솔창 초기화

$ yum 프로그램    //프로그램 설치

$ cd  //홈디렉터리로 이동

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



$ chvt 1~6 입력 //가상콘솔 이동

$ gedit //gedit 실행
        //윈도우 + 스페이스 : 한영전환

$ gedit 파일명 // 파일열기

$ vi 파일명 //vi 에디터가 실행된다.

i 또는 a 입력 //이후 편집, 입력작업진행

esc   //작업이 끝난 후 명령모드로 변경

:wq   //저장하고 종료한다.
:q!   //수정사항 무시하고 종료

$ :set number //코드의 행번호 확인

$ ls -a //숨김파일까지 조회

$ rm .myfile3.txt.swp //파일삭제

$ mas ls //ls에 대한 도움말을 확인 . 스페이스바로 페이지 이동가능

$ umount /dev/cdrom   //마운트 된것을 끊는것. 연결이 되지 않았을 경우 오류메시지가 나온다.

$ mount /dev/cdrom  /media/cdrom    //마운트!

$ rpm -qa 패키지이름 //패키지 설치여부 확인

$ yum -y install 패키지명 으로 설치를 한다.

$ ls    // 파일이나 폴더의 목록을 보는것 윈도우의 dir.

$ ls /etc/sysconfig   //특정 폴더내의 목록을 보는것

$ ls -a   //숨김파일까지 보는것.

$ ls -l  // 자세히 보는것
         //-로 시작하면 파일 d로 시작하면 디렉토리

$ ls *.cfg //확장자가 cfg인것

$ ls ls my* // my로 시작하는것들 조회

$ ls -al //숨김파일까지 자세히 조회
          // -a, -l 의 결합

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

$ touch        //비어있는 파일 생성

$ rm file1    // 삭제명령어 (삭제할것인지 재확인함)

$ rm -f file1   // 삭제 명령어(삭제할것인지 확인하지 않고 삭제)

$ cp file3 file4  //file3가 file4로 복사됨

$ cp -r /etc/sysconfig .  //  /etc/sysconfig폴더를 현재 폴더에 복사한다. -r은 디렉토리 복사일

$ mv myfile3.txt /root/sysconfig    //myfile3.txt파일을 /root/sysconfig 로 이동

$ mv file file3     //파일이름을 file에서 file3로 변경

$ mkdir folder1   //folder1 디렉토리 생성

$ mkdir -p dir1/dir2/dir3     // dir1/dir2/dir3 디렉토리를 생성
                      //  디렉토리가 3개가 만들어지는것이 아니라 dir1 안에 dir2 안에 dir3가 만들어진다.

$ rmdir folder1   //디렉토리 제거 (폴더가 비어있을 경우 가능)

$ rmdir -r folder1  //내부에 파일이 있는 디렉토리 제거
              //(안에 들어있는 파일 하나하나 지울것인지 다물어본다.)

$ rm -rf dir1   //묻지도 따지지도 않고 dir1 디렉토리 제거
                //매우 위험하다!! 주의해서 사용

$ cat anaconda.ks.cfg   // 파일을 읽기

$ head anaconda-ks.cfg  //위에 10줄만 읽기
$ head -5 anaconda-ks.cfg  //위에 5줄만 읽기

$ tail anaconda-ks.cfg  //아래 10줄만 읽기

$ more anaconda-ks.cfg  //페이지 단위로 끊어읽기

$ less anaconda-ks.cfg  //페이지 단위로 끊어읽기 less가 more보다 몇가지 명령어가 더 존재함

$ file file1    //디렉터리인지, 링크인지, 비었는지 등을 알려준다.

# userdel -r user1 //보통 현업에선 -r옵션을 넣어 디렉토리까진 지우지 않는다.
                  //하지만 실습이므로 과감하게 지운다.
# groupadd centosGroup    //그룹생성

# useradd user1         //user1생성

# useradd -g centosGroup user1  //centosGroup에 소속된 user1생성
# useradd -g centosGroup user2  //centosGroup에 소속된 user2생성

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

$ chmod o-x  test      //일반사용자(o : ordinary)는 -x (x:execute) 실행 못하도록
$ ls -l test
-rwxrwxrw- 1 centos centos 52  7월  3 13:52 test
        //마지막 x가 빠짐

$ chmod ug-w test    //소유주(u: user), 소유그룹(g: group) 쓰기권한 제거
$ ls -l test
-r-xr-xrw- 1 centos centos 52  7월  3 13:52 test
        //3번째 6번째 w가 빠짐

$ chmod ugo-r test  //전부다 읽기권한 제거
$ ls -l test   
---x--x-w- 1 centos centos 52  7월  3 13:52 test
        //r이 제거된것 확인

$ chmod uo+rw test  //소유주, 기타사용자에게 읽기,쓰기 허용
$ ls -l test  
-rwx--xrw- 1 centos centos 52  7월  3 13:52 test

//숫자를 넣지 않고 하는것을 심볼릭방법이라 하는데 이것이 더 편할 때가 있다.
숫자로 할 경우 기존의 권한도 알아야 하기 때문에..

# chown centos sample.txt   //centos로 소유권을 넘김
# ls -l sample.txt
-rw-r-xr-x 1 centos root 0  7월  3 13:35 sample.txt  //소유권 변경됨

# chgrp centos sample.txt   //파일소유그룹 변경
# ls -l sample.txt
-rw-r-xr-x 1 centos centos 0  7월  3 13:35 sample.txt

# chown root.root sample.txt  //파일 소유자, 그룹 변경
# ls -l sample.txt
-rw-r-xr-x 1 root root 0  7월  3 13:35 sample.txt

# su - centos   //centos계정으로 로그인
      //루트계정일 경우 암호를 묻지않는다.
      //-를 붙이면 디렉토리가 centos디렉토리로 변경되가
      //붙이지 않으면 변경되지 않는다.

# ps -ef | grep dnsmasq   //dnsmasq 프로세스 번호 확인
# kill -9 xxxx    //xxxx프로세스 kill

{% endcodeblock %}
