---
title: 2장. 규칙1 생성자 대신 정적 팩터리 메서드를 사용할 수 없는지 생각해보자.
date: 2018-03-14 07:53:39
categories:
- Language
- Java
tags:
- Java
thumbnail: /images/effectivejava.png
---
## 규칙 1. 생성자 대신 정적 팩터리 메서드를 사용할 수 없는지 생각해보자.
프로그래머가 알아야하는 또다른 객체생성 방법은 클래스에 public로 선언된 팩토리 메서드를 추가하는 것이다.
### 이러한 방법의 장점은 다음과 같다.
1.생성자와 달리 정적 팩토리 메소드에는 이름이 있다. (가독성 향상)

2.호출할 때마다 새로운 객체를 생성할 필요 없다.(객체생성 비용이 클 때 효율적, 개체 통제 클래스로의 역할)
`개체 통제 클래스(instance-controlled class) : 어떤 시점에 어떤 객체가 얼마나 존재할지 제어가능한 클래스. 이를 통해 싱글턴 패턴을 따르도록 할수 있고, 객체생성이 불가능한 클래스를 만들 수 도 있다. 변경이 불가능한 클래스의 경우 두개의 같은 객체가 존재하지 못하도록 할 수 있다.`

3.생성자와 달리 반환값 자료형의 하위 자료형 객체를 반환할 수 있다. (반환되는 객체의 클래스를 유연하게 결정 가능. EnumSet이 그 예시)
이를 통해 public으로 선언되지 않은 클래스의 객체를 반환하는 API를 만들 수 있다.

4.형인자 자료형(parameterized type) 객체를 만들 때 편하다.(자바 1.7부터는 자료형 유추가 가능해졌다 14p)
즉
{% codeblock lang:java %}
public static <K,V> HashMap<K, V> newInstance() {
	return new HashMap<K,V>();
}
{% endcodeblock%}
newInstance() 메소드를 통해 Map<String, List<String>> m = new HashMap<String, List<String>>();
대신 Map<String, List<String>> m = HashMap.getInstance(); 가 가능해짐
(자바 1.7 부터는 Map<String, List<String>> myMap = new HashMap<>();를 지원하긴 함)

### 단점은 다음과 같다.
1. public이나 protected로 선언된 생성자가 없으므로 하위 클래스를 만들 수 없다는 것이다.
2. 정적 팩터리 메서드가 다른 정적 메서드와 확연히 구분되지 않는다는 것이다.
보통 정적 팩토리 메소드의 이름으로는 다음과같은 이름을 쓴다.
valueOf , of , getInstance , newInstance , getType , newType
