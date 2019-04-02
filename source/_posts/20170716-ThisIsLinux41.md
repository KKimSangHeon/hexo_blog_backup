---
title: 18장. 방화벽 컴퓨터를 만들자
date: 2017-07-16 11:04:12
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
방화벽은 내부, 외부를 차단해주는 장비 또는 컴퓨터라고 생각하면 된다. 방화벽으로 인해 외부에서 내부 네트워크로 함부러 침입할 수 없다. 그 기법중 많이 쓰이는 것이 사설IP이다.
방화벽 컴퓨터는 랜카드가 두개여야 된다. 내부와 결합되는(사설 IP) 랜카드 외부와 연결되는 랜카드(공인IP)

실습에서 192.168.xxx는 공인 ip
10.1.xxx 는 내부ip로 사용한다
책 737을 통해 네트워크 구성을 확인하자


#### 1. 서버 B 설정(웹서버로 사용할것임)
서버B의 네트워크 세팅에 들어가서 네트워크 어댑터 클릭 후 Bridged 클릭
그러면 네트워크가 Bridged네트워크 안으로 들어옴(사설ip로 사용하기로 함)
그 후 ip를 변경하자

{% codeblock %}
# nmtui edit ens32
Address를 10.1.1.20/24로 변경
Gateway를 10.1.1.1로 변경
Dns 168.126.63.1로 변경  //kt에서 운영하는 dns 서버
ok버튼
# reboot

# ip addr
 inet이 10.1.1.20 인지 확인
{% endcodeblock %}

#### 2. 클라이언트 설정(클라이언트에서 실행)
클라이언트의 네트워크 세팅에 들어가서 네트워크 어댑터 클릭 후 Bridged 클릭 그러면 네트워크가 Bridged네트워크 안으로 들어옴
{% codeblock %}
# nmtui edit ens33
IPv4 CONFIGURATION을 Manual로 바꾸고 Show 엔터
Address를 10.1.1.10/24
Gateway는 10.1.1.1
DNS Server는 168.126.63.1  
OK 엔터
# reboot

# ip addr
{% endcodeblock %}

#### 3. 서버B,클라이언트 사설네트워크 테스트
현재까지 구현한것(서버B,클라이언트)은 게이트웨이가 구현되어있지 않으므로 외부로 나갈 수 없다. 하지만 사설네트워크로 안에서는 연결되어 있으므로 ping이 왔다갔다 하는지 확인해보자

클라이언트에서 실행
{% codeblock %}
# ping -c 3  10.1.1.20
{% endcodeblock %}

#### 4. 서버 구성(서버에서 실행)
#### 4.1 랜카드 하나가 외부로 연결 잘 되어있는지 확인해보자
{% codeblock %}
# nmtui edit ens32
게이트웨이, DNS Servers가 192.168.111.2로 잘 되어있다.
# halt -p
{% endcodeblock %}
서버에 랜카드를 하나 더 달자.
vmware 워크스테이션에서 Edit virtual machins settings를 눌러 Add 후 Network Adapter 선택 다음 후 Bridged 선택하고 Finish 그 후 부팅
추가한 장치가 ens32인지 확인해보자
날짜 옆에 랜카드 모양을 누르면 ens34가 추가되어 있다. 네트워크설정 클릭 후 오른쪽 아래 톱니모양(유선연결1)을 누르고 신원을 누르자. 그 후 ens34로 입력하자
다음 IPv4를 선택하여 자동을 수동으로 변경하자
그 후 주소 10.1.1.1  
네트마스크 255.255.255.0
게이트웨이 10.1.1.1
네임서버 끄자
IPv6로 가서 끄자
이제 적용을 누르고 재부팅하자

{% codeblock %}
# vi /etc/sysconfig/network-scripts/ifcfg-ens34
6,7행 지워버리기
# systemctl restart network
# ifconfig
ens32 는 192.168.111.100번으로 ens34는 10.1.1.1
{% endcodeblock %}
이제 하드웨어 설정은 끝났다

#### 5. 클라이언트에서 핑을 날려보자
{% codeblock %}
# ping 10.1.1.1
{% endcodeblock %}

#### 6. 서버에 정책을 적용시켜보자
{% codeblock %}
# vi /etc/sysctl.conf
net.ipv4.ip_foward=1 맨아래 입력

# echo 1 > /proc/sys/net/ipv4/ip_forward
      //아무 메세지 안나오면 성공
# cat /proc/sys/net/ipv4/ip_forward
      //1출력됨

포워딩성공!

# iptables --policy FORWARD DROP
# iptables --policy INPUT DROP
# iptables --policy OUTPUT DROP

ens34 장치를 설정해주자
input 설정
# iptables --append INPUT --in-interface ens34 --source 10.1.1.0/24 --match state --state NEW,ESTABLISHED --jump ACCEPT

output 설정
# iptables --append OUTPUT --out-interface ens34 --destination 10.1.1.0/24 --match state --state NEW,ESTABLISHED --jump ACCEPT

# iptables --append FORWARD --in-interface ens34 --source 10.1.1.0/24 --destination 0.0.0.0/0 --match state --state NEW,ESTABLISHED --jump ACCEPT

ens32 설정
# iptables --append FORWARD --in-interface ens32 --destination 10.1.1.0/24 --match state --state ESTABLISHED --jump ACCEPT

# iptables --table nat --append POSTROUTING --out-interface ens32 --jump MASQUERADE

설정한것 저장
# service iptables save
# firewall-config
영구적 - 마스커레이딩 - 마스커레이딩영역 체크 - 옵션 다시불러오기
{% endcodeblock %}

#### 7.클라이언트에서 인터넷 해보자
이 경우 클라이언트는 자신의 ip(10.1.1.0)이 아닌 Server IP(192.168.111.100)으로 접속하게 된다. 이를 확인해보자

#### 7.1 윈 클라이언트를 켜자
파일질라서버를 카페에서 받자
에딧 -유저- 오른쪽add -centos 입력- password centos -ok
왼쪽 add 아무 디렉토리 선택- write/delete 선택 -ok
cmd 열고 netsh advfirewall firewall add rule name="FTP서버" dir=in action=allow protocol=tcp localport=21
방화벽 설정 입력
ipconfig
아이피를 기억하자 (지금은 192.168.111.128임)

#### 7.2 클라이언트에서 FTP 접속해보자
{% codeblock %}
# su
# yum -y install ftp
# ftp 192.168.111.128
centos
centos
>pwd
>ls
{% endcodeblock %}


#### 7.3 윈도우 클라이언트에서 누가 접속했는지 확인해보자
netstat /an
찾다 보면 192.168.111.100이 들어온것을 확인할 수 있다.
즉 사설네트워크 컴퓨터는 외부로 나갈 때 192.168.111.100으로 나간다!

#### 8. 서버B를 웹서버로 만들고 윈 클라이언트로 접속해보자

#### 8.1 서버 B에서 실행

{% codeblock %}
# yum -y install httpd
# firewall-cmd --add-service=http    //포트열자
# cd /var/www/html
# touch index.html
# vi index.html
Centos7-Web Server 입력
저장 후 종료

# systemctl restart httpd
# systemctl enable httpd
{% endcodeblock %}


#### 8.2 서버에서 80번 포트로 연결오면 서버 B로 연결해라를 설정
서버에서 실행
{% codeblock %}
# iptables --table nat --append PREROUTING --proto tcp --in-interface ens32 --dport 80 --jump DNAT --to-destination 10.1.1.20
# service iptables save   //저장
{% endcodeblock %}

#### 8.3 윈도우 클라이언트에서 접속해보자
192.168.111.100 으로 접속하자
