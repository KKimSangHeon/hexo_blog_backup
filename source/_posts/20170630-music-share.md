---
title: Music Share
date: 2017-06-30 09:31:38
categories:
- About Me
- My Projects
tags:
- C
- Socket
- Music Share
- Ubuntu
---
# 프로그램 개발배경
-C 소켓프로그래밍에 대한 이해를 높이고자 진행한 프로젝트입니다.

# 프로그램 소개
-본 프로그램은 회원가입기능, 로그인기능, 서버에 mp3파일을 올리는기능 등이 구현되어 있습니다. 회원가입을 하고 로그인 후 서버에 mp3파일을 올리거나 다운로드 받을 수 있습니다. 프로그램 개발환경은 우분투 14.04 LTS 입니다.

# 프로그램 기능

{% asset_img music-share1.png 서버에 접속한 클라이언트[그림1] %}
초기 서버에 접속하였을 경우 화면입니다. 서버에서는 클라이언트의 접속현황 및 상태를 확인할 수 있으며 클라이언트에서는 다음에 할 수 있는 일을 확인할 수 있습니다.

{% asset_img music-share2.png 두 클라이언트가 회원가입[그림2] %}
두 클라이언트가 회원가입을 진행하고 있고 이에 대한 내용이 서버측 화면에 출력됩니다.

{% asset_img music-share3.png 회원가입한 ID로 로그인[그림3] %}
클라이언트가 가입한 ID를 이용하여 로그인을 하였습니다. 로그인 후 에는 서버에 존재하는 mp3파일리스트를 조회하거나 mp3파일을 업로드, 다운로드 할 수있습니다.

{% asset_img music-share4.png 로그인 후 mp3파일 업로드[그림4] %}
클라이언트가 로그인을 한 후 mp3파일을 업로드하는 화면입니다.

{% asset_img music-share5.png 로그인 후 mp3파일 조회[그림5] %}
클라이언트가 로그인을 한 후 서버에 존재하는 mp3파일을 조회하는 화면입니다.

{% asset_img music-share6.png mp3파일 다운로드[그림6] %}
[그림4]에서 KSH클라이언트가 업로드한 hello.mp3 파일을 HRA클라이언트가 접속하여 다운로드 하는 화면입니다.

{% asset_img music-share7.png 비밀번호 찾기기능[그림7] %}
비밀번호를 찾기위해 ID, Email을 입력하였고 이 후 비밀번호가 화면에 출력되는 모습입니다.

[github Repository](https://github.com/KKimSangHeon/music_share)
