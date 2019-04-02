---
title: 12장. 웹하드 설치와 운영
date: 2017-07-14 14:26:47
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
웹하드는 웹서버상에서 파일을 저장할 수 있는것. 웹하드에 파일을 저장해 놓으면 전세계 어디서든지 내려받고 수정할 수 있다.

#### 설정하자
{% codeblock %}
# cd /var/www/html
# ls -l
# mv /root/다운로드/pydio-core-6.0.2.tar.gz / .
# tar xfz pydio-core-6.0.2.tar.gz
# mv pydio-core-6.0.2 webhard //폴더명 변경

# chmod 707 webhard/    //일반사용자가 접근할 수 있게 권한변경
# chown -R apache.apache webhard/
# ls -l

# yum -y --skip-broken install php-*   
      //php관련 패키지 설치
      //--skip-broken 설치하다 에러나면 그냥 무시해라

# yum -y install php-mcrypt  //레드헷 엔터프라이즈에 존재
                            //지금은 설치 안될것이다.
# yum -y install epel-release
//레드헷 엔터프라이즈도 yum으로 깔 수 있게됨    
# yum -y install php-mcrypt //이젠 설치가 된다.

# vi /etc/httpd/conf/httpd.conf
151 행 AllowOverride All로 변경
# systemctl restart httpd
{% endcodeblock %}

#### 리눅스 클라이언트에서 접속해보자
http://192.168.111.100/webhard/  접속
click here 클릭
한국어 하고 start wizard
admin admin 12345678 12345678

global option 클릭
default language만 한국어로 변경

configurations storage 클릭하고
database system 선택
database에 xeDB 입력
User에 xeUser 입력
Password에 1234 입력
Try connecting.... 클릭

add some users 클릭
centos
centos@hanbit.co.kr
센토스사용자
1234
1234
Install pydi 클릭
웹브라우져 껏다 켜서
http://192.168.111.100/webhard/ 접속

centos
1234 후 로그인
업로드를 해보자!

#### 업로드 용량 제한을 풀자
서버에서 실행하자
{% codeblock %}

vi /etc/php.ini //현재는 8m이상 업로드가 안되는데 제한을 풀자.
:384  (384라인으로감!)
30초가 넘어가면 종료되도록 설정되어있다 300초로 수정하자

:672
8M를 100M으로 수정

:800
2M 를 100M으로 수정
:wq

# cd /var/www/html/webhard/data/cache/  
    //캐쉬로 인해 제대로 작동하지 않을 수 있으므로 캐쉬를 지우자
    //지금 캐쉬폴더가 없는데 실행을 안해서 그런가..?
# rm -f plugin*
# systemctl restart httpd

{% endcodeblock %}

#### 업로드를 다시 해보자
리눅스 클라이언트에서..


{% codeblock %}

{% endcodeblock %}
