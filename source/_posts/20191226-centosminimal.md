---
title: VirtualBox에 Centos Minimal 설치
date: 2019-12-26 21:10:51
categories:
- OS
- Linux
tags:
- centos
thumbnail: /images/centoslogo.png
---
# Centos minimal 설치 및 기본 세팅
Virtual Box 이용


```
$ vi /etc/sysconfig/network-scripts/ifcfg-enp0s3

네트워크 설정 파일을 열어 필요한 부분을 수정한다. 단, ifcfg-enp0s3 부분은 장비에 따라 다르게 나올 수 있다.
기본적으로 dhcp를 사용하도록 되어 있을텐데 dhcp를 그대로 사용한다면 ONBOOT=no 부분을 ONBOOT=yes으로 바꿔주기만 하면된다. dhcp를 사용하지 않는다면 사용할 IP를 지정한다

# service network restart


# ip addr

# yum install -y openssh-server net-tools

# service sshd start
Redirecting to /bin/systemctl restart  sshd.service

# chkconfig sshd on



# firewall-cmd --zone=public --add-port=22/tcp --permanent

# service sshd start
```
