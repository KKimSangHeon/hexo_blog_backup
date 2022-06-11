---
title: ServerMonitoring Tool 개발
date: 2022-02-09 21:19:52
categories:
- About Me
- My Projects
tags:
- Django
- Vuejs
---

### ServerMonitoring 개발
한 관리자가 여러 서버를 한눈에 관리할 수 있는 웹 페이지가 있으면 좋겠다고 생각을 했고 djang, vue를 공부해볼겸 진행해본 프로젝트.

#### 벤치마킹
다음 항목들을 벤치마킹하여 어떤식으로 활용될 수 있나 참고하였다.
`Ansible` `Jenifer` `datadog` `skwissh` `netdata` `munin`

#### 사용기술
`django (python)`
`django admin`
`vue`
`sqlite3`
`ORM`
`WSGI`
`conda`
`shell script`


### 설계
django 서버에서 여러 서버로 ssh로 접속 후 명령어를 질의하고 응답을 가져오는 형태.
client는 django 서버로 rest api 을 활용하여 서버의 현 상태를 파악할 수 있는 시스템을 개발하고자 함.

Server Monitoring / Management
    - yml 파일 활용하여 서버 구성 추가 및 관리
    - Process 모니터링 / 특정 Process가 열고있는 포트 확인 / 특정 프로세스 kill
    - CPU 사용율
    - Memory 사용율
    - Disk 사용율
    - 방화벽 개방여부 확인
    - File / Directory 조회

Installation
    - UI 상 모듈 install 기능 제공

Scheduler
    - Cron Job 관리
    - 즉시실행
    - 종료



### ERD

{% asset_img command.png 작동화면 %}

다수 서버 관리라는 특성상 서버의 OS, version에 따라 달라질 수 있는 부분(ex. 명령어)이 있기에 확장성을 고려한 설계를 하고자 하였음
CommandType은 어떤 행위를 할 지 추상적인 명칭을 나타내며 Command는 CommandType에 명시된 행위를 실행하기 위한 상세설명의 의미를 갖는다.





{% asset_img  K-000.png 서버등록 %}
서버는 위와 같이 Django admin페이지에서 등록 후 활용가능하다.

{% asset_img ERD.png ERD %}
### 기능
모든 기능은 우측 상된에 선택된 서버 내에서 발생합니다.

{% asset_img  K-001.png 설치패키지 조회 %}
설치된 패키지 조회 및 삭제기능


{% asset_img  K-002.png 패키지 설치 %}
필요 파일 업로드 후 설치
rpm 파일을 업로드할 경우 django서버에 파일이 업로드되고 설치버튼을 누르면 현재 선택된 서버에 설치된다.


{% asset_img  K-003.png CPU사용률 %}
cpu 사용율 조회

{% asset_img  K-004.png memory 조회 %}
memory 조회. top 명령어 기반으로 동작하므로 해당 명령어 실행이 실패할 경우 위와 같이 alert창이 뜬다.


{% asset_img  K-005.png terminal %}
terminal과 같이 여러 명령어를 입력하여 확인할 수 있다.


{% asset_img  K-006.png firewall %}
방화벽 오픈여부를 확인한다.

{% asset_img  K-007.png 프로세스를 확인 %}
현재 동작중인 프로세스를 확인하며 해당 프로세스가 열고 있는 port 확인, process kill 또한 가능하다.

{% asset_img  K-008.png directory 상세 조회 %}
directory 상세 조회


{% asset_img  K-009.png terminal %}
위 화면에서 시나리오를 등록할 수 있다.
시나리오란 일련의 동작이 정해진 주기마다 돌도록 하는 기능이다.

{% asset_img  K-015.png 시나리오 등록 %}
CRON expression, DATE, INTERVAL 을 지원하며 Drag & Drop로 아래와 같이 시나리오를 작성할 수 있다.

{% asset_img  K-011.png Action %}
스케줄을 생성할 때 세부 동작을 Action이라 한다.
시나리오는 한개의 Source, N개의 Destination으로 구성된다.

Source는 Cpu사용율, file tail, 프로세스 검색이 있으며 Destination 으로는 HTTPGet, Post, SlackNotification을 설정할 수 있다.

{% asset_img  K-013.png Action %}
다음과 같이 오전 0850분에 SlackNotification이 오도록 설정해 두면 서비스 모니터링도구로써 활용할 수 있다.
{% asset_img  slack.png SlackMessage %}
위와 같이 지정된 시간에 메세지가 온다.

{% asset_img  K-016.png 서버접속로그 %}
ssh를 통한 서버접속 히스토리를 볼 수 있는 화면이다.

{% asset_img  K-017.png ipinfo %}
ipinfo버튼을 클릭하면 접속한 ip에 대한 자세한 정보를 확인할 수 있다.


{% asset_img  K-018.png 접속실패로그 %}
ssh를 통해 접속을 시도했으나 실패한 히스토리이다.
{% asset_img  K-019.png ipinfo %}
ipinfo버튼을 클릭하면 접속을 시도한 ip에 대한 자세한 정보를 확인할 수 있다.
