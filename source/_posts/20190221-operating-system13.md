---
title: 13.전통적 동기화 문제, 생산자-소비자 문제
date: 2019-02-21 22:10:14
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---


OS에서 가장중요한게 프로세스 관리
이중 CPU 스케줄링, 프로세스 동기화가 중요하다.
이때 임계구역 처리를 잘해야한다.

### 전통적 동기화 예제
1.Producer and Consumer Problem(생산자-소비자 문제, 유한버퍼 문제 (Bounded Buffer Problem))
2.Readers-Writers Problem(공유 데이터베이스 접근)
3.Dining Philosopher Problem(식사하는 철학자 문제)

---

### Producer-Consumer Problem
#### 생산자-소비자 문제
생산자가 데이터를 생산하면 소비자는 그것을 소비
예: 컴파일러 > 어셈블러, 파일 서버 > 클라이언트, 웹 서버 > 웹 클라이언트


#### Bounded Buffer
보통 생산소비와 소비속도는 다르다.
그래서 보통 창고(버퍼)에 저장하고 컨슈머가 조금씩 빼먹는다.

생산된 데이터는 버퍼에 일단 저장 (속도 차이 등)
현실 시스템에서 버퍼 크기는 유한
생산자는 버퍼가 가득 차면 더 넣을 수 없다.
소비자는 버퍼가 비면 뺄 수 없다

버퍼가 가득차면 생산자는 버퍼가 비워질때까지 기다려야한다.
버퍼가 비면 소비자는 빼갈것이 없다.

생산자가 10000번 넣고 소비자가 10000번 넣으면 0이되지 않을 수 있다.
`이유` : 공통변수 count, buf[] 에 대한 동시 업데이트  
공통변수 업데이트 구간(= 임계구역)에 대한 동시 진입

`해결법` : 임계구역에 대한 동시 접근 방지 (상호배타)
세마포를 사용한 상호배타 (mutual exclusion, mutex)


### Busy-wait
바쁘게 기다린다.
생산자: 버퍼가 가득 차면 기다려야 == 빈(empty) 공간이 있어야 한다
소비자: 버퍼가 비면 기다려야 == 찬(full) 공간이 있어야 한다

이때 무한루프를 돌면서 기다리면 CPU는 다른일을 못하고 잡히게 된다. -> 비효율적
`해결법` : 두개의 세마포어를 사용. empty의 초깃값은 BUF_SIZE 만큼 full의 초깃값은 0으로 하여 사용
생산자 :
empty.acquire();
PRODUCE;
full.release();

소비자 :
full.acquire();
CONSUME;
empty.release();
---
