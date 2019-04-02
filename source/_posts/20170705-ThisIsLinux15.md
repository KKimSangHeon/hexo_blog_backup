---
title: 6장. IDE/SCSI 장치 구성, 하드디스크 1개 추가
date: 2017-07-05 16:33:36
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
하드디스크 관리는 관리자 입장에서 기본적인것이다.
디스크가 모자르면 디스크를 추가해야한다..

이런걸 어떻게 할지 알아보자

#### IDE SCSI 장치에 대해
IDE는 케이블을 2개 꽂을수가 있다. 여기에 하드디스크를 장착하거나 DVD롬을 장착할 수 있다. 즉 두개의 IDE가 있으면 4개를 장착할 수 있다. 0:0 , 0:1, 1:0 , 1:1 이렇게 구분된다(4개일 경우)

SCSI는 총 16개의 SCSI 하드디스크를 꽂을 수 있는 케이블이 따져있다. 0,7번은 VMware가 사용하므로 사용할 수 없다.
마찬가지로 SCSI 0:0 ,0:1 로 구분된다.

326p를 통해 그림으로 확인가능

우리가 80GB 의 하드디스크를 추가했는데 이를 sda라 그런다
만일 하나더 추가하면 sdb라 그런다.
정식이름은 /dev/sda 이다.
여기서 파티션을 나누면 sda1,sda2로 나뉜다. 즉 sda는 하드디스크 자체를 의미하며 sda1은 파티션1 sda2는 파티션2를 의미한다.

땅 자체를 사는것을 하드디스크를 추가하는것으로 보면되고 축구장으로 사용하기 위해 특정부분에 선을 긋는것을 파티션을 나눈다고 보면된다. 또한 파티션은 특정디렉터리에 마운트를 해줘야함.

#### 하드디스크 1개 장착과정
1.SCSI 0:1에 물리적인 하드디스크 하나 장착 이는 /dev/sdb이다.

2.fdisk 명령어를 통해 파티션을 나눔(실습에서는 파티션1개로)./dev/sdb1 파티션이 생긴다.

3.파티션을 사용하기 위해서는 포멧을 해줘야한다.(mkfs.ext4를 사용)

4.그러면 완전히 준비된 하드디스크가 생기는데 이를 특정 폴더에 마운트해야한다.

5.마운트는 컴퓨터를 끄면 마운트가 끊어지므로 /etc/fstab 파일을 편집해 주어야 한다.

----

##### 1.SCSI 0:1에 물리적인 하드디스크 하나 장착 이는 /dev/sdb이다.

에디트에서 애드를 선택하고 다음
스카시를 선택하고 다음
맨위를 하고 다음
1기가로 선택하고 싱글파일 선택하고 다음
어드밴스드를 눌러 0:1로 되어있는것을 확인

{% codeblock %}
# ls -l /dev/sd*
brw-rw---- 1 root disk 8,  0  7월  5 18:02 /dev/sda
brw-rw---- 1 root disk 8,  1  7월  5 18:02 /dev/sda1
brw-rw---- 1 root disk 8,  2  7월  5 18:02 /dev/sda2
brw-rw---- 1 root disk 8, 16  7월  5 18:02 /dev/sdb

sda는 초기 설정때 설정한것. 파티션을 두개로 나눠서 sda1, sda2로 나뉜다.

왜 sdb1은 없지? 파티션을 나누지 않았으므로!
{% endcodeblock %}

##### 2.fdisk 명령어를 통해 파티션을 나눔(실습에서는 파티션1개로)./dev/sdb1 파티션이 생긴다.
파티션을 나눠보자
{% codeblock %}
# fdisk /dev/sdb
n       //새로운 파티션 추가
p    // p(primary)는 주 파티션 e(extended)는 확장파티션을 의미
      //p는 4개까지 나눌 수 있음
      //4개 이상하고싶으면 3개는 p로하고 하나는 e로 잡아서
      //e 파티션안을 나눠서 늘리면 된다. 이는 굉장히 드문경우
      //일반적으로는 p를 쓰면 충분하다.
1    //sdb1로 하겠다.      
엔터       // 2047까지는 시스템 부분이므로
          // 어디서 부터 시작할꺼냐. 엔터하면 2048부터
엔터       //끝까지 쓰겠다.   
p     //최종확인
w     //적용      

# ls -l /dev/sd*      //생성된 것 확인
brw-rw---- 1 root disk 8,  0  7월  5 18:02 /dev/sda
brw-rw---- 1 root disk 8,  1  7월  5 18:02 /dev/sda1
brw-rw---- 1 root disk 8,  2  7월  5 18:02 /dev/sda2
brw-rw---- 1 root disk 8, 16  7월  5 18:11 /dev/sdb
brw-rw---- 1 root disk 8, 17  7월  5 18:11 /dev/sdb1
{% endcodeblock %}
파일 시스템을 생성하자.

##### 3.파티션을 사용하기 위해서는 포멧을 해줘야한다.(mkfs.ext4를 사용)
{% codeblock %}
# mkfs.ext4 /dev/sdb1
{% endcodeblock %}


##### 4.그러면 완전히 준비된 하드디스크가 생기는데 이를 특정 폴더에 마운트해야한다.
{% codeblock %}
# mkdir /mydata
# mount /dev/sdb1 /mydata

# df // 마운트 된것을 확인

# cp /boot/vmlinuz-3.10.0-123.el7.x86_64 /mydata/data1  // 새로운 디스크에 데이터 복사

# df //확인 (용량 늘어난것 확인)
{% endcodeblock %}

##### 5.마운트는 컴퓨터를 끄면 마운트가 끊어지므로 /etc/fstab 파일을 편집해 주어야 한다.
{% codeblock %}
# vi /etc/fstab   
/dev/sdb1   /mydata   ext4    defaults     0 0 입력 후 vi종료
                    //dev/sdb1을 /mydata에 마운트하는것. ext4를 이용해
{% endcodeblock %}







{% codeblock %}

{% endcodeblock %}
