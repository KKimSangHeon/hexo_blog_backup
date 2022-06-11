---
title: 소상공인 사업 활성화를 위한 프로젝트
date: 2022-06-11 16:47:38
categories:
- About Me
- My Projects
tags:
- Spring boot
- k8s
- jenkins
- MSA
---
### 1. 개발배경
소상공인의 사업 활성화를 위한 프로젝트를 진행하였으며 주 내용은 폐기 일자가 얼마 남지않은 음식을 특정 할인율을 적용해서 판매할 수 있도록 도와주는 플랫폼이다. 팀에서 인프라 구축, 서버 개발을 담당했던 프로젝트였다.

활용했던 항목들을 나열해 보자면 다음과 같다.
##### Infra & Middleware
Zipkin, EFK, Jenkins, K8S, AKHQ, ArgoCD, FCM, Slack, nginx ingress controller, kafka, postgres,

##### Libs & Framwork
Spring boot, Spring Security ,JWT, JPA, QueryDSL, H2, JUnit, JTS for geofencing, caffeine(Local Cache)
EFK, spring cloud config, bus, sleuth
Swagger, spring rest docs, Helm chart , Kustomize

##### Data
selenium, 공공데이터


### 2. 설계 및 구성
K8S 환경으로 구축하고자 minikube를 활용하였으며 상세 구성은 아래와 같다.
평소 궁금하고 언젠간 한번 훑어봐야지 했던 기술들을 한번씩 써본것같다.


![인프라 구성](/2022/06/11/closing-sale/infra.png)
![Kuberneties dashboard](/2022/06/11/closing-sale/dashboard.png)



##### 간단한 설명
- Helm을 활용하여 필요 항목을 설치하였으며 Jenkins를 활용해 CI/CD 환경을 구축하고 배포완료시 Slack로 noti를 보내도록 설정하였다.
- 공통 property 들은 spring-cloud-config를 활용하여 git에서 관리하도록 하였으며 actuator, spring-cloud-bus/kafka 를 활용하여 property 수정시 자동으로 반영되도록 설정하였다.
- 회원가입의 경우 spring-security 를 활용하여 kakao로그인이 가능하도록 구현하였다.

### 3. 배포관리

![Jenkins pipline](/2022/06/11/closing-sale/Jenkins.png)
![ArgoCD](/2022/06/11/closing-sale/argocd.png)
또한 Jenkins, ArgoCD를 활용하여 CI/CD, GitOps환경을 구축하였고 배포과정은 다음과 같다.

##### 배포 flow
- 소스코드 커밋시 git에서 설정해둔 webhook으로 인해 jenkins로 http 요청을 보낸다.
- jenkins는 소스코드를 clone하고 테스트 코드를 실행 / 빌드 후 이미지를 버전정보와 함께 docker hub로 push한다. 성공시 slack메세지 전송
- deployment.yaml이 위치한 별도 git repository에 Kustomize를 활용하여 docker hub에 반영된 이미지와 동일한 버전으로 수정 후 push 한다.  
- deployment.yaml파일의 변경을 인지한 argoCD가 서버에 동기화한다.

![빌드완료 후 Slack 메세지 수신](/2022/06/11/closing-sale/slack.jpg)

### 4. 모듈구성
어플리케이션 서버는 세 모듈로 나눠 개발하였다.

`Oauth Server`
kakao와 연동하여 로그인 성공시 jwt토큰을 전달해준다.

`API Server` :
Manager, Customer API가 존재하며 각 권한을 갖고있는 클라이언트만 api를 호출할 수 있다.
3개의 pod로 구성되어있으며 내장톰캣을 활용하여 동작한다.
주문/상품 등록시 client에게 알리기 위해 kafka에 데이터를 생성한다.

`Push Server` :
주문/상품 등록시 API Server가 생성한 데이터를 kafka로 부터 읽어들여 client에게 전송하기 위해 fcm으로 메세지를 전송한다.
Order, Stock 의 파티션을 두개로 구성하여 2개의 pod로 설정하였다.

### 5. Geofencing
구 별로 음식점 데이터를 캐싱해놓은 후 음식점 조회 api가 들어오면 JTS를 활용해 특정 n meter 이내 위치한 음식점 정보를 추출할 수 있었다.
음식점 데이터 조회 api는 quadtree라는 data struct를 활용하여 reponse시간을 최소화 할 수 있었다.

참고: https://www.smartycoder.com/2021/04/what-is-quadtree-example-usage-in-java-jts.html

### 6. Opentracing
Opentracing을 위해 서버에 zipkin을 설치하였고 EFK환경을 구축하여 Fluentd를 daemonset으로 구성하였다.
Fluentd는 pod에서 생성해내는 로그를 ElasticSearch로 보내고 kibana를 활용하여 이를 확인하였다.
또한 각 pod는 traceId, spanId를 만들어 zipkin 서버로 보내 확인할 수 있도록 하였다.

![zipkin](/2022/06/11/closing-sale/zipkin.png)

### 7.데이터 수집
초기 데이터를 구성하기 위해 음식점, 메뉴데이터가 필요했다.

음식점 데이터의 경우 공공데이터 포털에서 서울시 소상공인시장진흥공단_상가(상권)정보 를 csv 로 다운로드 후 파싱하여 db에 insert 하였다.
참고 : https://www.data.go.kr/data/15083033/fileData.do

메뉴 데이터는 selenium을 활용하여 kakao map에서 메뉴데이터를 검색 후 스크래핑 소스를 작성하고 db에 insert 하도록 설정하였다.


### 8.협업
app/web 개발자와 협업하기 위해 swagger, spring rest docs를 활용하였다.
![swagger](/2022/06/11/closing-sale/swagger.png)

![docs](/2022/06/11/closing-sale/restdocs.png)
