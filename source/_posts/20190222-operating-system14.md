---
title: 14.읽기-쓰기 문제, 식사하는 철학자 문제
date: 2019-02-22 22:50:49
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---
읽기-쓰기 문제, 식사하는 철학자 문제

### Reader & Writer
DB 읽기만 하는 프로세스 DB를 바꾸는 프로세스

A가 DB를 읽고 있을 때 B가 읽을 수 있다.
즉 한 프로세스가 DB를 읽을 때 다른 프로세스도 읽을 수 있게 허용해줘야 한다.

The first R/W problem (readers-preference)
리더가 있으면 라이터가 미뤄지는것

The second R/W problem (writers-preference)
라이터가 먼저 우선순위가 주어지는것

The Third R/W problem
아무한테도 주지 않는것

`정리` : 즉 리더가 들어왔는데 라이터가 들어오려면 블록되어야 하고
라이터가 들어왔는데 리더가 들어오려면 블록되어야 하고
리더가 들어와있는데 리더가 들어오면 허용해야한다

### Dining Philosopher Problem

`다음 프로그램 설명`
철학자 5명이 둥근탁상에 앉아있다. 젓가락은 5개뿐. 즉 자신의 왼쪽 오른쪽에 젓가락이 위치.
젓가락을 세마포어로 만들고 초기화를 1로한다.
무한루프를 돌면서 왼쪽젓가락, 오른쪽 젓가락을 acquire 하고. 먹고 다시 왼쪽 오른쪽 젓가락을 release한다.

{% codeblock lang:java  %}
import java.util.concurrent.Semaphore;
class Philosopher extends Thread {
	int id; // philosopher id
	Semaphore lstick, rstick; // left, right chopsticks
	Philosopher(int id, Semaphore lstick, Semaphore rstick) {
		this.id = id;
		this.lstick = lstick;
		this.rstick = rstick;
	}
	public void run() {
		try {
			while (true) {
				lstick.acquire();
				rstick.acquire();
				eating();
				lstick.release();
				rstick.release();
				thinking();
			}
		}catch (InterruptedException e) { }
	}


	void eating() {
		System.out.println("[" + id + "] eating");
	}
	void thinking() {
		System.out.println("[" + id + "] thinking");
	}
}

class Test {
	static final int num = 5; // number of philosphers & chopsticks
	public static void main(String[] args) {
		int i;
		/* chopsticks */
		Semaphore[] stick = new Semaphore[num];
		for (i=0; i<num; i++)
			stick[i] = new Semaphore(1);

		/* philosophers */
		Philosopher[] phil = new Philosopher[num];
		for (i=0; i<num; i++)
			phil[i] = new Philosopher(i, stick[i], stick[(i+1)%num]);

		/* let philosophers eat and think */
		for (i=0; i<num; i++)
			phil[i].start();
	}
}

{% endcodeblock%}

위의 프로그램은 쭉 가다가 정지된다. 그 이유는 <u>기아상태</u> 유발
모든사람이 각자의 왼쪽 젓가락을 들었을 때 해당상황이 발생
이를 <u>교착상태</u>라 한다.

운영체제에서 중요한것은 프로세스 관리. 이중 중요한것이 CPU 스케줄링, 동기화 문제이다.
동기화에서 중요한건 크리티컬 섹션문제, 효율성 증대 즉 ordering 인데 ordering를 잘못하면 <u>교착상태(Deadlock)</u>에 빠지게 된다.

### 교착상태(Deadlocks)
프로세스가 실행되기 위해서는 하드웨어 자원이 필요하다. 이를 리소스 즉 자원이라 한다. 자원을 사용하는 애플리케이션 즉 프로세스가 있고 중간에 OS가 있다. OS는 애플리케이션한테 자원을 잘 나눠줘야 한다. 자원은 한정되어 있는데 이를 사용하고자 하는 프로세스는 많다. 여기서 잘 나눠주지 못하면 교착상태가 발생한다.

어떤 자원은 갖고 있으나 다른 자원은 갖지 못할 때 (e.g., 다른 프로세스가 사용 중) 대기해야 한다.
다른 프로세스 역시 다른 자원을 가지려고 대기할 때 교착상태 가능성 발생

교착상태는 어떤자원을 갖고있을 때 다른 자원을 갖고자 할 때 발생

### 교착상태 필요 조건 (Necessary Conditions)
Mutual exclusion (상호배타) : 리소스가 상호 배타적이다. 즉 한프로세스가 A를 쓰고 있다면 다른 프로세스는 A를 쓰지 못한다.
Hold and wait (보유 및 대기) : 왼쪽 젓가락 잡고있으면서 오른쪽 젓가락을 기다리는것.
No Preemption (비선점) : 다른사람의 젓가락 하나를 강제로 뺏을 수 없다.
Circular wait (환형대기) : 대기하는 형태가 원을 이룸.


### 교착상태
OS에서는 데드락이 일어나지 않도록 미리 조취를 해야한다.

#### 자원의 사용
<u>요청 (request) → 사용 (use) → 반납 </u>
프로세스가 자원을 쓰려면 OS에게 요청을 한다. 그 요청이 올바르면 OS는 그것을 허용하고 프로세스가 자원을 다 쓰면 OS에게 반납한다.


#### 동일 자원
동일 형식 (type) 자원이 여러 개 있을 수 있다 (instance) - 예: 동일 CPU 2개, 동일 프린터 3개 등

### 자원 할당도 (Resource Allocation Graph)
<u>시스템 내에 어느 자원이 있고 어떤 프로세스에게 할당되었는가에 대한 그래프</u>
어떤 자원이 어떤 프로세스에게 할당되었는가?
어떤 프로세스가 어떤 자원을 할당 받으려고 기다리고 있는가?
<u>자원: 사각형, 프로세스: 원, 할당: 화살표</u>


그래프 강의는 37분부터 보자

#### 교착상태 필요조건
자원 할당도 상에 원이 만들어져야 (환형대기) - 충분조건은 아님!
자원에서 프로세스를 가르키면 프로세스가 자원을 할당받은 상태
프로세스에서 자원을 가르키면 자원을 요청한 상태


짝수번 철학자는 왼쪽들고 오른쪽 들고
홀수번 철학자는 오른쪽들고 왼쪽거 들기. 그러면 원형이 깨지게 되어 교착상태가 발생하지 않게된다.
