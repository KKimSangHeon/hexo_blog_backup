---
title:  4장. RPM 사용법, YUM 사용법
date: 2017-07-03 18:58:51
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### RPM
초창기 리눅스는 설치가 어려웠다 .소스코드를 갖고와서 적재적소에 컴파일을 해야했다. 그래서 일반사용자가 사용하기엔 거의 불가능했다. 이러한 이유로 외면을 받았었다. 결국 Redhat에서 RPM(Redhat Package Manager)을 내놓았다. 윈도우의 setup.exe 라고 생각하면 된다.

gedit-3.8.3-6.el7.x86_64.rpm

패키지이름 : gedit -> 패키지(프로그램)의 이름
버전 : 3.8.3 -> 대게 3자리수로 구성. 주버전, 부버전, 패치버전
릴리즈번호 : 6 -> 문제점을 개선할 때마다 붙여지는 번호   //noarch:모든CPU
CentOS버전 : el7 -> CentOS에서 배포할 경우에 붙여짐
아키텍처 : x86_64 -> 64비트 CPU를 의미

#### 자주쓰는 RPM 명령어 옵션
{% codeblock %}
$ rpm -Uvh 패키지파일이름.rpm
    // U -> (대문자)패키지가 설치/업그레이드
    // v -> 설치진행과정의 확인
    // h -> 설치진행과정을 "#"마크로 화면에 출력

$ rpm -e 패키지이름  //패키지 삭제

$ rpm -qa 패키지이름  //패키지가 설치되었는지 확인

$ rpm -qf 파일의 절대경로 // 파일이 어느 패키지에 포함된 것인지 확인

$ rpm -qlp 패키지파일이름.rpm //패키지 파일에 어떤 파일들이 포함되었는지 확인

$ rpm -qip 패키지파일이름.rpm //패키지 파일의 상세정보

{% endcodeblock %}

#### 실습
{% codeblock %}
$ rpm -qa mc  //mc 패키지가 설치되었나 확인

$ rpm -Uvh mc-4.8.7-8.el7.x86_64.rpm //설치

$ rpm -qa mc  //mc 패키지가 설치되었나 확인

$ rpm -qi mc  //mc 패키지 정보 출력

$ rpm -e mc-4.8.7-8.el7.x86_64.rpm  //삭제
오류: mc-4.8.7-8.el7.x86_64.rpm 패키지가 설치되어 있지 않습니다
//설치파일을 삭제하는것이다.!!!!

$ rpm -e mc  //패키지 이름만으로 삭제
{% endcodeblock %}

### YUM의 등장
Yellowdog Updater Modified

rpm의 경우 의존성 문제가 존재함. 이를 해결하기 위해 나온것이 YUM
의존성 문제란? A를 설치하기 위해 B가 먼저 설치되어있어야 하는것.
rpm은 B가 설치되어있지 않을 경우 A패키지 설치를 시도할 경우 B를 설치해야 한다고 알려주기는 하나 대략적으로 알려주고, B또한 다른 패키지에 의존성이 있을수도 있어 불편함을 유발한다.

yum은 인터넷이 정상적으로 동작해야 작동함. yum은 의존성이 있는것을 모두 알아서 설치해준다!!! 즉 yum 명령어는 내부적으로 rpm명령을 실행하는것!!

#### yum의 동작과정
1.yum install을 입력한다
2./etc/yum.repos.d/ 를 참고하여 URL을 확인하고
3.전체 패키지 목록 파일을 요청한다(CentOS 7 패키지 저장소로)
4.전체 패키지 목록 파일만 다운로드
5.설치할 패키지와 관련된 패키지의 이름을 화면에 출력
6.y를 입력하면 설치에 필요한 패키지 파일을 요청함
7.설치할 패키지 파일을 다운로드해서 자동 설치
{% codeblock %}
$ yum install 패키지이름   //패키지 설치

$ yum -y install 패키지이름   //패키지 설치(y안눌러도 됨)

$ yum remove 패키지이름  //패키지 제거

$ yum localinstall rpm파일이름.rpm  //로컬에 존재하는 rpm으로 설치
      // 만약 의존성이 있을경우 그 대상은 레파지토리에서 깐다.

$ yum info mc   //mc패키지에 관한 정보확인      

$ yum groupinstall "패키지 그룹이름" //예로 자바를 깐다면 jdk
                          //이클립스 등 한꺼번에 설치됭

$ yum clean all           //캐시를 지우는것.
                      //yum이 이상할때 써보자                          
{% endcodeblock %}


#### yum고급실습
네트워크를 거치지 않고 마운트 시킨 dvd에서 받는다. 교재 221참고
dvd에 없으면 네트워크에서 갖고온다.

#### yum grouplist
{% codeblock %}
$ yum grouplist   //그룹리스트 조회
$ yum grouplist hidden //숨겨진것까지 조회

//영어로 그룹을 설치하는것이 좋다.. 그러므로

$ chvt 2 //2번 가상터미널로 이동
$ root
 password

$ yum grouplist hidden > glist.txt  //glist.txt로 생성

$ chvt 1  //x 윈도모드로 이동
{% endcodeblock %}


#### 226p 비타민퀴즈
223p를 따라 설정 후
# yum groupinstall "GNOME Desktop" 입력
