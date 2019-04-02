---
title: 리눅스 개인폴더에 설치된 디렉토리 사용하기
date: 2018-03-30 08:29:17
categories:
- OS
- Linux
tags:
- Linux
thumbnail: /images/centoslogo.png
---
서버에 curl이 설치되어 있고 내 로컬에도 curl이 설치되어 있을 때 내 curl을 사용하기 위한 방법

vi ~/.bash_profile을 열고 아래를 입력하고


export CURL_DIR="$HOME/apps/usr/curl/bin"
export PATH=$CURL_DIR:$PATH

source ~/.bash_profile을 입력

curl -V를 통해 잘 적용되었나 확인
