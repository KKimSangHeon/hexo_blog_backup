---
title: 12장. 클라우드 서비스 구축
date: 2017-07-14 17:04:41
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
클라우드 서비스를 구축해봅시다.
클라우드 서비스는 서버에 파일이 있고 각각의 PC마다 폴더를 지정하여 동기화가 된다. 즉 웹하드는 업로드 다운로드를 하지만 클라우드는 자동으로 동기화가 되는점이 다르다.

카페에서 관련 서버 두개를 깔자.
{% codeblock %}
# cd /root/다운로드
# yum -y localinstall owncloud- *

# ls -l /var/www/html // owncloud가 깔렸나 확인

# systemctl restart httpd //서비스 재시작
# firewall-config
//영구적 선택 - http,https//방화벽 설정 열기

{% endcodeblock %}
클라이언트에서 192.168.111.100/owncloud 접속
admin 1234 입력
자세한 사용법은 책을 참고하자!
