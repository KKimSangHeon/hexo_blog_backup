---
title: 리눅스 에러
date: 2017-07-01 13:45:37
categories:
- OS
- Linux
tags:
- Linux Error
thumbnail: /images/centoslogo.png
---

증상:
Virtualized performance counters are not supported on the host CPU type. Module VPMC power on failed. Failed to start the virtual machine 이라는 에러메세지 출력.

해결법:
.vmx 파일을 열고 vpmc.enable 부분을 "FALSE"로 수정.

---

증상:
quotaoff: command not found    , 쿼터에 대한 명령어가 실행이 안됨.

해결법:
{% codeblock %}
# yum install quota
{% endcodeblock %}

---

증상:
ifconfig가 먹히질 않는다.

해결법
{% codeblock %}
# yum install net-tools
{% endcodeblock %}

---

방화벽 설정
dns 방화벽 설정을 허용한다고 가정.

해결법
{% codeblock %}
# firewall-cmd --permanent --add-service=dns  //방화벽 설정 허용
# firewall-cmd --reload     //설정내용 적용
{% endcodeblock %}


---

증상:
받고싶은 패키지가 있는데 레드헷전용이다.

해결법
{% codeblock %}
# yum -y install epel-release   //레드헷에 접속해서 다운받을수 있도록 설치
{% endcodeblock %}

---

증상: vsftpd를 사용하는데 너무 느리다.

해결법
{% codeblock %}
# vi /etc/vsftpd/vsftpd.conf

# local_max_rate=0

# systemctl restart vsftpd

{% endcodeblock %}
