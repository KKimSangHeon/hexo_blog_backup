---
title: 기숙사 외박계
date: 2017-06-30 10:22:14
categories:
- About Me
- My Projects
tags:
- Node.js
- Kakao talk open API
- 챗봇
- MySQL
- CentOS
---

# 프로그램 개발배경
-교내 기숙사의 외박계 프로그램이 존재하지 않으며, 업체에 의뢰하여 제작하려했으나 비용상의 문제로 거절된것을 알게되었습니다. 그래서 직접 만들어서 제출하면 어떨까 라는 생각을 하게되어 개발하게 되었습니다.

# 프로그램 소개
CentOS상에 서버를 실행시켰으며, 카카오톡 옐로아이디 페이지에서 세팅을 진행하였습니다. 각 과정을 거친 후 자신의 외박계를 하나 작성할 수 있는 프로그램입니다.

# 프로그램 동작화면

{% asset_img Dormitory-Sleep-Out1.png 초기접속화면 [그림1] %}
초기 채팅방에 진입하였을 경우 외박계를 작성할 것인지, 작성한 외박계를 확인할 것인지 선택합니다.
{% asset_img Dormitory-Sleep-Out2.png 외박계 작성절차1 [그림2] %}
외박계 작성을 선택하였을 경우 이름, 거주하는 동, 호수, 귀관일, 행선지를 입력하고
{% asset_img Dormitory-Sleep-Out3.png 외박계 작성절차2 [그림3] %}
전화번호 또한 입력 후 아무키나 입력하면 등록이 완료됩니다.
{% asset_img Dormitory-Sleep-Out4.png [그림1]에서 확인을 선택 [그림4] %}
[그림1]에서 확인버튼을 선택하였을 경우 [그림4]와 같이 입력한 내용을 확인할 수 있습니다.

{% asset_img Dormitory-Sleep-Out5.png 관리자계정모드 [그림5] %}
작성된 외박계를 관리자가 확인하기 위해서는 채팅방에서 supervisorgj@를 입력합니다. 그 후 [그림5]와 같이 외박계를 조회할 수 있습니다.


# 소스코드 (Github Repository)
[-카카오톡 자동응답 서버 소스코드](https://github.com/KKimSangHeon/DormitorySleepOut)
