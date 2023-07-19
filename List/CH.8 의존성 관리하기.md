# CH.8 의존성 관리하기

'의존성 관리하기'에서는 의존성의 개념을 자세히 설명하고 결합도를 느슨하게 유지할 수 있는 다양한 설계 방법들을 설명한다. 의존성의 관리가 곧 변경의 관리이고 유연한 설계를 낳는 기반이라는 사실을 이해하게 될 것이다.

# 의존성 이해하기

**변경과 의존성**

어떤 객체가 예정된 작업을 정상적으로 수행하기 위해 다른 객체를 필요로 하는 경우 두 객체 사이에 의존성이 존재한다고 말한다. 의존성은 방향성을 가지며 항상 단 방향이다. 의존성은 실행시점과 구현 시점에 서로 다른 의미를 가진다. 의존성은 변경에 의한 영향의 전파 가능성을 암시한다.

실행 시점 - 의존하는 객체가 정상적으로 동작하기 위해서는 실행 시에 의존 대상 객체가 반드시 존재해야 한다.

구현 시점 - 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다.  

**의존성 전이(transitive dependency)**

의존성은 함께 변경 될 수 있는 가능성을 의미하기 때문에 모든 경우에 의존성이 전이 되는 것은 아니다. 의존성이 실제로 전이될지 여부는 변경의 방향과 캡슐화의 정도에 따라 달라진다. 의존성 전이는 변경에 의해 영향이 널리 전파될 수도 있다는 경고일 뿐이다.

**런타임 의존성과 컴파일 타임 의존성**

런타임 : 애플리케이션이 실행되는 시점

컴파일 타임 : 작성된 코드를 컴파일 하는 시점 혹은 코드 그 자체(동적 타입 언어의 경우)

객체지향 애플리케이션에서 런타임의 주인공은 객체다. 따라서 런타임 의존성이 다루는 주제는 객체사이의 의존성이다. 반면 코드 관점에서 주인공은 클래스다. 따라서 컴파일 타임 의존성이 다루는 주제는 클래스 사이의 의존성이다. 

여기서 중요한 것은 런타임 의존성과 컴파일 타임 의존성이 다를 수 있다는 것이다. 사실 유연하고 재사용 가능한 코드를 설계하기 위해서는 두 종류의 의존성을 서로 다르게 만들어야 한다. 

코드 작성 시점의 Movie 클래스는 할인 정책을 구현한 두 클래스의 존재를 모르지만 실행 시점 Movie 객체는 두 클래스의 인스턴스와 협력할 수 있게 된다. 실제로 협력할 객체가 어떤 것인지는 런타임에 해결해야 한다. → 유연한 설계

**컨텍스트 독립성**

클래스가 사용될 특정한 문맥에 대해 최소한의 가정만으로 이뤄져 있다면 다른 문맥에서 재사용하기가 더 수월해진다. 이를 컨텍스트 독립성이라고 부른다. 

Ex) Movie 클래스에 추상 클래스인 DiscountPolicy에 대한 컴파일 타임 의존성을 명시하는 것은 Movie가 할인 정책에 따라 요금을 계산하지만 구체적으로 어떤 정책을 따르는지는 결정하지 않았다고 선언하는 것이다. 

**의존성 해결하기**

컴파일타임 의존성을 실행 컨텍스트에 맞는 적절한 런타임 의존성으로 교체하는 것을 의존성 해결이라고 부른다.

의존성 해결 방법 (1,2 섞어서 많이 사용)

- 객체를 생성하는 시점에 생성자를 통해 의존성 해결
    
    ```ruby
    
    Movie avatar = new Movie("avatar",
    												DuraionlofMinutes(120),
    												Money.wons(10000),
    												new AmountDiscountPOlicy(...)); 
    											//new PercentDiscountPolicy(...))
    
    이를위해 Movie 클래스는 두가시 인스턴스 모두를 선택적으로 전달 받을 수 있도록 
    이 두 클래스의 부모 클래스인 DiscountPolicy타입의 인자를 받는 생성자를 정의한다. 
    
    public class Movie{
    	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
    	...
    	this.discountPolicy = discountPolicy;
    	}
    }
    ```
    
- 객체 생성 후 setter메서드를 통해 의존성 해결
    - 객체를 생성한 이후에도 의존하고 있는 대상을 변경할 수 있는 가능성을 열어 놓고 싶은 경우에 유용하다.
    
    ```ruby
    Movie avatar = new Movie(...));
    avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
    
    이 경우 Movie 인스턴스가 생성된 후에도 DiscountPolicy를 설정할 수 있는 setter 메서드를 제공해야 한다. 
    
    public class Movie{
    	public Movie(DiscountPolicy discountPolicy){
    	this.discountPolicy = discountPolicy;
    	}
    }
    ```
    
- 메서드 실행 시 인자를 이용해 의존성 해결
    - Movie가 항상 할인 정책을 알 필요까지는 없고 가격을 계산할 때만 일시적으로 알아도 무방하다면 메서드 인자를 이용해 의존성을 해결할 수도 있다.
    
    ```ruby
    public class Movie{
    	public Movie calculateMovieFee (Screening screening, DiscountPolicy discountPolicy){
    	...
    	this.discountPolicy = discountPolicy;
    	}
    }
    ```
    

# 유연한 설계

## 의존성과 결합도

**용어**

- 의존성: 두 요소 사이의 관계 유무를 설명 ex) 의존성이 존재한다, 존재하지 않는다
    - 바람직한 의존성: 컨텍스트에 독립적인 의존성을 의미하며 다양한 환경에서 재사용 될 수 있다.
        - 추상 클래스를 의존 → 재사용 가능
    - 바람직하지 못한 의존성: 특정한 컨텍스트에 강하게 결합되어 있다.
        - 구체 클래스를 의존 → 재사용이 어렵다.
- 결합도: 의존성의 정도를 표현 ex) 결합도가 강하다, 결합도가 느슨하다
    - 결합도의 정도는 한 요소가 자신이 의존하고 있는 다른 요소에 대해 알고 있는 정보(지식)의 양으로 결정

**의존 대상 구분: 추상화와 결합도의 관점**

아래쪽으로 갈수록 클라이언트가 알아야 하는 지식의 양이 적어지기 때문에 결합도가 느슨해진다.

- 구체 클래스 의존성
- 추상 클래스 의존성
    - 구체 클래스에 비해 메서드의 내부 구현과 자식 클래스의 종류에 대한 지식을 클라이언트에게 숨길 수 있다.
- 인터페이스 의존성
    - 추상 클래스에서 더 나아가, 상속 계층을 몰라도 협력이 가능하다
        
        예시 추가하기!!  -아름 p268
        

## **의존성을 해결하는 방법**

**세 가지 방법**

1. 생성자
2. setter 메서드
3. 메서드 인자 사용

**의존성 특징**

명시적 의존성: 퍼블릭 인터페이스에 의존성을 노출

숨겨진 의존성: 퍼블릭 인터페이스에 노출하지 않고 숨긴다.

→ 의존성은 명시적으로 표현돼야 한다.

**new는 해롭다**

1. 구체 클래스의 이름을 직접 기술해야하기 때문에
2. 구체 클래스뿐만 아니라 어떤 인자를 이용해 클래스의 생성자를 호출해야 하는지도 알아야하기 때문에.→ 지식이 늘어남

**문제가 되는** **소스코드 및 도메인**

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
        this.discountPolicy = new AmountDiscountPolicy(Money.wons(800), new SequenceCondition ...;

}
```

<img width="701" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/3195a52f-df73-4637-8091-e4aba5d253de">


**해결 방법**

인스턴스를 생성하는 로직과 생성된 인스턴스를 사용하는 로직을 분리하여 **결합도를 낮춘다**

→ 다른 말로 표현하면 객체를 생성하는 책임을 클라이언트로 옮기고, movie는 인스턴스를 사용

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountpolicy) {
        this.title = title;
        this.runningTime = runningTime;
        this.fee = fee;
        this.discountPolicy = discountPolicy;

}
```

```java
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000),
new AmountDiscountPolicy(Money.wons(800), new SequenceCondition ...;
```

**예외**

- 주로 협력하는 기본 객체를 설정하고 싶은 경우 클래스 안에서 객체의 인스턴스를 생성해도 된다.
    - 오히려 이편이 중복코드를 줄일 수 있기 때문에
    

```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        this(title, runningTime, fee, new AmountDiscountPolicy(...));

}
```

- 표준 클래스에 대한 의존은 해롭지 않다
    - 변경될 확률이 거의 없기 때문에

**컨텍스트 확장**

1. 할인 혜택 제공하지 않는 영화: None discountPolicy 추가
2. 다수의 할인 정책 중복 적용: List 변수를 가져 중복정책 해결
