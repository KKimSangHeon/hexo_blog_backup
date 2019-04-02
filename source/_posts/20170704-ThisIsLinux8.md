---
title: 4장. 파일 압축과 묶기, CRON과 AT
date: 2017-07-04 09:26:28
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
### 파일 압축

유닉스는 압축하는 파일이 여러개이다. 그래서 확장자도 여러개이다. 점점 개선이 되면서 여러개가 생긴것이다. 예전것도 쓰고 최신것도 쓰므로 모두 사용할줄 알아야함
gzip->bzip2->xz 순으로 발전해옴 xz가 압축률도 제일좋다.

{% codeblock %}
# cp glist.txt file1.txt    //파일 복사
# cp glist.txt file2.txt
# cp glist.txt file3.txt
# xz file1.txt
# bzip2 file2.txt
# gzip file3.txt

# ls-l file*
-rw-r--r-- 1 root root 1080  7월  4 09:31 file1.txt.xz
-rw-r--r-- 1 root root 1072  7월  4 09:31 file2.txt.bz2
-rw-r--r-- 1 root root 1053  7월  4 09:31 file3.txt.gz

# xz -d file.txt.xz   //압축해제

# bzip2 -d file2.txt.bz2 //압축해제

# gzip -d file3.txt.gz  //압축해제
# gunzip file3.txt.gz //위와 같음

{% endcodeblock %}
gz로 압축한게 제일작은데? 파일마다 다르다!! 일반적으로는 gz가 효울이 제일 떨어진다. 그래서 일반적으로 커널파일을 xz로 배포한다.
리눅스에서의 압축은 파일을 압축해서 원본파일이 사라진다는것을 주의하자.

#### 파일 묶기
리눅스는 압축, 묶기가 다르다
묶기는 원본파일이 유지되며 하나로 묶은것이라 보면된다.
{% codeblock %}
# tar cvf bin.tar bin1 bin2 bin3    //bin1,2,3이 bin.tar로 묶임
# rm -f bin?    //bin1,2,3 제거

# tar xvf bin.tar //풀기
# ls -l
-rw-r--r--  1 root root 14714880  7월  4 09:41 bin.tar
-rwxr-xr-x  1 root root  4902656  7월  4 09:40 bin1
-rwxr-xr-x  1 root root  4902656  7월  4 09:40 bin2
-rwxr-xr-x  1 root root  4902656  7월  4 09:40 bin3

{% endcodeblock %}
묶으면서 압축을 진행해보자
{% codeblock %}
# rm -f bin.tar
# tar cvfj bin.tar.bz2 bin1 bin2 bin3  //bz2형식으로 압축하면서 묶어줌


# rm -f bin.tar.bz2
# tar cvfJ bin.tar.xz bin1 bin2 bin3  //xz형식으로 압축하면서 묶어줌
                                      //J가 대문자이다.
# tar xvfJ bin.tar.xz /etc/sysconfig  //xz압축해제 +tar 풀기
{% endcodeblock %}

#### 파일 위치 검색
{% codeblock %}
# find /etc  -name "*.conf"    //etc밑에 확장명이 conf 인것을 찾음
# find /etc  -name "*.conf" > conf.txt  //위 대상으로 conf.txt로 저장

# find /home -user centos   //centos 사용자의 파일들 검색

# find /usr/bin -size +95k -size -100k    //사이즈가 95kb 이상
                                        //100kb 이하인것 검색
# find /usr/bin -size +95k -size -100k
          -exec ls -l {} \;  // 윗줄의 결과가 중괄호 속에 들어가서
                            // ls -l 명령어 실행
{% endcodeblock %}

#### 시스템 설정
{% codeblock %}
# yum -y install system-config-date
# system-config-date  //날짜 설정 가능

# nmtui   //네트워크 설정

# firewall-config
{% endcodeblock %}

#### CRON과 AT
CRON : 주기적으로 반복되는 일을 자동적으로 실행될 수 있도록 실행

42 4 1 \*\* root run-parts /etc/cron.hourly
42 : 42분
4 : 4시
1 : 1일
\* : 매 월
\* : 매 요일

01 \* \* \* \* root run-parts  /etc/cron.daily
맨 뒤는 요일이므로 제외하고
매월
매일
매시
1분마다.

02 4 \* \* \* root run-parts  /etc/cron.weekly
매일 4시 2분마다 실행하라

03 4 \* \* 0 root run-parts  /etc/cron.monthly
일요일 4시 3분마다 실행해라

----

AT : 한번 실행되는것
\# at 3:00am tomorrow ->내일 새벽 3시
\# at 11:00pm January 30 ->1월 30일 오후 11시
\# at now + 1 hours ->한시간 후
그 후 예약 명령어 입력후 엔터
완료되면 ctrl + d

쉘 스크립트 : 윈도우의 배치파일처럼 실행할 명령어가 쭉 나열되어있는것


#### CRON 실습
{% codeblock %}
# systemctl status crond  //크론이 돌고있는지 확인

# vi /etc/crontab
01 3 15 * * root run-parts /etc/cron.monthly
 //한줄 추가 매달 매 요일 15일마다 3시 1분에

# cd /etc/cron.monthly/

# vi myBackup.sh

다음을 입력
#!/bin/sh   // 써줘야함

set $(date)   //날짜를 읽어옴
fname="backup-$2$3.tar.xz"    //$2는 월 $3은 일

tar cfJ /backup/$fname  /home   // /home폴더를 묶음

vi 종료

# chmod ugo+x myBackup.sh   //쉘스크립트 파일 실행권한부여

# mkdir /backup   //백업파일 보관할 장소 생성

# systemctl restart crond //예약하는 작업이 재시작된다.

# date 011503002019       //테스트를 위해 시간을 변경
    //월일시분년
2019. 01. 15. (화) 03:00:00 KST 로 변경됨

# systemctl restart crond  // 시간적용을 위해 재시작

1분이 지나고

# ls-l /backup/

{% endcodeblock %}

#### AT 실습

{% codeblock %}
# rdate -s time.bora.net  //서버에 가서 정확한 시간을 세팅함
# at 4:00 am tomorrow   //내일 새벽 4시에 할 명령어 입력

at> yum -y update
at> reboot

입력 후 ctrl + d

# at -l 예약된것 확인
1	Wed Jul  5 04:00:00 2017 a root

# atrm 1  //1번 목록 제거

{% endcodeblock %}











{% codeblock %}

{% endcodeblock %}
