---
title: 8.SJF, Priority, RR 스케쥴링
date: 2019-02-16 13:17:07
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---

### CPU 스케줄링 알고리즘

<u>CPU 스케줄링은 레디큐에서 프로세스들이 CPU의 작업을 기다리고있을 때 누가 먼저 들어갈것인가에 대한것.</u>

FCFS는 이전포스트 참고

`AWT`를 평균대기시간이라 함.
`ATT`를 Average turnaround time 반환시간이라함.
### SJF
<u>가장 짧은것을 먼저하는것.</u>
P1 6
P2 8
P3 7
P4 3
의 프로세스가있을 때 대기시간.

p4, p1,p3,p2의 순서로 진행된다.
평균 대기시간은 (3+16+9+0)/4 = 7 이다.
FCFS로 하면
(0+6+14+21) /4 = 10.25 이다.

실제로 프로세스가 얼마나 사용할지는 모른다. 해봐야 한다.
대기시간을 줄이기 위해 적절한 방법이긴 한데 CPU 사용시간을 예측을 해야한다.(이를 정확하게 예측하기 위해서는 과거를 기억해야 하는등 계산하는데 오버헤드가 발생한다.)

실제로 적용하기엔 어렵다!

Preemptive or Nonpreemtive 둘다 적용가능하다.

### Priority Scheduling
<u>우선순위가 높은것을 먼저 처리</u>

우선순위는 내부적요소, 외부적 요소로 결정된다.
내부요소: 타임 리밋이 짧은것을 높게주기, 메모리 적게잡는놈 높게주기, IO가 길고 CPU burst가 짧은거 높게주기 등등
외부요소: 돈 많이 낸사람, 정치적요소

일반적으로 많이 사용된다.

이 또한 Preemptive or Nonpreemtive 둘다 적용가능하다.

문제점: 기아상태
우선순위가 낮은 프로세스가 기다리는데 우선순위 높은것들이 자꾸 들어온다면 아무리 기다려도 자기 차례가 오지 않는다.
해결방법 : againg 어떤 프로세스가 오래머물러 있다면 점진적으로 우선순위를 높여준다

### Round Robin
<u>시간을 쪼개서 진행</u>
Time quantum 시간양자 = time slice (보통 10 ~ 100msec으로 잡는)
Preemptive scheduling

타임퀀텀을 얼마로 잡느냐에 따라 AWT가 달라진다.
즉 Time quantum에 의존적이다
Time quantum을 무한대로 주면 FCFS와 동일하다

반대로 Time quantum을 0으로 주면 동시에 도는것과 같이느낄 수있다,
context switching overhead 가 발생함.
