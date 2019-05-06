---
title: (K8s) 5. 여러 인스턴스를 실행하기. Scaling 하기
date: 2019-04-23 21:08:06
categories:
- CS
- MSA
tags:
- kubernetes
thumbnail: /images/K8s.png
---


#### Scaling
애플리케이션 스케일링하기
이전 모듈에서 서비스를 만들고 퍼블릭하게 노출했다. 해당 디플로이멘트는 하나의 파드로 증가하도록 했다. 트래픽이 증가하면 사용자가 요구하는대로 어플리케이션을 실행할 필요 있다,.
스케일링은 디플러이먼트의 어플리케이션 replicas를 몇개할지 정하는것이다.

하나만 감싸고 있는 서비스를 여러노드에 거쳐 서비스를 스케일 아웃을 할 수 있다.
디플로이먼트를 스케일 아웃하면 신규 파드가 생성되어서 가용한 자원이 있는 노드에 스케줄된다. 스케일링 인은 파드개수를 줄이는것을 의미한다.

이는 또한 오토스케일링을 지원하기도 한다.(즉 들어오는 유동적으로 결정해주기도 함)
제로로 스케일링하는것도 가능한데 이는 디플로이먼트에 정의되어있는 파드를 모드 제거 하는것이다.

애플리케이션의 인스턴스를 복수로 구동하게 되면 트래픽을 해당 인스턴스 모두에 분산시킬 방법이 필요해진다. 서비스는 노출된 디플로이먼트의 모든 파드에 네트워크 트래픽을 분산시켜줄 통합된 로드밸런서를 갖는다. 서비스는 엔드포인트를 이용해서 구동중인 파드를 지속적으로 모니터링함으로써 가용한 파드에만 트래픽이 전달되도록 해준다.

일단 여러인스턴스가 실행되면 롤링 업데이트를 자동으로 할 수 있다.

kubectl get deployments
를 입력하여 디플로이 먼트 리스트를 보자
DESIRED는 설정에서 정의된 replicas 개수를 의미
CURRENT는 현재동작중인 개수
UP-TO-DATE 는 DESIRED를 맞추기 위해 뭐가 바뀌었는지
AVAILABLE 유저한테 가용할 수 있는 개수

replicas을 네개로 바꿔보자
kubectl scale deployments/kubernetes-bootcamp --replicas=4

kubectl get deployments
를 입력하여 4개가 도는지 확인해보자

파드는 각 다른 아이피를 갖는데
kubectl get pods -o wide
를 입력하여 4개에 대해 자세히 볼수 있다.

kubectl describe deployments/kubernetes-bootcamp
를 입력하여 변경사항을 기록한 로그를 확인할 수 있다.(replicas를 4개로 바꾼것 확인 가능)


kubectl describe services/kubernetes-bootcamp
를 입력하여 서비스가 어떤아이피로 제공하고 있는지 확인할 수 있다.

```
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
```
를 입력하여 포트를 확인해보자.

이번엔 아래를 입력하여 스케일 다운해보자 replicas를2개로 바꾸자
kubectl scale deployments/kubernetes-bootcamp --replicas=2

kubectl get deployments
를 입력하여 디폴로이 먼트의 파드 개수를 확인해보자.

kubectl get pods -o wide
를 입력하여 2개의 파드가 종료된것을 확인할 수 있다.


#### Rolling update
유저는 어플리케이션을 항상 사용할수 있도록 원한다. 그리고 개발자는 하루에 여러번 배포할 수 있길 바란다. 이를 쿠버네티스에서 제공한느 롤링 업데이트를 통해 할 수 있다. 이는 디플로이먼트를 제로다운타임으로 점진적으로 파드를 업데이트 할 수 있다. 새파드는 가용한 리소스를 사용하여 새로운 파드가 만들어질 수 있다.

이전에는 여러 인스턴스를 에서 스케일링하는것을 배웠다. 기본적으로, 업데이트가 이루어지는 동안 이용 불가한 파드의 최대 개수와 생성 가능한 새로운 파드의 최대 개수는 하나다. 두 옵션은 기본적으로 업데이트할 때 파드 개수만큼 한번에 꺼버릴수있다. 그리고 최대갯수만큼 파드를 생성할 수있다.(즉 비율을 적용하여 할 수 있다.) 쿠버네티스에서 업데이트는 버전으로 관리되고 어떠한 디플로이먼트 업데이트라도 이전버전으로 원복이 가능하다.


어플리케이션 스케일링과 비슷하게 디플로이먼트가 노출되어있으면 서비는 가용한 파드한테만 보낸다.
롤링업데이트는 다음과 같은 행동을 허용한다.
- 어플리케이션 환경 변경(컨테이너 이미지 변경방법)
- 이전버전으로 롤백
- CI/CD 제로다운타임으로


새버전으로 업데이트 하고 롤백해보자

 kubectl get deployments
 를 입력하여 디폴로이 먼트들을 확인해보자(4개가 떠있다)

 kubectl get pods
 를 입력하여 돌아가고 있는 파드들을 보자

kubectl describe pods
를 입력하여  이미지 버전을 보자

이미지를 version2로 바꾸기 위해 다음을 입력하자

kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2

kubectl get pods
를 입력하면 4개는 종료하고 러닝은 4개인것을 볼 수 있다.
즉 한번에 4개를 죽여버리고 4개를 실행
제로 다운타임이 아니다!!

kubectl describe services/kubernetes-bootcamp
를 입력하여 앱이 돌아가고 있는것과 아이피 포트를 확인하자

```
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
```
를 입력하여 포트 설정 후

```
curl $(minikube ip):$NODE_PORT
```
을 입력하여 잘 돌고있나 보자

kubectl rollout status deployments/kubernetes-bootcamp
를 입력하여 롤 아웃된것을 확인할 수 있다

kubectl describe pods
를 입력하여 현재 이미지 버전을 볼 수 있다.

kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
를 입력하여 이미지를 버전 10으로 바꾸자

kubectl get deployments
를 입력하여 디플로이먼트의 현재상황을 보자
하나가 문제가 생긴것을 확인할 수 있다.

kubectl get pods
를 하면 파드들을 더 자세히 볼 수 있다.

kubectl describe pods
를 입력하여 파드들의 상태를 보자. 이미지가 바뀐지 보자
봤는데 이미지가 바뀌지 않은것을 확인할 수 있다.
레파지토리에 v10 이 없어서 그렇다. 그러므로 롤백하자.

kubectl get pods
를 입력하여 4개가 잘 돌고있는지 보고

kubectl describe pods
를 입력하여 이미지가 잘 돌고있는지 보자
