---
title: Aggregation vs Composition
date: 2020-01-07 19:58:19
tags:
- OOP
categories:
- CS
- OOP
---
다양한 책에서 볼 수 있는 Aggregation, Composition.
같은듯 다른 두개의 차이를 공부해봤다.


### Composition
```
public class Car {
    //final will make sure engine is initialized
    private final Engine engine;  

    public Car(){
       engine  = new Engine();
    }
}

class Engine {
    private String type;
}
```
위와같이 차는 엔진을 갖고있는 것이기 때문에 변수로 엔진을 갖고있다.


### Aggregation

```
public class Organization {
    private List employees;
}


public class Person {
    private String name;   
}
```
조직은 Employees 들의 리스트를 갖고(Composition)있다. 해당 Epmloyees들은 Person들이다. <u> 회사가 없어지더라도 Person 들은 그대로 유지된다.</u>


### 핵심은 다음 두줄이다!

<u>A "owns" B = Composition : B has no meaning or purpose in the system without A</u>
<u>A "uses" B = Aggregation : B exists independently (conceptually) from A</u>


### 연관해서 생각해보면 좋을 주제 (오브젝트 8장)
new 키워드를 사용할 경우 생성자의 모든 인자와 순서를 알아야 하고 인자로 사용되는 구체 클래스에 대해서도 의존성을 늘리게 된다,
그러므로 new 키워드를 사용하기 보다는 setter, 생성자 를 통해 주입받는것이 좋다. (좋은 방법은 생성자로 주입받고 setter로 변경가능토록 하는것)
new를 생성해도 좋을때는 해당클래스가 변경될 확률이 거의 없을 때 이다(ex. ArrayList, Data 클래스 등)


참고 : https://javarevisited.blogspot.com/2014/02/ifference-between-association-vs-composition-vs-aggregation.html
오브젝트 도서
