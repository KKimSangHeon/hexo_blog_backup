---
title: 규칙 5.불필요한 객체는 만들지 말라
date: 2019-03-24 13:06:59
categories:
- Language
- Java
tags:
- Java
thumbnail: /images/effectivejava.png
---


기능적으로 동일한 객체는 매번 만드는것보다 재사용하는 편이 낫다.
가령 String s = new String("test");라는 문장을 반복문안에 넣을경우 매번 String 객체가 생성된다.
이는 String s = "test"; 로 대체하는것이 낫다. 이는 실행할 때마다 객체를 만드는 대신 동일한 String 객체를 사용하며 같은 JVM안에서는 해당 객체를 재사용하게 된다.
생성자와 정적팩터리 메서드를 함께 제공하는 변경 불가능 클래스의 경우 생성자 대신 정적 팩터리 메소드를 이용하면 불필요한 객체 생성을 피할 수 있을 때가 많다. (Boolean(String) 보다는 Boolean.valueOf(String)쪽이 더 바람직하다. 생성자는 호출할 때마다 객체를 만들지만 정적 팩터리 메서드는 그러지 않는다.) 변경 불가능한 객체 뿐 아니라 변경가능한 객체도 재사용할 수 있다.
{% codeblock lang:java default %}
..
public boolean isIn( ){
	Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));

	gmtCal.set(.....);
	Date strart = gmtCal.getTime();

	gmaCal.set(...);
	Date end = gmtCal.getTime();
	...
}
{% endcodeblock%}
와 같은 코드가 존재할 때 isIn메서드는 호출될 때 마다 Caendar, TimeZone,Date객체 두개를 쓸데없이 만들어낸다.
이는 `정적 초기화 블록`을 통해 개선하는것이 좋다.
{% codeblock lang:java default %}
private static final Date START;
private static final Date END;

public boolean isIn( ) {
	static{
		Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
		gmtCal.set(.....);
		START = gmtCal.getTime();

		gmaCal.set(...);
		END = gmtCal.getTime();
	}
}
{% endcodeblock%}

이렇게 할 경우 Caendar, TimeZone,Date <u>객체는 클래스가 초기화 될 때 한번만 만들어진다</u>. 이를통해 성능향상, 코드가 명료해진다(START, END가 상수라는것을 한눈에 알 수 있다)

만약 isIn() 메소드가 호출되지 않는다면 쓸데없는 초기화 과정이 진행된것인데 이는 <u>초기화 지연기법</u>을 사용하면 피할 수있다. 즉 isIn() 메서드가 처음 호출될 때 초기화한다. 이는 구현이 복잡해지고 성능을 개선시키기 어려워 질 수 있는상황을 만들수있다,

`객체를 무작정 만들지 말라는것은 아니고 코드의 명확성, 단순성을 높이고 프로그램의 능력을 향상시킬 수 있을 때 만들라는것!`
객체 풀을 만들어 객체 생성을 피하는 기법 또한 객체생성 비용이 극단적으로 높지않다면 사용하지 말라.(DB접속 할때는 허용) 최신 JVM은 고도로 최적화된 GC를 갖고있으므로 풀보다 월등한 성능을 보여준다.
