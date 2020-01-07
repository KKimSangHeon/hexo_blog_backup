---
title: StreamSets Data Collector의 클러스터링 방법
date: 2020-01-07 20:22:41
categories:
- CS
- 오픈소스,기술
tags:
- Streamset
thumbnail: /images/streamsets.png
---

https://streamsets.com/blog/five-ways-scale-kafka-streamsets/

### 1.Vertical Scaling – Deploy a Bigger Box
가장 간단한 방법으로 여러개의 컨슈머 스레드를 생성하는 방법
컨슈머 스레드를 병렬로 실행하게 됨
서버가 죽으면 당연히 데이터 flow 또한 죽게됨.

`카프카 관련지식으로 스레드는 카프카의 파티션의 수 보다 작아도 된다.`

---
# Horizontal Scaling – Deploy More Boxes


### 2.Manually Run Multiple Data Collectors
수동으로 여러 인스턴스를 생성 후 각 인스턴스에서 파이프라인을 돌리는것이다.
이방법의 경우 간단한 방법이지만 수동으로 각 인스턴스의 파이프라인을 제어해야 하기 때문에 운영자가 번거로울 수 있다.

- 장점
하나의 인스턴스가 죽게될 경우 카프카는 해당 파티션을 나머지 인스턴스에 할당하기 때문에 데이터 흐름을 유지할 수 있다.

`1.Vertical Scaling – Deploy a Bigger Box 와의 차이는 인스턴스의 수`


---
### 3. Run the Pipeline in Cluster Streaming Mode
분산 컴퓨팅 환경을 제공하는 YARN(Yet Another Resource Negotiator) 혹은 아파치 Mesos cluster 를 사용했다면 Data Collector’s Cluster Streaming mode를 사용하는것이 좋다.
파이프라인의 실행모드를 ‘Cluster YARN Streaming’ or ‘Cluster Mesos Streaming’로 설정하고 카프카 consumer을 생성하면 카프카 토픽에 부여된 파티션만큼의 노드를 요청하게 된다.

- 장점
운영자가 파이프라인을 수동으로 여러개 생성할 필요가 없으며 클러스터 플랫폼(YARN or Mesos )이 스케일링을 관장한다
파티션 수가 변경되면 파이프라인을 재시작 하면된다.
또한 YARN의 경우 노드장애가 발생할경우 클러스터 플랫폼이 알아서 다시시작해준다.

---
### 4.Use StreamSets Control Hub to Start Multiple Pipeline Instances ( 유료 )
YARN이나 Mesos를 사용하지 않지만 Data Collector의 인스턴스를 자동으로 관리하고 싶을 때 StreamSets Control Hub를 사용할 수 있다.
StreamSets Control Hub를 사용하면 UI에서 Data Collector의 인스턴스, 파이프라인을 관리할 수 있다.

- 장점
인스턴스 관리 설정만 해두면 알아서 해줌
한 인스턴스가 오프라인이 되면 다른 인스턴스로 스스로 대체한다.

---

### 5.Use StreamSets Control Hub with Kubernetes to Start Multiple Data Collector Containers On Demand (유료)

쿠버네티스 클러스터를 사용한다면  Control Hub’s Kubernetes Control Agent를 사용할 수 있다.
Google Kubernetes Engine 또는 Azure Kubernetes Service 에서도 사용 가능하며 데이터 센터에도 배포가 가능하다.
YARN or Mesos 보다 유연한 클러스터링 환경을 제공한다.

- 장점
Control Agent는 Control Hub와 통신하여 실행중인 Kubernetes 클러스터에서 Data Collector 컨테이너를 자동으로 프로비저닝합니다.
`프로비저닝(deploying, registering, starting, scaling, and stopping the Data Collector containers)`



---
###  Cluster Streaming Mode vs StreamSets Control Hub
YARN이나 Mesos를 사용하지 않지만 Data Collector의 인스턴스를 수동으로 관리하고 싶지 않을 때 StreamSets Control Hub를 사용할 수 있다.

Control Hub는 Cluster Streaming Mode와 유사한 자동화 기능을 제공하지만 파이프 라인을 실행할 할 수 있는 Data Collector 인스턴스가 요구됨

Control Hub는 Data Collector 인스턴스의 모니터링, 하나의 인스턴스가 죽었을 때 대체하는 기능등을 제공한다.
Control Hub는 자체적인 통계기능을 제공함, 파이프라인 커밋 히스토리를 볼 수 있다.
