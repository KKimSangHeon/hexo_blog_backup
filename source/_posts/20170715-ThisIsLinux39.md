---
title: 16장. DHCP 서버 설치와 운영
date: 2017-07-15 16:09:22
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
DHCP(Dynamic Host Configuration Protocol) 서버는 자동으로 네트워크 정보(IP주소, 서브넷 마스크, 게이트웨이 주소,DNS 서버 주소)를 할당해 주는것이다. 일반 PC는 자신에게 고정IP가 할당되어있지 않아도 DHCP서버가 할당해준다. 그러므로 일반사용자는 IP에 대한 지식 없이도 인터넷 사용이 가능해진다.

Vmware에서 제공하는 DHCP 서버의 기능은 중지시켜야 한다.
#### 클라이언트 설정
{% codeblock %}
# ifconfig
ip를 DHCP로부터 할당받은것.
# cat /etc/resolv.conf
nameserver 또한 DHCP로 부터 할당받은것.
{% endcodeblock %}

#### 1. DHCP 기능중지
워크스테이션 - Edit - Virtual Network Editor - change setting 선택 - VMnet8선택 - DHCP Setting 선택
128부터 254까지를 할당하는 것을 확인

Use local DHCP .... 클릭 해제
Apply
이제 IP를 자동으로 할당받지 못한다.

#### 2. DHCP 서버 설정하자.
{% codeblock %}
# yum -y install dhcp
# ps -ef | grep dnsmasq   //dhcp와 충돌하므로 죽이자.
nobody    1696     1  0 18:21 ?        00:00:00 /sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf
root      3514  2462  0 18:28 pts/0    00:00:00 grep --color=auto dnsmasq

# kill -9 1696    //다를 수 있다.
# systemctl disable dnsmasq //껏다켜도 실행 되지 않도록.

# vi /etc/dhcp/dhcpd.conf   //dhcp 설정파일
아래를 추가하자. (엔터로 인한 공백이 존재하면 안된다)

ddns-update-style  interim;
subnet  192.168.111.0   netmask 255.255.255.0 {
        option  routers  192.168.111.2;   
                          //라우터설정
        option  subnet-mask  255.255.255.0;
                          //서브넷마스크설정
        range   dynamic-bootp 192.168.111.30  192.168.111.50;
                          //ip범위 설정 30~50까지 20개
        option  domain-name-servers 192.126.63.1;
                          //dns설정(kt에서 제공하는것)
        default-lease-time      10000;
                          //디폴트 임대시간
        max-lease-time          50000;
                          //최대임대시간
}             

# ls /var/lib/dhcpd
dhcpd.leases에 ip를 임대해준것에 대한 기록이 있다.

# systemctl restart dhcpd
# systemctl enable dhcpd  //상시가동
# systemctl status dhcpd  //잘 작동하는지 확인

# systemctl stop firewalld

# systemctl restart dhcpd
{% endcodeblock %}


#### 3. 클라이언트에서 IP 할당받아보자
{% codeblock %}
# su -c 'systemctl restart network'
password 입력
# ifconfig    //아이피 확인해보자
192.168.111.30을 받았다. (아까 30~50을 지정해줌)
{% endcodeblock %}

#### 4. 윈도우 클라이언트에서 IP 할당받아보자
네트워크 - 공유센터 - 어댑터 - 로컬 영역 연결을 사용안함으로 했다가 사용으로 변경 - cmd를 켜고 ipconfig 를 입력하면
192.168.111.31을 받은것을 확인할 수 있다.

#### 5. 서버에서 빌려간것들을 확인해보자
{% codeblock %}
# cat /var/lib/dhcpd/dhcpd.leases
IP를 언제빌려갔고 누가 빌려갔고 맥어드레스 등을 확인할 수 있다.
{% endcodeblock %}

#### 6. 다시 워크스테이션의 DHCP를 켜자
워크스테이션 - Edit - Virtual Network Editor - change setting 선택 - VMnet8선택 - DHCP Setting 선택 - Use local DHCP ...선택
