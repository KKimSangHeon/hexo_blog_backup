---
title: Resilience4j
date: 2023-10-07 02:11:26
categories:
- CS
- 오픈소스,기술
tags:
- Resilience4j
- CircuitBreaker
---

간략하게 정리하는 Resilience4j

### Resilience4j의 핵심적인 기능들

- CircuitBreaker 
- Bulkhead
- RateLimiter
- Retry
- TimeLimiter
- Cache

### CircuitBreaker 
차단기와 같은것
장애가 다른곳으로 전파되지 않게해줌
HALF_OPEN, CLOSE, OPEN 상태가 있음


### Exception의 종류
`IgnoreException`
서킷의 상태변화를 일으키진 않으나 실패시 fallback메소드가 동작하게 함

`RecordException`
서킷의 상태변화를 일으키고 fallback또한 동작함

`주의점`
유효성검사나 NullPointerException처럼 서킷이 열리는것과 무관한 예외는 RecordException으로 등록하면 안됨
Exception or RuntimeException 처럼 너무 높은 수준의 예외 역시 recordExceptions으로 등록하면 안됨