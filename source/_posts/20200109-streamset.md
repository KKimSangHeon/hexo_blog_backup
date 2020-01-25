---
title: StreamSets의 에러처리
date: 2020-01-09 21:59:00
categories:
- CS
- 오픈소스,기술
tags:
- Streamset
thumbnail: /images/streamsets.png
---


에러 처리는 Stage(Origin, Processor, Destination, Executor) 관점, 파이프라인 관점에서 처리방법이 존재

### Stage(Origin, Processor, Destination, Executor) 관점
Discard , Send to Error, Stop Pipeline

### 파이프라인 관점
Discard, Send Response to Origin, Write to Another Pipeline, Write to Elasticsearch/File/Kafka …


{% asset_img error1.png 에러처리 예시 %}
위 그림은 파이프라인을 총 2개 생성한 상황이다.

`카프카 컨슘 파이프라인`에서 에러가 발생할 경우 SDC RPC를 통해 다른 파이프라인으로 에러내용을 전달하도록 하였고 `에러처리 파이프라인`에서는 RPC로 받은 에러데이터를 Email로 보내고 Local 파일로 저장하는 상황이다.
