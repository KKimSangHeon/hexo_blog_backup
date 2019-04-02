---
title: 24.FIFO, OPT, LRU, 전역/지역교체
date: 2019-03-04 08:22:17
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---


메모리 용량이 작을수록 페이지 폴트가 자주일어난다

### Belady's Anomaly
메모리 용량이 늘어나느데도 페이지 폴트가 발생하는 이상한 현상이 발생한다.
언제? FIFO를 사용할때!

### Optimal (OPT)
2를 몰아냈는데 2가 필요한 경우가 생길 수 있다. 즉 억울한 경우
이런일이 안일어나게 하기위해 <u>앞으로 사용되지 않을것을 희생자로 선택한다.</u>
<u>이는 비현실적이다. 앞으로 뭐가 사용안될지 잘 모르는것이다,</u>
SJF와 비슷하다. 이 또한 어떤놈이 제일 짧은지 알 수 없다.

### Least-Recently-Used (LRU)
<u>최근에 가장 적게 사용된놈을 희생양으로 삼는다. 대부분의 컴퓨터가 사용하는 방식.</u>
가장 좋은것은 앞으로 사용안될것을 희생양으로 삼는게 좋겠지만 현실적으로 판단하기 어려움으로,

### Global vs Local Replacement
`Global replacement` - 희생자를 선택할 때 메모리 상의 모든 프로세스 페이지에 대해 교체
`Local replacement`- 희생자를 선택할 때 메모리 상의 자기 프로세스 페이지에 대해 교체

`성능 비교` - Global replacement 가 더 효율적일 수 있다.

---


CPU utilization vs Degree of multiprogramming

CPU utilization - CPU 이용률
Degree of multiprogramming - 메인메모리에 올라와있는 프로세스의 갯수

메모리 안에프로세스를 많이 올리니까 일정범위를 넘어서면 CPU의 이용률이 감소했다.
`왜그럴까?` : 너무 많은 프로세스로 인해 빈번한 page in/out가 발생한다. 즉 디스크 IO가 많아져서 CPU이용율이 떨어진다. 이를 <u>쓰레싱(Thrashing)</u>라고 한다.

`쓰레싱???` : 일정범위를 넘어서면 CPU이용율이 떨어지는데 이범위를 쓰레싱이라 한다.

### 쓰레싱을 극복하기 위한 방법.
Global replacement 보다는 local replacement 활용
프로세스당 충분한/적절한 수의 메모리(프레임) 할당

### 프레임 할당(Allocation of Frames)
<u>프레임 할당은 크게 <u>정적할당 동적할당</u>으로 나뉜다.</u>

#### 정적 할당 (static allocation) 또한 균등할당, 비례할당으로 나뉜다.
`균등 할당 (Equal allocation)` : 메인메모리에 프레임 수가 100개고 프로세스 수가 3개면 각 프레세스에 프레임을 33개씩 할당해주는것. 조금 적절하지 않다. hwp와 메모장이 올라와 있을 때 동일하게 분배한다는것은 옳지않다!! hwp는 조금더 무겁잖아
`비례 할당 (Proportional allocation)` : 사이즈가 큰놈한텐 많이 나눠주고 작은놈한텐 적게 나눠주는것.

사실 hwp에서 특별한 기능은 많이 쓰지 않으므로 정적할당은 올바르지 않다,


#### 동적 할당 (dynamic allocation) 또한 Working set model , Page fault frequency 등으로 나뉜다.
`Working set model ` :
`Page fault frequency` :

다음 포스팅에서....
