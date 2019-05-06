---
title: (K8s) 4. 서비스를 사용하여 앱을 외부로 공개하기 , 서비스 / 레이블
date: 2019-04-23 21:08:01
categories:
- CS
- MSA
tags:
- kubernetes
thumbnail: /images/K8s.png
---
#### 서비스를 사용하여 앱을 외부로 공개하기
https://kubernetes.io/ko/docs/tutorials/kubernetes-basics/expose/expose-intro/

지금까지의 앱은 컨테이너 안에서 도는데 private하고 네트웍도 isolate되어있었다. 그러므로 proxy 를 붙여서 동작했었다

#### 쿠버네티스 서비스에 대한 개요

파드는 언젠가 죽는것이다. 즉 라이프사이클이 있다. 어떤 워커노드가 죽으면 그 노드에서 돌고있는 파드도 죽는다.
 그렇게되면 ReplicationController 가 클러스터를 동적으로 새로운노드를 만듬으로서 복구한다. 예를들어 이미지 처리하는 백엔드가 3개의 어플리케이션을 갖고있다 해보자. 프론트엔드는 백엔드 파드가 없어지는것 생기는것에 대해 신경쓰지 말고 동작하여야 한다. 각 파드는 유니크한 아이피를 갖고있는데 애플리케이션이 계속해서 동작할 수 있도록 발생하는 변화들을 서로 감지하는 방법이 필요하다.

### 서비스
쿠버네티스에 있는 서비스는 파드의 set을 논리적으로 정의 한것이고 그 파드들에게 어떻게 접근할 수 있는지에 대한 정책을 정의한것이다.
서비스는 파드들간에 커플링을 낮춘다. 서비스는 모든 쿠버네티스 오브젝트들과 같이 YAML (보다 선호하는) 또는 JSON을 이용하여 정의되고. 서비스가 대상으로 하는 파드 셋은 보통 LabelSelector에 의해 결정된다


#### 서비스 외부로 노출하기
비록 각각의 파드가 제각각의 IP를 갖고있지만 서비스 없이는 클러스터 바깥으로 노출되지 않는다. 서비스는 어플리케이션이 트래픽을 받게할 수 있는데 ServiceSpec에 type을 지정하여 노출 할 수 있다.

#### ServiceSpec의 type
- ClusterIP (기본값) - 클러스터 내에서 내부 IP 에 대해 서비스를 노출해준다. 이 방식은 오직 클러스터 내에서만 서비스가 접근될 수 있도록 해준다.
- NodePort - NAT가 이용되는 클러스터 내에서 각각 선택된 노드들의 동일한 포트에 서비스를 노출시켜준다. <NodeIP>:<NodePort>를 이용하여 클러스터 외부로부터 서비스가 접근할 수 있도록 해준다. CluserIP의 상위 집합이다.
- LoadBalancer - (지원 가능한 경우) 기존 클라우드에서 외부용 로드밸런서를 생성하고 서비스에 고정된 공인 IP를 할당해준다. NodePort의 상위 집합이다.
- ExternalName - 프록시를 사용하지 않고 임의의 Name을 사용해서 expose 하는방법으로 externalName을 스펙에다 정의함(YAML 혹은 JSON)  kube-dns 1.7 이상 요구됨

추가적으로 셀렉터를 지정하지 않는경우도 있는데 셀렉터를 지정하지 않은것은 엔드포인트를 오브젝트를 만들지 않을것이다. 그렇게 함으로써 유저들로 하여금 직접 서비스를 특정엔드포인트에 매핑할 수 있도록 해준다. selector를 생략하게 되는 또 다른 가능성은 여러분이 type: ExternalName을 이용하겠다고 확고하게 의도하는 경우이다.

#### 서비스와 레이블
서비스는 파드들의 묶음인데 하나의 ip로 묶인다. 하나의 아이피는 외부에 노출되어 서비스안에 존재하는 파드들에 접근할 수 있게해준다. 서비스는 파드셋에다가 트래픽을 라우팅(쿠버네티스 서비스들에 의해 처리된다.)하는데 서비스는 파드가 애플리케이션에 영향을 주지않고 혼자 죽거나 살아날 수 있도록 한다.

파드 셋을 label이랑 selector을 사용하여 매치한다. label은 key / value 쌍이며 오브젝트에 붙어있고 여러가지 방식으로 사용될 수 있다.

- 개발, 테스트, 그리고 상용환경에 대한 객체들의 지정
- 임베디드된 버전 태그들
- 태그들을 이용하는 객체들에 대한 분류

레이블은 오브젝트가 만들어 질때 붙일수도 있고 나중에 붙일수있고 아무때나 수정할 수 있다.

kubectl get pods
를 입력하고 파드들을 확인해보자.

kubectl get services
를 입력하여 서비스를 확인해보자. 만들지도 않았는데 하나가 돌아가고 있는데 클러스터에서 미니큐브를 만들면 실행되는것이다.

새로운 서비스를 만들고 노출하려면 다음과 노트 포트를 파라미터로 줘야한다.
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
내부 8080포트에 붙으라는것.

kubectl get services
를 입력하여 서비스를 확인해보자.
하나가 더 추가가 된것을 알 수 있다.

서비스가 클러스터ip로 인터널, external 포트가 있다.

kubectl describe services/kubernetes-bootcamp
를 입력하여 자세히 살펴보자.

노드포트라는 환경변수를 만들자
```
export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')

echo NODE_PORT=$NODE_PORT
```
아래를 입력하여 접근해보자
```
curl $(minikube ip):$NODE_PORT
```
디플로이먼트는 자동으로 레이블이 하나있다.
이를통해 파드리스트를 쿼리해보자.
kubectl get pods -l run=kubernetes-bootcamp
run=kubernetes-bootcamp이 label이다.

kubectl get services -l run=kubernetes-bootcamp
를 입력하여 run=kubernetes-bootcamp 이라는 레이블을 갖고있는 서비스를 조회해보자.

```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
```
를 입력하여 파드 네임을 환경변수에 넣어보자.

app=v1으로 새로운이름을 주자.
kubectl label pod $POD_NAME app=v1

kubectl describe pods $POD_NAME
레이블 확인해보자.


kubectl get pods -l app=v1
바뀐 레이블로 파드를 조회해보자.

kubectl delete service -l run=kubernetes-bootcamp
를 입력하여 서비스를 삭제하자.


kubectl get services
서비스를 확인해보자.

라우팅도 잘 안되나 확인해보자.
```
curl $(minikube ip):$NODE_PORT
```

파드안에서 동작하는 아래의 명령어를 통해 아직 파드가 동작는지 보자.
kubectl exec -ti $POD_NAME curl localhost:8080
