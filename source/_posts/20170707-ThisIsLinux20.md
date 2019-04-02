---
title: 6장 사용자별 공간 할당(쿼터)
date: 2017-07-07 23:55:51
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### 쿼터
사용자마다 용량 제한을 두는것이다.
ex.클라우드 서비스를 가입하면 데이터 양이 한정되어있음.

#### 쿼터 실습
설치 직후로 초기화하자.

새로운 디스크를 추가해서 쿼터 전용 디스크로 하자
사용자의 홈 디렉토리는 별도의 디스크로 하는것이 효과적이다. 그리고 사용자별 쿼터도 지정해보자

##### 1. 20기가짜리 하나 디스크 달자
{% codeblock %}
# fdisk /dev/sdb
# mkfs.ext4 /dev/sdb1
# mkdir /userHome     //앞으로 추가되는 사용자는
              userHome를 홈 디렉토리로 함
# mount /dev/sdb1  /userHome/
# vi /etc/fstab
/dev/sdb1     /userHome   ext4    defaults   1   2  추가
{% endcodeblock %}
##### 2. 유저 두명 추가하자
{% codeblock %}
# useradd -d /userHome/john  john   
          //홈디렉토리를 지정하면서 유저생성
# useradd -d /userHome/bann  bann   
                    //홈디렉토리를 지정하면서 유저생성   

# ls -l /userHome     //확인

# passwd john   //암호는 1234로 설정

# passwd bann   //암호는 1234로 설정
{% endcodeblock %}
##### 3. 아까 추가한 디스크를 쿼터용으로 설정하자
{% codeblock %}
# vi /etc/fstab
defaults 를 아래와 같이 변경
defaults,usrjquota=aquota.user,jqfmt=vfsv0

리부트 해도 되지만 귀찮으니까
# mount --options remount  /userHome
# mount  //맨아래서 확인
{% endcodeblock %}

##### 4. 쿼터디비생성
명령어들에 대해 관심있으면 책을보자
{% codeblock %}
# cd /userHome/
# quotaoff -avug
# quotacheck -augmn
# rm -rf aquota.*
# quotacheck -augmn
# touch aquota.user aquota.group
# chmod 600 aquota.*
# quotacheck -augmn
# quotaon -avug

# ls -l   //aquota.group , user 생긴것 확인
{% endcodeblock %}
##### 5. 개인별 쿼터 설정

블록(block)이 현재 사용하는 키로바이트 수

소프트제한(soft)- 넘어갈수 있지만 기간이 있음
하드제한(hard) -완전히 못넘어감

소프트를 100으로 주고 하드를 150을 주었을 때
130을 저장하면 몇일 이내에 30메가를 줄여야함.

inodes- 파일의 개수를 제한하는것
{% codeblock %}
# edquota -u john //유저 존에 대해 쿼터를 수정하겠다.

소프트 10240(10메가) 하드 15360(15메가)를 주고 저장

# su - john  //존으로 로그인
# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 test1
      // 파일 복사
# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 test2
# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 test3
쿼터가 넘어갔다고 경고 뜸
# ls -l     //데이터가 잘 들어갔다.

# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 test4
sdb1: write failed, user block limit reached.
cp: `test4'에 쓰는 도중 오류 발생: 디스크 할당량이 초과됨
cp: failed to extend `test4': 디스크 할당량이 초과됨
 오류뜬다.

# ls -l
//마지막 데이터는 용량이 다름 즉 짤려있음

# quota  // 내가 얼마나쓰고 있는지 확인
block 현재 사용량
grace가 6일동안 봐준다는것.
{% endcodeblock %}

##### 6. 존이 한것을 밴한테 그대로 적용하기
{% codeblock %}
# exit    //존 로그아웃
# repquota /userHome    //쿼터확인

# edquota -p john bann
{% endcodeblock %}
