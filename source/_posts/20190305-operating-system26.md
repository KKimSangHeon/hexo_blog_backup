---
title: 26. 파일할당
date: 2019-03-05 22:02:47
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---


OS 프로세스관리 배웠고/ 메인메모리 관리 배웠고/ 파일시스템중 파일 할당에 대해 배운다

OS는 컴퓨터 자원을 관리한다.
CPU: 프로세스 관리 (CPU 스케쥴링, 프로세스 동기화)
주기억장치: 메인 메모리 관리 (페이징, 가상 메모리)
보조기억장치: 파일 시스템 관리

파일이 어떻게 할당되어지는가에 대해 알아보자.
`하드디스크는 구조`: 원판에 자성물질을 바르고 모터를 달아서 돌린다.  데이터는 동심원 상에 저장된다. 또한 헤더가 존재하는데 헤더에는 코일이 감겨있고 여기에 전기를 흘리면 데이터가 기록된다. 데이터를 읽을 때는 판을 빨리 돌리면 판이 지나가면서 전기가 유도되는데 이 값을 통해 데이터를 읽어낸다.
하드디스크에는 트랙이 있고 이를 잘라 섹터라 한다. 실린더는 하나의 하드디스크 안에 여러 판을 넣을 수 있다. 즉 <u>실린더란 같은 거리에 있는 트랙들의 집합<u>을 의미한다. 즉 원통같은것을 의미.

보통 한섹터는 512 bytes이고 섹터를 모아놓은것을 block이라 한다. 하드디스크에 읽고 쓰는것은 블록 단위로 이뤄지는데 이때문에 하드디스크를 <u>block device</u>라고 한다. 이와 반대되는것을 <u>character device</u>라 하는데 대표적인것이 키보드가 있다.

메모장을 켜서 한글자를 입력하고 저장만 하고 속성을 들어가보자, 이때 크기는 1바이트인데 디스크 할당크기는 4바이트이다. 이를 통해 한 블록 크기가 4바이트인것을 알 수 있다.(억울하다,,,) 블록 크기는 운영체제 설계자가 정한다,

디스크는 pool of free blocks 즉 free 블록들의 모음이다. 각각의 파일에 대해 free block을 어떻게 할당해줄까???
연속적으로 할당할 수 도 있고 떨어져있는것들로 할수도있고.. 어떤것이 더 좋을까/???

이는 3가지로 나뉘는데 연속 할당 (Contiguous Allocation) /연결 할당 (Linked Allocation) / 색인 할당 (Indexed Allocation)
이 존재한다. 이에 대해서는 다음포스팅에서 알아보자.
