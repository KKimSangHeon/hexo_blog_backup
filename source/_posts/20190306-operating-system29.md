---
title: 29. SCAN 알고리즘 및 변종
date: 2019-03-06 22:40:17
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---
{% asset_path adsense.ejs %}


프로세스 관리(CPU 스케줄링,동기화), 메인메모리 관리(디멘딩 페이지), 파일관리(연속,연결,색인할당)

디스크의 헤더의 움직이는데 오래걸린다. 물론 ms는 느린게 아니지만 컴퓨터의 수준에서 느린것이다.
200개의 실린더가 있을 때 어떻게 조금만 움직일 수 있을까?
맨안에 원이 트랙1 그다음이 2...

다중프로그래밍 환경에서의 디스크 큐(disk queue)에는 많은 요청(request)들이 쌓여있다.
요청들을 어떻게 처리하면 탐색시간을 줄일 수 있을까?

### FCFS (First-Come First-Served)
 온 순서대로 처리해준다.

### SSTF Scheduling
현재위치를 기준으로 헤더를 조금움직이기 위해 위한것.
이는 Starvation문제가 발생할 수 있다. 큐에 줄서있는 순서대로 제공하는것이 아니라 가까운놈 순으로 제공하기 때문에 멀리있는놈은 가까운놈들이 계속 들어올 때 기아상태가 된다..
SSTF가 가장 좋은것인가???? 아니다...

200 cylinder disk, 0 .. 199
Disk queue: 98 183 37 122 14 124 65 67
Head is currently at cylinder 53

SSTF의 경우 Total head movement = 236 cylinders
일 때
최적의 경우: 53 - 37 - … = 208 cyl으로 최적으로 돌릴 수 있다.

### SCAN Scheduling
디스크 헤더가 전체에걸쳐 들어갔다 나왔다 한다.

200 cylinder disk, 0 .. 199
Disk queue: 98 183 37 122 14 124 65 67
Head is currently at cylinder 53 (moving toward 0) - Total head movement = 53+183 cylinders (less time)

디스크 헤더를 끝까지 넣었다가 뺐다가 하는것.
53-37-14-0-65-67-....183
53부터~0 까지 + 0부터 ~183까지

스캔 알고리즘을 적용할 때 방향이 중요하다. 최적은 왼쪽으로 갔다 오른쪽으로 간다. 왼쪽으로 갔다 오른쪽으로 갔다면 걸린 시간이 다르다.

프로세스의 개수가 많으면 골고루 요청이 분포되어 있을것이다.



### SCAN의 변종 1.C-SCAN
53부터~0 까지 처리했으면 53부터 ~183까지 처리하는것이 더욱 효율적일것이다. 이를 Circular SCAN

### SCAN의 변종 2.LOOK
처음 53부터 0에서 제일 가까운 14까지만 가고 헤더를 옮겨 다시 53에서 183까지 가는것.
The head goes only as far as the final request in each direction
Look for a request before continuing to move in a given direction

### SCAN의 변종 3.C-LOOK
처음 53부터 0에서 제일 가까운 14까지만 가고 헤더를 옮겨 183부터 65까지 이동

### 스캔알고리즘을 엘리베이터 알고리즘이라 한다.
올라가면서 서비스하고 내려오면서 쭉 서비스를 제공하기 때문에
