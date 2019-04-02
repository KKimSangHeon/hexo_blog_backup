---
title: 4장. vi에디터, CD/DVD마운트
date: 2017-07-02 13:13:19
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---

### 에디터
gedit / vi 로 나뉜다. gedit는 윈도우의 메모장으로 생각하면 되며 단순하다. vi는 리눅스에서 자주 사용되므로 반드시 익혀야 한다.

#### gedit
{% codeblock %}
$ gedit //gedit 실행
        //윈도우 + 스페이스 : 한영전환

$ gedit 파일명 // 파일열기
{% endcodeblock %}


#### vi 에디터
x윈도우가 설치되지 않은곳에서도 사용 가능하기에 공통적으로 사용가능하다!
초기 실행시 vi에디터는 명령모드이다. 즉 입력이 불가능한 상태이다.

새파일 작성시  , 수정시
{% codeblock %}
$ vi 파일명 //vi 에디터가 실행된다.

i 또는 a 입력 //이후 편집, 입력작업진행

esc   //작업이 끝난 후 명령모드로 변경

:wq   //저장하고 종료한다.
:q!   //수정사항 무시하고 종료.
{% endcodeblock %}
----
코드의 행 번호를 확인하고 싶을경우
{% codeblock %}
$ :set number
{% endcodeblock %}

----
### swp 파일?
중간에 작업을 하다 터미널이 비정상적으로 종료될 경우 swp파일이 생성된다. vi를 실행하면 에러가 뜨는데 엔터를 누르면 작업이 가능하다.

이 파일을 확인하기 위해서는 다음명령어를 입력한다.
{% codeblock %}
$ ls -a //숨김파일까지 조회
{% endcodeblock %}

그 후 파일을 swp 파일을 지우려면 다음을 입력한다.
{% codeblock %}
$ rm .myfile3.txt.swp
{% endcodeblock %}


### 도움말기능
도움말 사용방법
{% codeblock %}
$ mas ls //ls에 대한 도움말을 확인 . 스페이스바로 페이지 이동가능
{% endcodeblock %}

### 마운트
물리적인 장치와 디렉터리를 연결시켜주는 과정
무슨소리지? 윈도우는 CD를 넣으면 D 드라이드 등으로 연결시켜 주지만 리눅스는 이과정이 없어서 특정 디렉토리로 연결시켜 주는것이다.

X윈도우에서 가능하며, 텍스트 모드에서 가능하다
1.
우선 vmware상으로 cd를 삽입해야 하므로 그 과정을 거친다. 좌측상단 Player을 누르고 Removable Decices -> CD/DVD -> Setting 을 들어간다.
혹은 우측상단 CD 모양 누르고 Setting

2.
CD/DVD 탭으로 가서 Connected 키고 Use ISO.... 에 iso 이미지 선택

3.
자동으로 인식이 되는데???? 리눅스는 자동으로 안된다며...
하지만 이게 되는 리눅스가 있고 안되는게 있다. 또한 텍스트 모드는 자동으로 인식이 되지 않으므로 수동으로 연습하는 것이 필요!

#### 마운트 사용법
사전지식 :
dvd장치의 경로 : /dev/cdrom
보통 /media/cdrom 디렉토리를 마운트 할 떄 많이씀

{% codeblock %}
$ umount /dev/cdrom   //마운트 된것을 끊는것. 연결이 되지 않았을 경우 오류메시지가 나온다. 하지만 상관없다.

$ ls /media   //마운트 대상 디렉터리를 생성하기 전에 존재유무 확인
$ mkdir /media/cdrom    //마운트 대상 디렉토리 생성

$ mount /dev/cdrom  /media/cdrom    //마운트!

$ cd /media/cdrom //디렉토리 이동

$ ls  //마운트가 잘 되었는지 확인

$ umount /dev/cdrom   //마운트 끊기 하지만 에러가 난다. 현재 cdrom 안에 존재하기 때문에.

$ cd      // 홈디렉토리로 이동

$ umount /dev/cdrom   //마운트 끊기 재시도

$ ls /media/cdrom   //정상적으로 끊어진것을 확인

{% endcodeblock %}

### ISO 파일 제작
p180 보고 진행하세요~ 앞으로 사용할 일은 없지만 가볍게 실습해보세요
ISO 파일을 생성하는 명령어는 genisoimage 이다. 또 iso 파일을 CD로 굽기 위한 명령어는 cdrecord이며 DVD로 굽는 명령어는 growisofs 이다.

먼저 아래의 명령어로 패키지가 설치되어있는지 확인해보자.
{% codeblock %}
$ rpm -qa 패키지이름
{% endcodeblock %}
설치되어있지 않다면
{% codeblock %}
$ yum -y install 패키지명 으로 설치를 한다.
{% endcodeblock %}

그 후 아래와 같이 입력하자. /boot디렉토리의 모든 파일을 test1.iso파일로 만드는 것이다.
-r -J 는 8글자 이상의 파일 이름 및 대소문자를 구분해서 인식하는 옵션이며 -o는 출력할 파일을 위한 옵션이다.
{% codeblock %}
$ genisoimage -r -J -o test1.iso /boot  
{% endcodeblock %}

테스트 해보기 위해 디렉토리를 만들고 마운트 시킨다.
{% codeblock %}
$ mkdir /media/iso
$ mount -o loop test1.iso /media/iso
$ ls -l /media/iso
$ ls -l /boot
{% endcodeblock %}

테스트를 했으면 마운트를 취소시켜준다
{% codeblock %}
$ umount /media/iso  
{% endcodeblock %}
