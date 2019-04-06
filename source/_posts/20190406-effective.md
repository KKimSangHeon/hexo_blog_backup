---
title: 규칙 14. public 클래스 안에는 public 필드를 두지 말고 접근자 메서드를 사용하라.
date: 2019-04-06 13:37:42
categories:
- Language
- Java
tags:
- Java
thumbnail: /images/effectivejava.png
---


class Point {
	public double x;
	public double y;
}
데이터 필드를 직접 조작할 수 있어서 캡슐화의 이점을 누릴 수 없다. API를 변경하지 않고서는 내부 표현을 변경할 수 없고, 불변식도 강제할 수 없고, 필드를 사용하는 순간에 어떤 동작이 실행되도록 만들수도 없다. <u>이는 private 필드와 public 접근자 메서드로 바꿔야 한다.</u>

public 클래스의 데이터 필드를 공개하게 되면, 그 내부 표현을 변경할 수 없게 된다. 변경하면 이미 작성된 클라이언트 코드를 깨뜨리게 되게 때문이다. 하지만 package-private 클래스나 priavte 중첩 클래스는 데이터 필드를 공개하더라도 잘못이라 말할 수 없다.

#### 요약
public 클래스는 변경 가능 필드를 외부로 공개하면 안된다. 변경 불가능 필드인 경우에는 외부로 공개하더라도 많이 위험하진 않지만, 그럴 필요가 있을까? package-private나 private로 선언된 중첩 클래스의 필드는 그 변경 가능 여부와는 상관없이 외부로 공개하는것이 바람직할 때도 있다.
