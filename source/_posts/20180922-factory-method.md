---
title: 팩토리 메서드 디자인 패턴
date: 2018-09-22 13:25:51
categories:
- CS
- Design Pattern
tags:
- Design Pattern
thumbnail: /images/design_pattern.png
---
### 팩토리 메서드 패턴
공장처럼 팩토리 메서드의 일은 객체를 생성하는것이다. 이는 생성패턴으로 클래스들 중 하나의 객체를 인스턴스화 하는데 사용된다. 생성자에 비해 많은장점을 갖으며 상황에 따라 생성자 대신 혹은 기존의 생성자에 덧붙여 제공하는것이 좋다.


{% codeblock lang:c Factory %}
// 팩토리 메서드 패턴으로 인스턴스화될 형으로 제공될 기본 클래스
class Pet {
  public :
    virtual void petSound() = 0;
};

// 팩토리 메서드 패턴에 의해 인스턴스를 얻게 될 첫 번째 파생 클래스
class Dog: public Pet{
  public :
    void petSound( ) {
        cout<<"Bow Bow...";
    }
};


// 팩토리 메서드 패턴에 의해 인스턴스를 얻게 될 두번째 파생 클래스
class Cat : public Pet {
  public :
    void petSound() {
      cout<< "Meaw Meaw...";
    }
};

// 로직에 기반해서 객체들을 인스턴스화하는 팩토리 메서드 패턴 구현
class PetFactory {
  public :
    Pet* getPet(int petType) {
      Pet pet = NULL;
      // 비즈니스 로직에 기반한 객체의 인스턴스화
      if (petType == 1)
          pet = new Dog();
      else if(petType ==2)
          pet = new Cat();

      return pet;

    }
}

{% endcodeblock%}
다음은 생성한 팩토리메소드를 활용하여 인스턴스를 생성하는 방식이다.

{% codeblock lang:c Factory %}
int main() {
  PetFactory *PetFactory = new PetFactory();

  Pet pet = petFactory->getPet(2);

  cout<< "Pet Sound";
  pet->petSound();

}
{% endcodeblock%}
