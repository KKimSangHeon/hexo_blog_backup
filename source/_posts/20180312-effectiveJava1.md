---
title: Effective Java
date: 2018-03-12 22:49:17
categories:
- Language
- Java
tags:
- Java

thumbnail: /images/effectivejava.png
---
워낙 유명한 이책을 한번쯤 보고싶다는 생각을 했었고 이제서야 이 책을 읽으며 스스로 정리한 내용을 잊지 않기위해 블로그에 올려 공유하고자 한다.
다음은 이 책을 읽기위해 알아두자.


## 1. 자바는 네 가지 유형의 자료형을 지원한다.
- 인터페이스(어노테이션 포함)
- 클래스(enum 포함)
- 배열
- 기본 자료형(primitive)
여기서 위의 3개는 참조 자료형(reference type)으로 알려져있으며 모든 개체(instance)와 배열은 객체(object)이다. 하지만 기본 자료형은 객체가 아니다. 클래스의 멤버로는 필드(field), 메서드(method), 멤버 클래스(member class), 그리고 멤버 인터페이스(member interface) 등이 있다. 메서드의 시그니처(signature)는 그 이름과 형식 인자 자료형들로 구성된다. 시그니처에는 메서드의 반환값 자료형은 포함되지 않는다.
`메소드 시그니처란 : 메소드의 이름과 매개변수 목록`

## 2. 자바 언어 명세서의 정의를 따르지 않는 것들
- 계승(inheritance)라는 용어를 하위 클래스 정의(subclassing)와 같은 뜻으로 사용한다.
- 인터페이스에는 계승이라는 용어 대신, 클래스가 인터페이스를 구현(implements)한다고 하거나, 한 인터페이스가 다른 인터페이스를 확장(extends)한다고 표현한다.
- 접근권한을 명시하지 않았을 때 가능한 접근 수준을 표현할 때는 기본 접근 권한 (default access)대신 의미가 분명한 package-private라는 용어를 사용한다.

## 3. API 관련
- API를 사용해 프로그램을 작성하려는 프로그래머는 API의 사용자(user)라고 지칭한다.
- 어떤 API를 사용해 구현된 클래스는 API의 클라이언트(client)라고 부른다.
