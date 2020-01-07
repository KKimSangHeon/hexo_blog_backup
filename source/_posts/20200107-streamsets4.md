---
title: StreamSets 이란
date: 2020-01-07 20:25:56
categories:
- CS
- 오픈소스,기술
tags:
- Streamset
thumbnail: /images/streamsets.png
---
### StreamSets란?
데이터 흐름을 관리할 수 있으며 Data drift에 유연하게 대처가능한 솔루션

{% asset_img SS1.jpg StreamSets의 플랫폼들 %}

### StreamSets 활용 및 장점 요약
글로벌 社에서 StreamSets을 Data Lakes, Big Data/Hadoop Ingestion, Event Streaming, IoT/edge device integration 등 용도로 사용중.

- 데이터센터, AWS, GCP 등에 배포 가능하며 YARN, MESOS, Kubernetes을 활용한 스케일링 가능.
- 처리량, 지연, 에러율 등을 UI로 확인할 수 있음.
- 민감한 데이터를 보호할 수 있음.

### StreamSets의 개발, 운영, Agility 측면의 장점

개발자 생산성 측면
- 코드작성의 최소화, 자동 인스턴스 관리, 파이프라인 로직의 재활용 가능

운영효율 측면
- 파이프라인 시각화, 파이프라인 성능 모니터링, 이벤트에 대한 동작설정 가능

Agility 측면
- Downtime 없는 파이프라인 내 데이터 시스템 업데이트, 데이터 변경 시 자동탐지 및 동기화, 버전 별 파이프라인의 성능 비교 가능


### StreamSets Data Collector
{% asset_img SS2.png 파이프라인 생성 시 예시 %}
위의 화면에서 드래그앤드롭 방식으로 데이터 flow를 파이프라인으로 생성 및 관리 가능

파이프라인은 Origin/Processor/Destination/Executor 로 구성된다.

{% asset_img SS3.png 파이프라인 요소별 구분 %}

#### Origin
- 파이프라인의 시작점을 의미하며 파이프라인 내 하나만 생성할 수 있다.
- Kafka / Redis / GCP / TCP / UDP 를 포함한 약 60여개를 data 근원지로 활용가능

#### Processor
- 데이터 처리, 가공
- Stream Selector / JSON Parser / Jython Evaluator /  Field Masker 를 포함한 약 50여개 기능 제공

#### Destination
- 파이프라인의 목적지
- Hbase / Local file / Redis / DB 를 포함한 50여개를 data 목적지로 활용가능

#### Executor
- 이벤트를 수신했을 때 동작 정의
- Email Executor / Shell Executor / JDBC Query Executor 를 포함한 약 10여개 기능 제공


세부지원 요소 확인하기
https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq
https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Executors/Executors-title.html
