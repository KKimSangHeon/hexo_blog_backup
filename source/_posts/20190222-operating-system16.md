---
title: 16.프로세스 동기화의 다른 도구인 모니터 사용
date: 2019-02-22 22:51:04
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---


### 모니터
동기화 도구로 세마포어를 배웠다. 세마포어는 조금 오래된 동기화 도구인데 지금은 모니터를 많이쓴다. 주로 자바에서 많이쓴다.
즉 세마포 이후 프로세스 동기화 도구 이며 세마포 보다 고수준 개념

### 세마포어의 구조
정수변수, 큐, P(acquire)동작, V(release)동작

### 모니터의 구조
공유자원 , 공유자원 접근함수 , 2개의 큐

2개의 큐?
하나는 배타동기. 즉 한쓰레드만 공유자원에 접근할 수 있도록 하는 큐
하나는 조건동기를 위한 큐

베타동기를 위한큐 | 다양한 함수 | 조건동기를 위한큐

{% asset_img image1.png 모니터의구조 %}

1.쓰레드 한개만 공유자원 접근 함수에 접근할 수 있다. 즉 들어오고 싶어하는 애들은 베타동기를 위한 큐에서 기다려야 한다.
2.진입 쓰레드가 조건을 만족하면(wait을 호출하여) 조건동기 큐로 들어가는데 이땐 블록된다. 그러면 새 쓰레드가 진입가능
3.새 쓰레드는 조건동기로 블록된 쓰레드를 notify를 호출하여 깨울 수 있다
4.깨워진 쓰레드는 현재 쓰레드가 나가면 재진입할 수 있다.
(하나의 쓰레드만 접근 가능하므로)

세마포어에 비해 복잡하나 사용하기 편리하다.

`배타동기`: synchronized 키워드 사용하여 지정
`조건동기`: wait(), notify(), notifyAll() 메소드 사용

베타동기: 하나만 특정함수에 들어갈 수 있다. 자바에서는 함수 정의 앞에 synchronized 키워드를 붙인다.
조건동기는 wait 함수를 호출하여 조건동기를 위한 큐에 갇히게 할 수 있고 notify(하나깨우기) 혹은 notify all(전체깨우기) 을 호출하여 깨울 수 도 있다.

#### 상호배제 용도로 사용
{% codeblock lang:java  %}
class BankAccount {
	int balance;
	synchronized void deposit(int amt) {
		int temp = balance + amt;
		System.out.print("+");
		balance = temp;
	}
	synchronized void withdraw(int amt) {
		int temp = balance - amt;
		System.out.print("-");
		balance = temp;
	}
	int getBalance() {
		return balance;
	}
}

{% endcodeblock%}

위처럼 하면 동기화가 가능하다. 세마포어 보다 쉽다!

#### Ordering 용도로 사용
P1은 공백/ S1/notify();
P2는 wait/S2/공백

P1이 입금, P2가 출금 로직이라고 가정하고 생각해보자.


P1,P2 반복해서 출력되게 하려면 boolean 변수 하나를 둬서 처리
{% codeblock lang:java  %}


int balacne = 0;
boolean p_turn;
synchronized void deposit(int amt) {
	int temp = balance + amt;
	balance = temp;
	notify();
	p_turn = false;
	wait();
}

synchronized void withdraw(int amt) {
	while(p_turn)
		wait();

	int temp = balance - amt;
	balance = temp;
	p_turn = true;
	notify();
}


---
class Buffer {
	int[] buf;
	int size, count, in, out;
	Buffer(int size) {
		buf = new int[size];
		this.size = size;
		count = in = out = 0;
}

synchronized void insert(int item) {
	while (count == size)
	try {
		wait();
	} catch (InterruptedException e) {}
	buf[in] = item;
	in = (in+1)%size;
	notify();
	count++;
}


synchronized int remove() {
	while (count == 0)
	try {
		wait();
	} catch (InterruptedException e) {}
	int item = buf[out];
	out = (out+1)%size;
	count--;
	notify();
	return item;
	}
}

---
교착상태에 대한 고려는 되지 않음.

class Chopstick {
	private boolean inUse = false;
	synchronized void acquire() throws InterruptedException {
		while (inUse)
		wait();
		inUse = true;
	}

	synchronized void release() {
		inUse = false;
		notify();
	}
}

{% endcodeblock%}
