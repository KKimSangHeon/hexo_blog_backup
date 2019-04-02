---
title: 23.페이지 교체 필요성, 희생 페이지 설정 원칙
date: 2019-02-28 14:39:51
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---


### 가상메모리
프로세스를 페이지 크기로 쪼개서 메인메모리로 올리는데 당장 필요한 것들만 올리고 보자


그러다 메모리가 꽉차게 되면 한놈을 쫒아내고 필요한놈을 불러온다,

### Demand Paging
프로세스 이미지는 backing store 에 저장
프로세스는 페이지의 집합

### 가상메모리를 위한 하드웨어 지원
valid 비트 추가된 페이지 테이블
backing store (= swap device)

### Memory full!
메모리가 가득 차면 추가로 페이지를 가져오기 위해
어떤 페이지는 backing store 로 몰아내고 (page-out)
그 빈 공간으로 페이지를 가져온다 (page-in) -  용어: victim page

### 어느 페이지를 몰아낼 것인가?
i/o 시간 절약을 위해 기왕이면 modify 되지 않은 페이지를 victim 으로 선택
방법: modified bit (= dirty bit)

### Page reference string
CPU 가 내는 주소: 100 101 102 432 612 103 104 611 612
Page size = 100 바이트라면
페이지 번호 = 1 1 1 4 6 1 1 6 6
Page reference string = 1 4 6 1 6

`설명`: fault가 발생하면 100바이트씩 (한페이지씩) 갖고오게 되기 떄문에 100번지를 못찾을경우 100~199까지 갖고오므로 그다음의 101은 fault가 발생하지 않는다. 즉 연속적일 때 앞에서 한번만 갖고오게 한다.

### Page Replacement Algorithms
`FIFO (First-In First-Out)` : 메인메모리에 먼저 올라온놈을 내보낸다.
`OPT (Optimal)` :
`LRU (Least-Recently-Used)`


### FIFO 기법
`Idea`: 초기화 코드는 더 이상 사용되지 않을 것이라는 생각을 바탕으로 만들어짐! 가장 간단하다.

`예제`
페이지 참조열 = 7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1
of frames = 3
15 page faults


### Optimal(OPT)
