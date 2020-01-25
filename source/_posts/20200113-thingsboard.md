---
title: Thingsboard
date: 2020-01-13 20:24:26
categories:
- CS
- 오픈소스,기술
tags:
- Streamset
thumbnail: /images/thingsboard.jpg
---
### Thingsboard
- 오픈소스 IoT 플랫폼으로서 데이터 수집, 처리, 시각화, 디바이스 관리 기능 등을 제공
- 클라우드, on-premise 환경에서 손쉬운 설치를 제공하며 데이터 유실이 없음 (한 노드가 죽었을 때 downtime 없이 대체 가능)
- Multi-tenancy 구조
- Downtime 없는 Scale Out 가능
- SQL / NoSQL / SQL + NoSQL 사용 가능


### 가격정책
{% asset_img 1.jpg 가격정책 %}
사용 환경에 따른 다양한 가격정책이 존재. 영구사용의 경우 2999$


### Use Case
{% asset_img 2.jpg Use Case %}

### 제공 설치환경
{% asset_img 3.jpg 가격정책 %}
다양한 플랫폼에서 설치를 지원하므로 on premise 혹은 cloud 환경에 손쉽게 설치 가능.

### Multi-tenancy
ThingsBoard의 유저 구분
- System Administrator
- Tenant Administrator
- Customer

### Asset
{% asset_img 5.jpg Asset %}
논리적 구성단위인 Asset
- Asset과 Device를 계층 형태(Contain, Manage), 즉 상위/하위 개념으로 구성가능
- Device, Asset에 속성 정보 지정가능

속성정보 활용예
- Device의 속성정보에 위경도를 지정해 지도상에 표시 가능

### Multi-tenancy & Asset의 활용예
{% asset_img 6.jpg Multi-tenancy & Asset %}


### System Administrator의 역할
- IoT 플랫폼을 사용할 Tenant 관리
- 위젯관리
- 보안설정

Security Setting 내 항목
- 최대 로그인 시도횟수, 계정이 잠기게 될 경우 메일을 수신할 주소 설정
- 최소 패스워드 길이 설정
- 최소 소문자/대문자/숫자/특수문자 의 수 설정
- 패스워드 유지 기간 설정


### Tenant Administrator의 역할
- 디바이스, 룰, 대시보드, 위젯 등을 관리하며 Customer 가 각종 정보를 확인할 수 있도록 권한을 부여 함.


### 기능 정리

{% asset_img 7.jpg 룰관리 기능 %}
{% asset_img 8.jpg 디바이스 연동 %}
{% asset_img 9.jpg 기타 %}
