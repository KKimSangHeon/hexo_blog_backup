---
title: 17.주기억장치 관리 개요
date: 2019-02-23 09:48:52
tags:
categories:
- CS
- Operating System
thumbnail: /images/os.png
---
CPU / 메모리 / 보조기억장치가 존재.
O/S는 프로세스 관리를한다. CPU 관리를 하는데 이를 프로세스 매니지먼트라함.
또한 O/S는 메모리 관리 즉 메모리 매니지먼트를 한다.

OS : 하드웨어를 어플리케이션이 활용할 수 있도록 함.
`CPU 자원을 나눠주는 곳` : 프로세스 매니지먼트
`메인메모리를 관리하는곳` : 메모리 매니지먼트.

### 메모리 역사
`Core memory` : 반지모양의 철심에 자성물질을 바르고 전기를 흐르게 한 후 자석이 되게 한 후 메모리로 사용.
`진공관 메모리` : 50~60년대 손가락 크기만함. 네다섯개가 한비트를 저장
`트랜지스터 메모리` : 손톱만한 크기로 한비트를 저장하는데 네개의 트랜지스터 필. 반도체 소자안에 들어간다.
`집적회로 메모리: SRAM, DRAM` : 보통 생각하는 메모리

### 메모리 용량
1970년대: 8-bit PC 64KB
1980년: 16-bit IBM-PC 640KB > 1MB > 4MB
1990년: 수MB > 수십 MB
2000년~: 수백 MB > 수 GB

메모리가 지속적으로 커졌다!!
---
### 메모리는 언제나 부족하다..
#### 이유 : 프로그램의 변천
기계어/어셈블리어 작성
C언어
자바, 객체지향형 언어 작성
숫자 처리 > 문자 처리 > 멀티미디어 순으로 패러다임이 변화함.


#### 메모리 용량 증가 vs 프로그램 크기 증가
언제나 부족한 메모리 ㅠㅠ

#### 어떻게 메모리를 효과적으로 사용할 수 있을까?
메모리 낭비를 없애고 가상메모리와 같은 기법을 활용!

### 메모리 구조?
{% asset_img image1.png CPU와 메모리 %}
CPU가 몇번지를 읽겠다고 주소를 메모리로 보낸다. 그러면 거기에 저장되어있는 정보를 메모리가 CPU에게 제공한다.
또한 데이터 저장을 위해서 CPU가 데이터를 전송하기도 한다
메모리는 주소(Address) + 데이터(Data)로 구성된다.

### 프로그램은 어떻게 개발될까?
컴파일러, 어셈블러, 링커, 로더에 대해 알아보자
프로그램이 실행되기까진 아래의 순서를 따른다.
<u>main.c (고수준언어)->main.o(기계어) -> link -> 메인메모리로 올림</u>
첫번째 화살표 컴파일러가 진행
두번째 화살표 링커가 진행
세번째 화살표 로더가 진행

`원천파일 (Source file)`: 고수준언어 또는 어셈블리언어
`목적파일 (Object file)`: 컴파일 또는 어셈블 결과
`실행파일 (Executable file)`: 링크 결과
프로그램 실행을 위해 필요한것들: code + data + stack

실행파일을 메모리에 올리기 위해 메모리 몇번지에 올려야되지?(로더가 해결해준다)

### MMU의 사용
MMU 안에 base limit가 있다했는데 재배치 레지스터가 있다. 프로그램을 개발할 때 0번지에 들어간다고 생각했는데 실제로는 500번지에 들어간다면 Relocation register에 500을 넣어줘 CPU가 봤을땐 0번지 이지만 500을 더한 500번지에 저장되게 한다. 즉 CPU는 얘가 0번지에 있는줄 안다.
