---
title: 12.세마포를 사용한 프로세스 동기화
date: 2019-02-20 23:16:51
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---

---


### 프로세스 관리에서 중요한것들

CPU 스케줄링 : 이전포스팅 참고

프로세스(쓰레드) 동기화
`필요이유` : 보통컴퓨터 안에있는 프로세스들은 독립적이지 않고 협조적이므로 즉 공통된 자원에 접근함
`동기화가 되지 않으면?` : 누군가는 계속 입금하고 누군가는 출금하는 로직이 있을 때 다 더한값이 0이 아닐 수 있다.
`임계구역` : 공통적으로 사용되는 부분

`임계구역 문제 해결` : 상호베타, 진행, 유한대기 ( 모르겠으면 이전포스팅 보자)
우리가 원하는대로 프로세스 순서를 맞추는것.
###  동기화 툴
`세마포어` : acquire P동작 release V동작이 있음
누가 CPU 서비스를 받다가 acquire를 호출했는데 if문이 만족하면 세마포어 큐안에 잡혀들어간다. 이 후 누가 wake를 시켜주면 다시 레디큐로 들어가게 된다.
세마포어는 Mutual exclusion을 위해 사용한다. 즉 크리티컬 섹션에는 한명만 들어갈 수 있다.

세마포어의 일반적 사용은 <u>Mutual exclusion</u> 그리고 <u>Ordering</u>으로 나뉜다.
그 중 Ordering를 구현하기 위해서는 다음과 같이 하면 된다.

P1	P2
S1	S2

`P1, P2가 있을 때 P1을 먼저 실행하고자 할 때(Ordering)` : 세마포어의 value를 0으로 셋팅한다. 그 후 P2 앞에 sem.acquire를 호출한다.
이러면 P1이 먼저 호출되면 우리가 원하던 대로 되는것이고 그렇지 않고 P2가 먼저 실행될 경우 세마포어 큐에 들어가서 block이 된다. 아무튼 P1이 먼저 실행되게 되고 실행을 완료하면 release를 호출하여 P2가 실행 될 수 있도록 한다.
즉 다음과 같은 형태

P1		P2
		acquire
S1		S2
release


### 세마포어 정리
세마포어는 임계구역 문제 해결, 프로세스 실행 순서 제어를 한다.
