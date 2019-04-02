---
title: 9.다중 큐 스케쥴링, 프로세스 생성과 소멸, 쓰레드
date: 2019-02-18 22:29:58
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---
### 다중 큐 스케쥴링, 프로세스 생성과 소멸, 쓰레드

### 다양한 CPU 스케줄링 알고리즘
First-Come, First-Served (FCFS)
Shortest-Job-First (SJF)
Priority
Round-Robin (RR)
Multilevel Queue
Multilevel Feedback Queue



### 1. Multilevel Queue Scheduling

### 1.1 Process groups : 프로세스는 그룹별로 묶일 수 있다.

System processes : 가장 긴급하고 먼저 처리되어야 하는것.
Interactive processes : 대화형 프로세스
Interactive editing processes : ex 워드프로세스
Batch processes : 인터액션이 따로 없다.
Student processes

성격이 다양한것을 동일한 큐에 세우는것이 맞지 않다라고 판단.
입출금은 빠르게 끝나는데 대출은 오래 끝나니까!
이를 위해 <u>큐를 여러개를 두자 이를 멀티레벨 큐</u>.

### 1.2 Single ready queue → Several separate queues
각각의 Queue 에 절대적 우선순위 존재
또는 CPU time 을 각 Queue 에 차등배분
각 Queue 는 독립된 scheduling 정책

큐가 여러개이므로 우선순위를 둬서 차등반영한다.
또한 각 큐별로 다른 스케줄링 기법을 사용한다.

### 2. Multilevel Feedback Queue Scheduling
큐를 여러개 둔다는점은 Multilevel Queue Scheduling와 같은데 `하나의 입구로 진입해서 거의 실행되지 않는다면 다른큐로 이동한다.`

### 2.1 다른 Queue 로의 점진적 이동
모든 프로세스는 하나의 입구로 진입
너무 많은 CPU time 사용 시 다른 Queue 로
기아 상태 우려 시 우선순위 높은 Queue 로

상용운영체제는 여러 큐를 갖고있어 다양한 스케줄링 정책을 두어 적절히 활용한다.

### 3. Process Creation
프로세스는 사람과 비슷하다. <u>프로세스는 프로세스에 의해 만들어진다.</u>

`그렇다면 제일 첫번째 프로세스는 어떻게 만들어졌을까??`
O/S가 메모리로 올라가고 초기화 작업을 하면 제일 처음 프로세스를 만든다. 제일 첫번째 프로세스 이름을 유닉스의 경우 init프로세스(OS마다 다름)라 한다. 이 프로세스가 다른 프로세스를 만든다.

### 3.1 Process Identifier (PID)

사람의 주민등록번호와 같은것.
처음 만들어지는 프로그램은 0이다.
PPID? 부모 PID

### 3.2 프로세스 생성
fork() system call - 새로운 프로세스 만드는것.
exec() - 만들어진 프로세스의 실행파일을 메모리로 갖고오는것.


### 3.3 프로세스 종료
exit() system call - 해당 프로세스가 가졌던 모든 자원은 OS에게 반환 (메모리, 파일, 입출력장치 등)

### 4. 쓰레드
<u>프로그램 내부의 흐름, 맥이라고 한다.</u>

### 4.1 다중 쓰레드(Multithreads)
<u>하나의 프로그램에 맥이 2개이상 있는것.</u>
맥이 빠른 시간 간격으로 스위칭 된다 →  여러 맥이 동시에 실행되는 것처럼 보인다
Web browser : 화면 출력하는 쓰레드 + 데이터 읽어오는 쓰레드
Word processor : 화면 출력하는 쓰레드 + 키보드 입력 받는 쓰레드 + 철자/문법오류 확인 쓰레드


이전강의까지는 P1이 끝나면 P2로 넘어간다 했는데 그건 예전의 OS이다.
즉 <u>현재의 컴퓨터는 컨텍스트 스위칭 단위가 프로세스가 아니라 쓰레드 단위이다</u>.

각 쓰레드는 코드와 데이터 공유하고 스택은 공유하지 않는다.

한 프로세스에는 기본 1개의 쓰레드 : `단일 쓰레드 (single thread) 프로그램`
한 프로세스에 여러 개의 쓰레드 : `다중 쓰레드 (multi-thread) 프로그램`

### 4.2 쓰레드 구조
프로세스의 메모리 공간 공유 : (code, data)
프로세스의 자원 공유 : (file, i/o, …)
비공유: 개별적인 PC, SP, registers, stack
