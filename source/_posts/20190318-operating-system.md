---
title: 운영체제 핵심!
date: 2019-03-18 22:11:03
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---
운영체제란
부팅
커널, 쉘?
배치프로세싱시스템
멀티프로그래밍 시스템
타임쉐어링 시스템
다중프로세서 시스템
분산시스템
실시간 시스템
인터럽트- 하드웨어인터럽트 , 소프트웨어인터럽트
ISR
이중모드-모니터비트
MMU
운영체제의 매니지먼트들 - 프로세스 메인메모리 파일 보조기억장치 입출력장치
프로세스의 상태-new ready running wating terminated
PCB-상태정보, PC(지금은 몇번지인지. 번지정보), registers, MMU info (base, limit), CPU time(CPU 이용시간), process id, list of open files(얘가 지금 어떤파일을 사용하고 있는지)

job queue , ready queue, device queue
Job scheduling(Long-term scheduler)

CPU Scheduler( Short-term scheduler )

Device Scheduler
Medium-term scheduler
프로세스는 크게 두가지로 나눌 수 있다,
i/o-bound
CPU-bound process

context switching
Dispatcher
CPU스케줄링
Preemptive vs Non-preemptive
Scheduling criteria(척도)
* 응답시간, 대기시간, 반환시간, 처리율,cpu 이용율

fcfs,sjf,Shortest-Remaining-Time-First
Priority,
Round-Robin (RR),
Multilevel Queue,
Multilevel Feedback Queue,

쓰레드
쓰레드 구조
프로세스의 메모리 공간 공유 : (code, data)
프로세스의 자원 공유 : (file, i/o, …)
비공유: 개별적인 PC, SP, registers, stack

프로세스(쓰레드) 동기화
임계영역 해결을 위한 방법 3가지
상호베타 진행 유한대기

동기화
세마포어 모니터(2개의 큐?)
odering 상호베제 용도로 쓰임

교착상태
교착상태 필요조건
상호베타 점유및대기 비선점 환형대기


메모리 낭비방지기법
1.Dynamic Loading
2.Dynamic Linking
3.Swapping

외부단편화
first fit
best fit
worst fit

외부단편화 해결: compaction, 페이징
페이지테이블(mmu의 집합,TLB에 저장)
내부단편화 해결 : 세그멘테이션

페이지, 세그멘테이션의 보호적인 측면의 차이

가상메모리
요구페이징
pure demand paging vs prepaging
Page Replacement Algorithms: FIFO,OPT,LRU
Belady’s Anomaly
Global vs Local Replacement
쓰레싱(Thrashing

프레임 할당은 크게 정적할당 동적할당으로 나뉜다.

정적 할당 (static allocation) 또한 균등할당, 비례할당으로 나뉜다.

block device
character device
연속 할당 (Contiguous Allocation) - 장단점
연결 할당 (Linked Allocation) - 장단점 및 디렉토리 / FAT
색인 할당 (Indexed Allocation) - linked,multi level, combined

디스크 스케줄링
fcfs, sstf, scan scheduling(cscan,look,c-look)
