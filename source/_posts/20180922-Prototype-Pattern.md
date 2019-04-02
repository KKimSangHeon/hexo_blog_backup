---
title: 프로토타입 디자인 패턴
date: 2018-09-22 15:26:34
categories:
- CS
- Design Pattern
tags:
- Design Pattern
thumbnail: /images/design_pattern.png
---
### 프로토타입 디자인 패턴
프로토타입 패턴은 객체를 복사품을 만드는것을 의미하는 것이다. 복제된 객체는 호출되는 객체의 현재 상태를 갖고 초기화 되는데 이는 얕은복사 혹은 깊은복사에 기반하여 이뤄진다.

#### 얕은복사 vs 깊은복사
얕은복사는 단지 참조의 사본을 만든다고 볼 수 있다.
깊은복사는 사본을 새로 생성하여 복사대상이 변경되어도 복사한 데이터는 변경되지 않는다.


{% codeblock lang:c Prototype %}
class Quotation{
  protected:
    string type;
    int value;

  public:
      virtual Quotation* clone() = 0;
      string getType() {
        return type;
      }

      int getValue() {
        return value;        
      }        
};

{% endcodeblock%}
CarQuotation, BikeQuotation 는 Quotation클래스를 상속한다.
{% codeblock lang:c Prototype %}
class CarQuotation: public Quotation {
  public:
   CarQuotation(int number) {
     type = "Car";
     value = number;
  }
  Quotation* clone() {
    return new CarQuotation(*this);
    }
};

class BikeQuotation : public Quotation {
  public:
    BikeQuotation(int number) {
      type = "Bike";
      value = number;      
    }
    Quotation* clone(){
      return new BikeQuotation(*this);
    }

}
{% endcodeblock%}

사용예
{% codeblock lang:c Prototype %}
  class QuotationFactory {
    private :
      Quotation *carQuotation;
      Quotation *bikeQuotation;

    public :
      QuotationFacory() {
        carQuotation = new CarQuotation(10);
        bikeQuotation = new BikeQuotation(20);
      }

      ~QuotationFactory() {
        delete bikeQuotation;
        delete carQuotation;
      }

      Quotation* createQuotation(int typeId) {
        if( typeID ==1 )
          return carQuotation->clone();
        else
          return bikeQuotation->clone();

      }
  }

int main() {
  QuotationFacory* qf = new QuotationFactory();

  Quotation* q;
  q = qf->createQuotation(1);
  delete q;

  q = qf->createQuotation(2);
  delete q;
  delete qf;
  return 0;
}
{% endcodeblock%}

프로토타입 패턴에서 복사한다는 것이 무슨뜻인지 이해가 잘 안됐지만 이젠 이해가 간다. 미리 특정형태(프로토타입)을 만들어 놓고 팩토리패턴으로 호출할 때 그 프로토타입을 반환하여 활용할 수 있도록 하는 패턴인것이다.

#### 자바에서의 프로토타입 패턴
C++같은 경우엔 위와같이 코드를 작성해야 되지만 Java의 경우 최상위 클래스 Object가 clone() 메소드가 정의되어 있으므로 이를 오버라이딩해서 활용하면 된다.
