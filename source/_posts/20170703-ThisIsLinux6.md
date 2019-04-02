---
title:  4장. 파일의 소유권/허가권, 링크
date: 2017-07-03 13:33:03
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---



{% codeblock %}
# touch sample.txt  //빈 파일생성
# ls -l
-rw-r--r--  1 root   root         0  7월  3 13:35 sample.txt
{% endcodeblock %}

1.맨앞의 -는 파일의 유형을 의미.
  -: 일반파일
  d: 디렉토리
  l: 링크파일

2.rw-r--r--는 세개씩 끊어 읽으면 된다.
2-1.  맨앞 세개는 소유자의 파일접근권한 (rw-)
즉 소유자는 읽기, 쓰기 가능, 실행 불가

2-2.  두번째는 그룹의 파일접근권한
읽기만 가능

2-3.  세번째는 그 외의 사용자의 파일접근권한이다.
읽기만 가능
또한 rw-r--r--는 3자리씩 끊어서 보면 644로 볼수 있다.
rwxrwxrwx는? 666

3.1은 링크의 수

4.root는 파일 소유자 이름

5.root는 파일 소유 그룹이름

6.0은 파일의 크기

7.7월  3 13:35는 마지막 변경 날짜/시간

8.sample.txt는 파일이름

#### chmod 파일 허가권 변경
{% codeblock %}
# chmod 655 sample.txt
# ls -l sample.txt
-rw-r-xr-x 1 root root 0  7월  3 13:35 sample.txt

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

{% endcodeblock %}

#### chown 파일 소유자 변경
{% codeblock %}
# chown centos sample.txt   //centos로 소유권을 넘김
# ls -l sample.txt
-rw-r-xr-x 1 centos root 0  7월  3 13:35 sample.txt  //소유권 변경됨
{% endcodeblock %}

#### chgrp 파일 소유 그룹 변경
{% codeblock %}
# chgrp centos sample.txt
# ls -l sample.txt
-rw-r-xr-x 1 centos centos 0  7월  3 13:35 sample.txt
{% endcodeblock %}

#### chown 파일 소유자, 그룹 변경
{% codeblock %}
# chown root.root sample.txt
# ls -l sample.txt
-rw-r-xr-x 1 root root 0  7월  3 13:35 sample.txt
{% endcodeblock %}

#### ./에 대한 이해

파일을 실행하기 위해서는 아래의 명령어를 입력해야한다.
{% codeblock %}
# ./파일명
{% endcodeblock %}
./는 왜 붙는것일까?
그냥 파일명만 입력하면 찾지못하는데 그 이유는 현재 디렉토리가 경로에 없어서 그렇다.
그래서 ./을 넣어주는 것이다. 앞에서 배웠듯이 . 은 현재 디렉토리를 의미하는것을 떠올려보자.
즉 ./파일명 는 현재 디렉터리 아래에 있는 파일명을 실행하라는 것이다.

#### su 해당사용자로 로그인
{% codeblock %}
# su - centos   //centos계정으로 로그인
      //루트계정일 경우 암호를 묻지않는다.
      //-를 붙이면 디렉토리가 centos디렉토리로 변경되가
      //붙이지 않으면 변경되지 않는다.
{% endcodeblock %}

#### 실습해보자.

{% codeblock %}
# touch test    //test 파일 생성

# vi test     //이후 명령어(ex ls) 입력 그후 저장후 종료

# ./test      //test 파일 실행

실행되지 않음.   

# chmod 755 test    //실행권한을 모두 준다.

# ./test    //실행된다.

현재 파일의 소유권은 root이다.

# chown centos.centos test  //소유자,그룹을 변경

# su - centos   //센토스 사용자로 접근
              //이때 암호는 물어보지 않음. 루트에서 이동하는것이므로

$ ls -l /root/test    //허가 거부가 된다.
                    //왜냐하면 /root 디렉토리가 일반사용자는 들어올수 없게 되어있음.
                    //그러므로 파일을 이동시키거나, /root 디렉토리에 다른사용자가 들어
                    //올수 있게 허가해주어야 한다. 후자는 좋지 않은 방법이므로 전자를
                    //활용하자

# exit                //centos계정 로그아웃

# mv test ~centos     //centos 홈폴더로 test파일이동.

# su - centos   //다시 centos로 로그인

$ chmod 777 test

$ chown root.root test  //소유권을 다시 root로 변경
                      //불가능하다. 어딜감히 root로 변경해
                      //악성코드를 심고 할 수도 있고..위험함..
                      //그래서 이 명령어는 루트사용자만 가능!!!

$ chmod o-x  test      //일반사용자(o : ordinary)는 -x (x:execute) 실행 못하도록
$ ls -l
-rwxrwxrw- 1 centos centos 52  7월  3 13:52 test
                    //마지막에 -가 들어간것을 확인

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
{% endcodeblock %}

#### 링크
파일의 링크는 하드링크, 심볼릭 링크가 있다.
윈도우의 바로가기처럼 연결되어있는것은 심볼릭링크 소프트 링크라 한다.

원본파일이 inode블록(실제 파일정보를 갖고있는것)을 가르키고 inode블록은 원본파일 데이터를 가르킨다.

하드링크파일은 inode를 직접 가르키며
심볼링크파일은 새로운 inode를 만들고 새로운 inode는 원본파일 포인터를 하나 더만들고 그것을 가르킨다. 원본파일 포인터는 원본파일을 가르킨다. 교재 201p를 참고하자.

주로 하드링크보다는 심볼릭 링크를 생성한다.

##### 실습해보기

{% codeblock %}
# mkdir linktest
# cd linktest/
# vi basefile //아무거나 입력후 저장후 종료
//원본파일이 생겼으므로 inode 블록이 생성되었을것이다.
# ls -il basefile //inode 번호 확인
405679049 -rw-r--r-- 1 root root 34  7월  3 14:36 basefile
  //405679049이 inode 번호

# ln basefile hardlink  //하드링크 생성
# ln -s basefile softlink //소프트링크생성
# ls -il

405679049 -rw-r--r-- 2 root root 34  7월  3 14:36 basefile
405679049 -rw-r--r-- 2 root root 34  7월  3 14:36 hardlink
405679048 lrwxrwxrwx 1 root root  8  7월  3 14:40 softlink -> basefile

//basefile inode와 hardlink inode가 같다. softlink의 inode는 새로 만들어서 basefile을 가르킨다.

# mv basefile ../     //원본파일을 앞 디렉토리로 이동시킴
# ls -il

# cat hardlink  // 출력이 될까 안될까?
        //출력이 된다. 그 이유는 원본 inode를 가르키기 때문에!

# cat softlink  // 출력이 될까 안될까.?
        //안된다. 원본파일이 없어졌기 때문에.

# mv ../basefile    //다시 원상복구 시킴  (앞디렉토리로간 basefile 델꼬옴)

{% endcodeblock %}







{% codeblock %}


{% endcodeblock %}
