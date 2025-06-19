

# CH.9 유연한 설계

8장에 이야기 한 내용들을 원칙이라는 관점에서 정리한 단원

## 개방-폐쇄 원칙(=런타임 의존성과 컴파일타임 의존성)
소프트웨어 개체(클래스, 모듈, 함수 등등)은 확장에 대해 열려있어야 하고, 수정에 대해서는 닫혀 있어야 한다. 

→ **컴파일타임 의존성**을 고정시키고 **런타임 의존성**을 변경하라

<img width="694" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c5ae4cc8-d64c-4d8c-ab51-bb5efd7816a5">

개방-폐쇄 원칙의 핵심은 **추상화에 의존**하는 것이다.

추상화 : 핵심적인 부분만 남기고 불필요한 부분은 생략함으로써 복잡성을 극복하는 기법
→ 문맥이 바뀌더라도 변하지 않는 부분(할인 여부를 판단하는 로직)만 남게 되고 문맥에 따라 변하는 부분(할인 요금을 계산하는 방법)은 생략

주의 ‼️
모드 요소가 추상화에 의존해야 한다. (위에 그림 컴파일 타임 의존성 처럼)
수정에 대해 닫혀 있고 확장에 대해 열려 있는 설계는 공짜로 얻어지지 않는다. **변경에 의한 파급효과를 최대한 피하기 위해서는 변하는 것(런타임 의존성)과 변하지 않는 것(컴파일 의존성)이 무엇인지를 이해하고 이를 추상화의 목적으로 삼아야 한다**. 

## 생성 사용 분리(separation use from creation)
동일한 클래스 안에서 객체 생성과 사용이라는 두가지 이질적인 목적을 가진 코드가 공존하는 것은 문제다. 유연하고 재사용 가능한 설계를 원한다면 객체와 관련된 두 가지 책임을 서로 다른 객체로 분리해야 한다.

> 소프트웨어 시스템은 (응용 프로그램 객체를 제작하고 의존성을 서로 “연결”하는) 시작 단계와 (시작 단계 이후에 이어지는) 실행 단계를 분리해야 한다.

방법 
- 클라이언트로 객체를 생성할 책임을 옮기는 것 (가장 보편적인 방법 → 어떤 정책을 이용할지 알고 있는 것은 그 시점에 협력할 클라이언트 이기 때문에)
  ~~~java
  public class Movie{
      private DiscountPolicy discountPolicy;
  	
      public Movie(String title, Duration runningTime, Money fee){
          ...
  		this.discountPolicy = new AmountDiscountPolicy(...);
  	}
  }
  ~~~
  요랬던 코드를 ... !
  ~~~java
  public class Client{
      public Money getAvatarFee(){
  		Movie avatar = new Movie("아바타", ..., new AmountDiscountPolicy(...));
  
  	}
  }
  
  ~~~
  요렇게 Client에서 패러미터로 Policy를 넣어 만드는 것 !!!

    <img width="649" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/6266bd25-53d2-4cf7-9085-5c52470ac27e">

- 객체 생성과 관련된 지식이 client와 협력하는 클라이언트에게까지 새어나가기를 원하지 않는다면 ->   
  FACTORY 추가하기 -> 객체 생성과 관련된 책임만 전달하는 별도의 객체를 추가하고 client는이 객체를 사용하도록 만들기 
  ```java
  public class Factory{
  	public Movie createAvatarMovie(){
  		return new Movie("아바타", Duration.ofMinutes(120),
  								  Money.wons(10000),
  								  new AmountDiscountPolicy(...));
  	}
  	public Movie createMadMaxMovie(){
  		return new Movie("매드 맥스", Duration.ofMinutes(120),
  									Money.wons(10000),
  									new AmountDiscountPolicy(...));
  	}
  }
  ```
    
  ```java
  public class Client{
  	private Facotry facotry;
  	
  	public Client(Factory factory){
  		this.factory = factory;	
  	}	
  
  	public Money getAvatarFee(){
  		Movie avatar = factory.createAvatarMovie();
  		return avatar.getFee();	
  	}
  }
  ```
    
      <img width="630" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c3ba35cc-525b-4aec-92ec-d17d283be73d">

    

## 순수한 가공물에 책임 할당하기

시스템을 객체로 분해하는 2가지 방식 (by. 크레이그 라만)

**표현적 분해 (representational decomposition)** : 도메인에 존재하는 사물 또는 개념을 표현하는 객체들을 이용해 시스템을 분해하는 것으로 도매인 모델에 담겨 있는 개념과 관계를 따르며 도메인과 소프트웨어 사이의 표현적 차이를 최소화 하는 것을 목적으로 한다. (기본)

**행위적 분해 (behavioral decomposition)** : 표현적 분해로 종종 (도메인 개념을 표현하는 객체를 할당하는 것) 부족한 경우가 발생하게 되는데 설계자의 편의를 위해 임의로 만들어낸 가공의 객체(순수한 가공물(PURE FABRICATION))에게 책임을 할당해서 문제를 해결

**객체지향이 실세계를 모방해야 한다는 헛된 주장에 현혹될 필요가 없다.**(설계자의 편의를 위해 임의로 만들어낸 가공의 객체들을 보면… 실세계와 안맞는게 있다) 객체를 창조하라. 우리가 애플리케이션을 구축하는 것은 사용자들이 원하는 기능을 제공하기 위해서지 실세계를 모방하거나 시뮬레이션하기 위한 것이 아니다. 도메인을 반영하는 애플리케이션의 구조라는 제약 안에서 실용적인 창조성을 발휘할 수 있는 능력은 휼룡한 설계자가 갖춰야 할 기본적인 자질이다. 

## 의존성 주입
**의존성 주입**
외부의 독립적인 객체가 인스턴스를 생성한 후 이를 전달해서 의존성을 해결하는 방법
-> 컴파일 타임 의존성을 런타임 의존성으로 바꾼다.
**세 가지 방법**
1. 생성자 주입: 객체를 생성하는 시점에 생성자를 통한 의존성 해결
   1. 소스 코드
   ```java
   Movie avatar = new Movie("아바타", Duration,ofMinutes(120),
   								  Money.wons(10000),
   								  new AmountDiscountPolicy(...));
   ```
2. setter 주입: 객체 생성 후 setter 메서드를 통한 의존성 해결
   1. 소스 코드
   ```java
   avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
   ```
   장단점: 의존성 대상을 런타임에 변경할 수 있지만, setter 메서드 호출을 누락한다면 객체가 비정상적인 상태로 생성될 것

3. 메서드 주입: 메서드 실행 시 인자를 이용한 의존성 해결
   1. 소스 코드
    
   ```java
   avatar.calculateDiscountAmount(screening, new AmountDiscountPolicy(...);
   ```
    
   장단점: 주입된 의존성이 한 두개의 메서드에서만 생성된다면 각 메서드의 인자로 전달하는게 생성자 주입보다 낫다.
    
**의존성 주입 이외의 방법**
SERVICE LOCATOR이라는 의존성을 해결할 **객체들을 보관하는 저장소**를 생성.

→ 외부에서 객체에 의존성을 전달하는 의존성 주입과는 달리, 객체가 직접 SERVICE LOCATOR에서 의존성을 해결해줄 것을 요청한다.

**소스코드**
```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        this.title = title;
        this.runningTime = runningTime;
        this.fee = fee;
        this.discountPolicy = ServiceLocator.discountPolicy();

}
```

```java
public class ServiceLocator {
    private static ServiceLocator soleInstance = new ServiceLocator();
    private DiscountPolicy discountPolicy;

    public static DiscountPolicy discountPolicy() {
        return soleInstance.discountPolicy;
    }

    public static void provide(DiscountPolicy discountPolicy) {
        soleInstance.discountPolicy = discountPolicy;
    }

    private ServiceLocator() {
    }
}
```

ServiceLocator에 PercentDiscountPolicy의 인스턴스를 등록하면 이후에 생성되는 모든 Movie는 비율 할인 정책을 기반으로 할인 요금을 계산
```java
ServiceLocator.provide(new PercentDiscountPolicy(...));
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000));
```

**숨겨진 의존성을 조심하라**‼️
위 패턴의 가장 큰 단점은 의존성을 감춘다는 것이다. Movie는 DiscountPolicy를 의존하고 있지만 Movie의 퍼블릭 인터페이스 어디에도 이 정보가 표시되어 있지 않다.
```java
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000));
avatar.calculateMovieFee(screening);
```

따라서 위 코드와 같이 ServiceLocator를 깜빡하면, NullPointerException 예외가 던져질 것이다. 위 예제로 의존성 관련 문제가 컴파일 타임이 아닌 런타임에가서야 발견된다는 사실을 알 수 있다.

문제의 원인은 숨겨진 의존성이 캡슐화를 위반했기 때문이다. 단순히 인스턴스 변수의 가시성을 private으로 선언하고 변경되는 내용을 숨겼다고 해서 캡슐화가 지켜지는 것은 아니다.
-> 클래스의 퍼블릭 인터페이스만으로 사용 방법을 이해할 수 있는 코드가 캡슐화의 관점에서 훌륭한 코드이다. 
-> 명시적인 의존성이 숨겨진 의존성보다 좋다.

## 의존성 역전 원칙
객체 사이의 협력이 존재할 때 그 협력의 본질을 담고 있는 것은 상위 수준의 정책이다. Movie와 AmountDiscountPolicy 사이의 협력이 가지는 본질은 영화의 가격을 계산하는 것이다. 

이 상황에서 상위 클래스인 Movie가 하위 클래스에 의존해서는 안된다. 하위클래스의 변경에 영향을 받으면 안된다는 것이다.

의존성 역전이란 ’**하위’ 클래스가 ‘상위’ 클래스에 의존(절차 지향과 반대)하는 것**을 말하며 이 경우 **추상화**를 통해 해결할 수 있다.

원칙
1. 상위 수준의 모듈은 하위 수준의 모듈에 의존해서는 안된다. 둘 모두 추상화에 의존해야 한다
   1. 상위수준 클래스(Movie) -> DiscountPolicy(추상화)
   2. 하위수준 클래스(AmountDiscountPolicy) -> DiscountPolicy(추상화)
2. 추상화는 구체적인 사항에 의존해서는 안 된다. 구체적인 사항은 추상화에 의존해야 한다.
-> 이를 **의존성 역전 원칙**이라고 부른다.


**의존성 역전 패키지**
의존성 역전은 의존성의 방향뿐만 아니라 인터페이스의 소유권에도 적용된다. 

<img width="705" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c88e6c44-a297-4a62-8a76-55b7df841855">
이 그림은 인터페이스가 서버 모듈 쪽에 위치하는 전통적인 모듈 구조인데, 개방-폐쇄 원칙을 준수할뿐만 아니라 의존성 역전 원칙도 따르고 있기 때문에 이 설계가 유연하고 재사용 가능하다고 생각할 것이다. 하지만 Movie를 다양한 컨텍스트에서 재사용 하기 위해서는 불필요한 클래스들이 Movie와 함께 배포돼야만 한다.

<img width="703" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/9f8126b3-f45b-4ec0-80ed-bb5c8f6452ce">
이 그림은 인터페이스의 소유권을 역전시킨 객체지향적인 모듈 구조인데, Movie와 추상클래스인 DiscountPolicy를 하나의 패키지로 모아 Movie를 특정한 컨텍스트로부터 완벽하게 독립시켰다.

Movie를 다른 컨텍스트에서 재사용하기 위해서는 단지 Movie와 DiscountPolicy가 포함된 패키지만 재사용하면 된다. 

## 유연성에 대한 조언
**유연하고 재사용 가능한 설계**란 런타임 의존성과 컴파일타임 의존성의 차이를 인식하고 동일한 컴파일 타임 의존성으로부터 다양한 런타임 의존성을 만들 수 있는 코드 구조를 가지는 설계를 의미한다. 하지만 유연성은 복잡성을 수반한다. 유연함은 단순성과 명확성의 희생 위에서 자라난다. 한편 불필요한 유연성은 불필요한 복잡성을 낳는다. 단순하고 명확한 해볍이 좋다면 유연성을 제거하라.→ **유연한 설계는 유연성이 필요할때만 옳다**

**객체의 협력과 책임이 중요하다. 설계를 유연하게 만들기 위해서는 결국 객체가 다른 객체에게 어떤 메시지를 전송하는지가 중요하고 역할 책임 협력에 초점을 맞춰야 한다.**

**역할, 책임, 협력**에 먼저 집중하라. 의존성에만 집중하다 보면 역할, 책임, 협력의 모습이 선명하게 그려지지 않을 수도 있다.