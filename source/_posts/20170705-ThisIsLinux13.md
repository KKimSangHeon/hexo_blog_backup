---
title: 5장. 노틸러스, FireFox업그레이드, 기타 X윈도 유틸리티
date: 2017-07-05 13:47:34
categories:
- OS
- Linux
tags:
- Linux
- This Is Linux
thumbnail: /images/centoslogo.png
---
#### 노틸러스
윈도우 탐색기와 비슷하다.
실행법 : 프로그램-보조프로그램-파일

내폴더가 홈 디렉토리이다.
컴퓨터가 / 폴더이다.
rpm 파일도 설치 가능
복붙도 가능 컨트롤 씨브이
289p 참고

#### 파이어폭스 업그레이드
설치하고
압축풀고

{% codeblock %}
# mv firefox /usr/local   //압축푼것 위치이동
# chown -R root.root /usr/local/firefox/ 루트에게 권한줌
# cd /usr/local/bin
# ln -s /usr/local/firefox/firefox . //링크를 걸어줌

파폭을 실행하면 에러가 나는데

# cd /usr/local/firefox/browser/extensions/
# rm -rf *
해주고 리부트
{% endcodeblock %}


#### 어도비리더 설치
{% codeblock %}
# wget http://download.hanbit.co.kr/centos/7/AdobeReader_kor-8.1.7-1.i486.rpm

# su -c 'yum -y localinstall Adobe*'
{% endcodeblock %}
