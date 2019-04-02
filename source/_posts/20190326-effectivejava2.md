---
title: 규칙 8. equals를 재정의 할 때는 일반 규약을 따르라
date: 2019-03-26 22:21:31
categories:
- Language
- Java
tags:
- Java
thumbnail: /images/effectivejava.png
---
equals는 생각보다 재정의 하기 어렵다. 실수로 인한 결과는 끔찍해 질 수 있다.
 다음중 하나라도 만족할 경우에는 equals를 재정의 하지 않아도 된다.
1.각각의 객체가 고유하다.(ex. Thread 객체)
2.클래스에 논리적 동일성 검사방법이 있건 없건 상관없다(ex. Random class)
3.상위클래스에서 재정의한 equals가 하위 클래스에서 사용하기에도 적당하다.(ex. AbstractSet의 equals 메소드)
4.클래스가 private 또는 pakage-private로 선언되었고 , equals 메서드를 호출할 일이 없다.(필자는 실수로 equals를 호출할 수 있는 경우도 있으므로 재정의 해야 한다고 함)

#### equals를 재정의하는게 바람직한 때?
객체 동일성이 아닌 <u>논리적 동일성의 개념을 지원하는 클래스일 때</u>, <u>상위 클래서의 equals가 하위 클래스의 필요를 충족시키지 못할 때</u>이다.
대체로 값 클래스는 두 가지 요건을 충족시키지만 개체 통제 기능을 사용할 때, 열거형일때는 equals 메소드를 재정의 할 필요가 없다.

#### equals 메서드를 재정의 할 때 준수해야 하는 일반규약
메서드는 동치관계를 구현한다. 다음과 같은 관계를 동치 관계라 한다.
`반사성` : null이 아닌 참조 x가 있을 때 x.equals(x)는 true를 반환한다.
`대칭성` : null이 아닌 참조 x,y가 있을 때 x.equals(y)는 y.equals(x)가 true일 때만 true를 반환한다.
`추이성` : null이 아닌 참조 x,y,z가 있을 때 x.equals(y)가 true이고 y.equals(z)가 true이면 x.equals(z)도 true이다.
`일관성` : null이 아닌 참조 x와 y가 있을 때  equals를 통해 비교되는 정보에 아무 변화가 없다면, x.eqauls(y)호출 결과는 항상 같아야 한다.
null이 아닌 참조 x에 대해서 x.equals(null)은 항상 false이다.

컬렉션 클래스를 비롯한 상당수 클래스는 전달되는 객체가 equals 규칙을 따른다고 가정하고 구현되어 있으므로 equals 는 잘 구현해야 한다!
