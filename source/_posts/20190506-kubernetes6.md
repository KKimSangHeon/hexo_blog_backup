---
title: (K8s) 6. 윈도우에서 실습해보기
date: 2019-05-06 23:26:48
categories:
- CS
- MSA
tags:
- kubernetes
thumbnail: /images/K8s.png
---


도커툴박스설치


https://docs.docker.com/toolbox/overview/

virtual machine 가 없다면 체크하여 설치


PowerShell 관리자 권한으로 실행

```


$ ExecutionPolicy      <-- 현재상태확인
$ Set-ExecutionPolicy Unrestricted


$ Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

$ choco install minikube kubernetes-cli

$ kubectl run sangheonkim --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080


$ minikube dashboard

```
파드 내부에서 출력하는 로그를 보자
```
$ kubectl get pods
를 통해 파드의 정보를 복사하자,
$ kubectl logs $POD_NAME
```

컨테이너로 들어가서 여러 명령어를 실행해보자
```
$ kubectl exec $POD_NAME env

$ kubectl exec -ti $POD_NAME bash
```

서비스를 만들어보자

```
$ kubectl get services
서비스를 확인해보자. 미니쿠베가 만든게 하나 존재한다.

$ kubectl expose deployment/sangheonkim --type="NodePort" --port 8080
서비스를 노출시켜보자

$ kubectl describe services/sangheonkim
서비스를 자세히 보자

```

Deployment를 자세히보자
```
$ kubectl describe deployments
```
보면 레이블이 있는것을 확인할 수 있는데 디플로이먼트는 저절로 레이블이 하나 생긴다.


레이블을 변경해보자
```
$ kubectl label pod $POD_NAME app=v1
를 입력하여 레이블을 app=v1 으로 변경하고

$ kubectl describe pods $POD_NAME
을 입력하여 확인해보자.

$ kubectl get pods -l app=v1
을 입력하여 잘반영되었나 확인해보자
```
서비스를 삭제해보자
```
$ kubectl delete service -l run=sangheonkim
```
