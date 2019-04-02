---
title: 6장. RAID 정의 및 개념,구현
date: 2017-07-06 09:13:09
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### RAID
Redundant Array of Inexpensive Disks
여러개의 디스크를 하나처럼 사용하는 RAID를 알아보자
비용절감 + 신뢰성 향상 + 성능 향상
1테라 디스크 3개를 하나로 묶어서 3테라 디스크처럼 쓴다


##### 하드웨어 RAID
여러개의 하드디스크를 가지고 장비를 만들어서 사용. 장비업체에서 제공하기 떄문에 사용법이 다를 수 있다. 그리고 상당히 고가이다. 하드웨어RAID가 안정성이 높다.

##### 소프트웨어 RAID
디스크를 장착만하면 운영체제 레벨에서 하나로 묶을 수 있다.

##### Linear RAID (339p 그림보기)
최소 2개이상의 디스크를 하나로 묶음
앞 디스크 부터 차례로 저장
100TB 100TB 100TB 를 사용하면 총 300TB가 된다. 즉 공간효율성이 100%임.

##### RAID 0 (339p 그림보기)
모든 디스크에 동시에 저장되는 개념
그러므로 하드디스크가 3개면 /3 시간으로 저장됨
신뢰성이 낮다.
빠르지만 전부 잃어버려도 큰 문제가 되지 않는 자료가 적당하다.
3TB 100TB 100TB 를 사용하면 총 9TB가 된다. 즉 디스크 크기가 다르면 공간효율성이 100%가 되지 않음.
그래서 RAID를 구성할 떈 같은회사 같은 용량의 디스크로 구성한다.
디스크 세개중에 하나만 고장나도 디스크를 못쓴다.

##### RAID 1
미러링이라 부름
1TB 1TB 를 사용하면 1TB 사용가능
결함 허용을 제공= 신뢰성 높음. 즉 둘중에 하나가 고장나도 데이터가 보존된다.
공간효율이 나쁘므로 비용이 비싸다는 단점이 있다.

##### RAID 5
RAID 2,3,4의 장점을 포용하기 때문에 2,3,4를 사용할 필요가 없다.
RAID1의 데이터 안정성 +RAID0의 공간 효율성을 추구 디스크 개수 -1개의 공간을 사용. 즉 4개일 경우 3개를 사용(75%) 100개일 경우 99개를 사용(99%)
즉 가격이 저렴하면서 안정적인것.
최소한 3개 이상의 하드디스크가 필요하며 오류가 발생했을 때는 패리티(Parity)를 이용해서 데이터를 복구
결함 허용을 제공 - 즉 디스크 하나가 고장나도 사용 복구 가능
하지만 디스크가 하나가 고장났을 때는 복구가 가능하지만 디스크가 2개가 고장날 경우에는 복구 불가능.
그래서 100개를 사용하였을 경우에는 불안하다.(2개 이상 고장날 확률이 높아지므로)
즉 적은 디스크를 활용할 경우 공간효율은 낮아지지만 신뢰도는 높아진다.
일반적으로 RAID 5는 7개에서 10개정도의 디스크로 구성

##### RAID 6
패리트를 2개 쓴다. 즉 디스크가 2개가 고장나도 복구가능. 최소 4개이상의 디스크가 필요함.(패리티를 2개 쓰므로)
패리티를 2개 쓰는 알고리즘의 효율성이 떨어져서 성능이 떨어지는 단점이 있어 많이 사용하지는 않음.

##### RAID1 + 0 = RAID + RAID0
신뢰성(안정성)과 성능(속도)이 동시에 뛰어난 방법
RAID1로 구성된것을 RAID 0 으로 묶음. 그림참고

##### 실습
SCSI 0:1 부터 0:10 까지 진행.

SCSI 0:1  SCSI 0:2 - Linear RAID

SCSI 0:3  SCSI 0:4 - RAID 0

SCSI 0:5  SCSI 0:6 - RAID 1

SCSI 0:8  SCSI 0:9  SCSI 0:10 - RAID 5

SCSI 0:1 만 2GB로 구성하고 나머지는 다 1GB로 구성

에디트 쪽에서 디스크 2기가 1개 1기가 8개 추가.


{% codeblock %}
# ls -l /dev/sd*      //장착이 잘 되었나 확인
{% endcodeblock %}


9번 반복
{% codeblock %}
# fdisk /dev/sdb ~sdj 까지 반복
n   p   엔터 엔터
p //로 확인해보면 시스템이 리눅스인데 RAID로 변경해야한다.
t 엔터
L 누르면 도움말이 나온다.
fd를 입력 후 엔터
p를 눌러 확인
w를 눌러 저장
{% endcodeblock %}

{% codeblock %}
# ls -l /dev/sd*      //장착이 잘 되었나 확인
{% endcodeblock %}

#### 과정 설명
1.mdadm 명령어로 논리 볼륨 생성
2.mkfs.ext4 명령어로 파일시스템 생성(포멧)
3.mount 적용
위 3가지 과정은 6장에서 하드디스크 1개추가 과정과 같다.

#### Linear RAID 만들기
sdb1 sdc1 이용
mdadm RAID를 만들고 없애는 명령어

{% codeblock %}
# fdisk -l /dev/sdb /dev/sdc // 아까 한것이 잘 적용되었나 확인

# mdadm --create /dev/md9 --level=linear --raid-devices=2 /dev/sdb1 /dev/sdc1
        //RAID md9를 만드는데 리니어로 디바이스는
        //개수 2개로 2개는 /dev/sdb1 /dev/sdc1 로

# mdadm --detail --scan     //돌고있는지 확인
# mdadm --detail /dev/md9   //md9에 대해 자세히 보자

# mkfs.ext4 /dev/md9    //포멧하자

# mkdir /raidLinear   //디렉토리 생성

# mount /dev/md9 /raidLinear/ //마운트

# df      //마운트 확인
# vi /etc/fstab     //재부팅하면 마운트가 풀리므로 설정

/dev/md9    /raidLinear   ext4    defaults    0 0    
맨 아래 추가

# reboot
# df    //마운트 확인
{% endcodeblock %}


#### RAID 0 만들기
sdd sde로 한다
{% codeblock %}
# mdadm --create /dev/md0 --level=0 --raid-devices=2 /dev/sdd1 /dev/sde1

# mdadm --detail --scan     //돌고있는지 확인
# mdadm --detail /dev/md0   //md9에 대해 자세히 보자

# mkfs.ext4 /dev/md0    //포멧하자

# mkdir /raid0   //디렉토리 생성

# mount /dev/md0 /raid0/ //마운트

# df      //마운트 확인

# vi /etc/fstab     //재부팅하면 마운트가 풀리므로 설정

/dev/md0    /raid0   ext4    defaults    0 0   
추가
{% endcodeblock %}

#### RAID 1
sdf sdg 사용
{% codeblock %}
# mdadm --create /dev/md1 --level=1 --raid-devices=2 /dev/sdf1 /dev/sdg1
y입력  // raid1은 부트 디바이스로 사용 할 수 없는데 만들겠냐는 뜻

# mdadm --detail /dev/md1   //확인해보자

# mkfs.ext4 /dev/md1    //포멧하자

# mkdir /raid1   //디렉토리 생성

# mount /dev/md1 /raid1/ //마운트

# df      //마운트 확인

# vi /etc/fstab     //재부팅하면 마운트가 풀리므로 설정
/dev/md1    /raid1   ext4    defaults    0 0   
추가
{% endcodeblock %}


#### RAID 5
sdh sdi sdj 사용
{% codeblock %}
# mdadm --create /dev/md5 --level=5 --raid-devices=3 /dev/sdh1 /dev/sdi1 /dev/sdj1

망가졌다는 메세지 나오는데 무시하면된다. (컴퓨터가 착각함!)

# mdadm --detail /dev/md5   //확인해보자

# mkfs.ext4 /dev/md5    //포멧하자

# mkdir /raid5   //디렉토리 생성

# mount /dev/md5 /raid5/ //마운트

# df      //마운트 확인

# vi /etc/fstab     //재부팅하면 마운트가 풀리므로 설정

/dev/md5    /raid5   ext4    defaults    0 0  
{% endcodeblock %}
