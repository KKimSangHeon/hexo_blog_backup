---
title: Spring Reactive 
date: 2023-10-07 04:45:42
categories:
- Web/App
- Spring
tags:
- Spring Security
thumbnail: /images/spring.jpg
---


### 리액티브 선언문 
https://www.reactivemanifesto.org

### 위에서 말하는 리액티브 시스템의 특징 4가지
Responsive - 응답성
Resilient - 유연성
Elastic - 탄력성
Message Driven

### 리액티브 스트림즈(Reactive Streams)
https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.3/README.md#specification

Reactive Streams에선 4가지 명세가 존재하며 이를 구현한 구현체는 RxJava, Java 9 Flow API, Akka Streams, Reactor, RxJS, RxScala, RxAndroid  등이 존재한다.
- Publisher
- Subscriber
- Subscription
- Processor

### Spring WebFlux를 사용하며 주의해야 할 점 및 특징
하나의 Thread로 대량의 요청을 처리할 수 있다.
복잡한 계산은 다른 요청들을 처리할 수 없도록 만드므로 지양하고, 다른시스템에 위임하도록 하자(WebClient 등 활용)
중간에 Non Blocking가 끼어있으면 진정한 Non Blocking효과를 보기 어렵다

Spring MVC 

### Spring WebFlux ? 
Spring 5부터 지원하는 리액티브 웹 프레임워크
리퀘스트당 하나의 스레드를 하용하는 Spring MVC와 다르게 적은수의 스레드를 사용한다.
Reactive Streams의 구현체 중 하나인 Reactor에 의존하여 비동기 로직을 구성함(이 Reactor의 학습비용이 많이듦)
Reactor기반이지만 RxJava 등 다른 리액티브 확장 라이브러리를 쉽게 적용가능

기본 서버엔진이 비동기 non blocking를 지원하는 Netty를 사용함.(jetty, undertow로 변경가능)

### 이벤트루프를 통한 Spring WebFlux의 Non-Blocking 프로세스
CPU 코어 내부에서 아래의 동작들이 비동기적으로 발생
클라이언트 리퀘스트 -> 요청 핸들러 -> 이벤트루프로 요청이벤트 푸쉬 -> 콜백등록 -> 이벤트 루프로 작업완료 알림 -> 콜백호출하며 요청핸들러로 응답 -> 클라이언트에게 응답

### Spring WebFlux는 언제 적용하나
Blocking I/O방식으로 처리하는데 한계가 있는 대량의 트래픽이 발생할 때
->Blocking I/O방식은 하나의 리퀘스트에 하나의 스레드를 사용하기에 대량의 리퀘스트가 발생할때 스레드를 무한정 늘리기 어렵기때문에

MSA
-> 많은 수의 IO가 발생하기 때문에

스트리밍 시스템 또는 실시간 시스템.
-> 많은 데이터 스트림을 잘 처리하기 위해

네트워크 접속이 느린 클라이언트의 요청 처리
-> 스레드를 블로킹하지 않기때문에 접속이 느린 클라이언트가 접속하더라도 스레드가 블로킹되지 않고 다른 요청을 처리가능

### Reactor?
리액티브 프로그래밍을 위한 리액티브 라이브러리로 Reactive Streams 스펙을 구현한 구현체 중 하나임. Spring 에코 시스템에서 Reactive Stack의 기반이 되며 Spring WebFlux프레임워크에 포함되어있다.

### 용어정리
`Publisher` : 발행자, 게시자, 생산자, 방출자(Emitter)
Flux, Mono가 존재. 데이터를 생성하는 주체

`Subscriber` : 구독자, 소비자
`Emit` : Publisher가 데이터를 내보내는 것(방출하다. 내보내다. 통지하다.)
`Sequence` : Publisher가 emit하는 데이터의 연속적인 흐름을 정의 해 놓은 것. Operator 체인형태로 정의 된다.
`Subscribe` : Subscriber가 Sequence를 구독하는 것. 오퍼레이터 체인을 정의해 놓은것
`Subscriber`: subscribe()메소드가 Subscriber가 아니라 파라미터로 넘어가는 람다가 subscriber이다
`Dispose` : Subscriber가 Sequence 구독을 해지 하는 것
`Operator` : just, map 등 연산자를 의미. 데이터를 가공함

#### Mono
Mono : 0개 또는 1개의 데이터를 emit하는 Publisher이다.
Mono는 emit을 종료하면 onComplete signal을 emit하고, 비정상적으로 종료 될 경우 onError signal을 emit한다

#### Flux
Flux : 0개 ~ N개의 데이터를 emit하는 Publisher이다.
Flux는 emit을 종료하면 onComplete signal을 emit하고, 비정상적으로 종료 될 경우 onError signal을 emit한다


### Publish가 만들어 내는 Sequence
React에선 Publish가 만들어 내는걸 Sequence라 한다.

Sequence는 Cold Sequence, Hot Sequence가 존재하며 이는 각각 Cold Publisher, Hot Publisher가 만들어낸다.

`Cold Sequence`
Cold Publisher가 emit 한 데이터를 Subscriber가 구독할 때마다 타임라인의 처음부터 emit 된 모든 데이터를 받을 수 있다.
즉 Sequence timeline이 구독을 할 때마다 하나씩 더 생긴다.
ex.Subscriber A, B의 구독 시점이 발행 이후라도 데이터는 처음부터 받을 수 있다.

`Hot Sequence`
구독 이전시점에 emit 된 데이터는 전달받지 못한다
즉 Subscriber가 구독한 시점의 타임라인부터 emit된 데이터를 받을 수 있다.
즉 구독이 얼마나 발생되던지 타임라인은 하나만 생성된다.

### Back pressure 백프레셔
puvlisher가 sub로 데이터를 보낼때 sub가 안정적으로 데이터를 받기위해하는 제어기능
즉, 들어오는 데이터를 적절히 제한해서 과부하 방지

Back pressur의 여러가지 방법 
`1. 요청데이터의 개수를 제어하는 방법`
sub가 처리할 수 있는 수준의 개수를 pub에 요청

`2. 백프레셔 전략 사용`
2.1 Ignore 전략. 즉 적용하지 않는다
2.2 Error 전략. 다운스트림으로 전달할 데이터가 버퍼에 가득차면 익셉션을 발생시킨다
2.3 Drop 전략. 다운스트림으로 전달할 데이터가 버퍼에 가득처면 버퍼 밖에서 대기하는 먼저 emit된 데이터부터 드랍
2.4 Latest전략. 더운스트림으로 전달할 데이터가 버퍼에 가득차면 버퍼 밖에서 대기하는 가장 최근에 emit된 데이터부터 버퍼에 태우는 전략
2.5 Buffer전략. 다운스트림으로 전달할 데이터가 버퍼에 가득찰경우 버퍼안에 있는 데이터를 드랍시키는 전략

`3. 버퍼 전략`
3.1 Buffer Drop latest 전략
버퍼가 가득찼을 때 가장 최근에 들어온 데이터를 드랍. 버퍼가 크기가 2일 때 1,2가 존재한다면 3이 들어오면 3이 드랍됨. 이후 1이 subscriber에게 emit 되면 데이터가 드랍되지 않고 버퍼로 들어온다

3.2 Buffer Drop Oldest 전략
버퍼가 가득찼을 때 가장 먼저 들어온 데이터를 드랍(버퍼에서 가장 오래된 데이터)

### sink - thread safe함
mono flux는 onNext onComplete 시그널을 내부적으로 발생시키기때문에 프로그래밍 코드에는 이러한것들이 보이지 않는다. 즉 내부적으로 처리하기 때문.
sink는 프로그래밍적으로 이러한 시그널을 푸쉬할수 있는 기능을 갖고있는 퍼블리셔의 한 종류임

processor 는 퍼블리셔 subscriber의 특징을 동시에 갖고있음(processor는 리액트 3.5.0부터 제거될예정)
processor는 멀티스레드 환경에서 스레드세이프 하지 않기때문에 쓰레드 세이프한sink가 프로세서보다 더 나은 대안이 된다.(프로세서는 onNext, onComplete, onError를 직접적으로 호출하기 때문에 쓰레드 세이프 하지 않을 수 있다.sink는 동시접근을 감지하고 그중 하나가 빠르게 실패하기 때문에 쓰레드 세이프를 보장함)
sinks는 sink.many 또는 sink.one 인터페이스를 사용해서 스레드세이프하게 시그널을 발생시킨다

### 프로그래밍 적으로 데이터를 emit하는 방법(create(), generate()) 그리고 Sink와 차이

`create() Sink와 차이점` : 

ex>Flux.create(~~~)
create() 메소드 내부에서만 데이터를 emit할 수 있다.

Sink객체를 이용해서 외부, 내부에서 데이터를 emit 할 수 있다. 또한 EmitFailureHandler를 제공해서 멀티 스레드 환경에서 빠르게 실패할 수 있는 환경을 제공하며 쓰레드 세이프하게 시그널을 발생시킬 수 있다 

`generate()`


### 스케줄러

스케줄러역시 스레드관리자의 역할을 한다 구독시점에 데이터가 emit되는 영역과 emit 된 데이터를 operator로 가공하는 영역을 분리해서 손쉽게 멀티스레딩을 가능하게 함

오퍼레이터 체인에서 스케줄러를 전환하는 역할하는 전용 오퍼레이터

`publishOn()`: operator 체인에서 다운스트림 오퍼레이터 실행을 위한 스레드를 지정
`subscribeOn()`: 최상위 업스트림 퍼블리셔의의 실행을 위한 스레드를 지정한다. 즉 원본데이터 소스를 emit하기 위한 스케줄러를 지정한다

스케줄러를 통해 생성되는 스레드 실행모델을 지정하는 부분


### Context
ThreadLocal처럼 Reactor Sequence 상에서 상태를 저장하고 Operation체인에서 공유해서 사용할 수 있는 인터페이스이다.

Context에 값을 저장하는 메소드: contextWrite( )
데이터 소스레벨에서 접근하기 위한 메소드: deferContextual( )
오퍼레이터 중간에서 접근하기 위한 메소드: transformDeferredContextual( )

`자주사용되는 Context API - 저장 할 때`
put(key, value)
Context.of(key1, value2, key2, value2 ...) : key/value형태로 Context에 여러개의 값을 쓴다
putAll(ContextView)  : 파라미터로 입력된 ContextView를 합친다
delete(key)

`자주사용되는 Context API - 데이터를 읽어올 때`
get(key)
getOrEmpty(key) : value를 Opetional로 래핑해서 반환
getOrDefault(key, default value) : ContextView에서 key에 해당하는 value를 가져온다. key에 해당하는 value가 없으면 default value를 가져온다
hasKey(key) 
isEmpty() : Context가 비어있는지 확인
size()


### context 특징
컨텍스트는 구독시점에 subscriber를 통해 reactor sequence에 연결된다.또한 체인에서 각각의 Operator들이 실행 쓰레드가 달라도 연결된 Context에 접근할 수 있으며 subscriber 마다 context가 연결된다.


Context는 체인의 맨 아래에서부터 위로 전파되므로 Operator 체인에서 Context read 메서드가 Context write 메서드 밑에 있을 경우에는 write된 값을 read할 수 없다. 따라서 일반적으로 Context에 write 할때에는 Operator 체인의 마지막에 둔다.
메인 Operator 내부에서 Sequence를 생성하는 flatMap( ) 같은 Operator내에서 write 된 Context의 값은 Inner Sequence 내부에서만 유효하고, 외부 Operator 체인에서는 보이지 않는다.


