# Appendex B_타입 계층의 구현

**타입과 클래스**

- 타입 ≠ 클래스
    - 타입: 개념의 분류
    - 클래스: 타입을 구현하는 방법

**이번 장을 읽으면서 주목해야할 부분**

1. 타입 계층은 동일한 메시지에 대한 행동 호환성을 전제한다.

→ 다형성을 구현하는 방법이기도 하다.

1. 여기서 제시하는 방법을 이용한다고 서브타이핑 관계 보장 X

→ 올바른 타입 계층이 되기 위해서는 리스코프 치환 원칙을 준수해야 한다.

→ 리스코프 치환 원칙을 준수하도록 만드는 것은 우리 자신!!(특정한 구현 방법에 의해 보장되는 것이 아님!!)

## 클래스를 이용한 타입계층 구현

**2가지 구현 방법**

1. 퍼블릭 메서드

```java
public class Phone{
...
public Phone(Money amount, Duration seconds){

	}
}

public Money calculateFee(){ // Phone의 퍼블릭 인터페이스 <- 타입 <- Phone클래스는 Phone 타입을 구현한 것임..
	Money result = Money.ZERO;
...

}
```

1. 상속

```java
public class NightlyDiscountPhone extends Phone{
	private static final int ...

}

@Override
public Money calculateFee(){ // Phone과 퍼블릭 인터페이스는 동일하지만 다른 방식으로 구현한 객체
	Money result = super.calculateFee();

}
```

**결론**

클래스는 타입을 구현할 수 있는 다양한 방법 중 하나!!

다음에 살펴볼 것은 인터페이스를 이용한 타입 계층 구현!!

## 인터페이스를 이용한 타입 계층 구현

간단한 **게임을 개발**하고 있다고 가정하자. 



<img width="703" alt="Untitled" src="https://github.com/HJC96/Obejct/assets/87226129/0868d2f0-2888-445f-ae76-5b636ef950be">

다음과 같은 GameObject의 타입 계층이 있다고 가정할때 이를 상속으로 구현한다면 밑의 Explosion 같이 다중 상속을 요구하는 타입이 있을 것이다.

<img width="638" alt="Untitled 1" src="https://github.com/HJC96/Obejct/assets/87226129/8320bdb5-96f3-41f8-b46a-bf3753d567eb">



문제는 대부분의 언어들이 다중 상속을 지원하지 않는다는 것. 또한, 이 클래스들을 동일한 상속 계층 안에 구현하고 싶지 않을수도 있다.

→ 인터페이스를 이용하여 해결 가능!!

- GameObject
    
    ```java
    public interface GameObject {
        String getName();
    }
    ```
    
- Displayable
    
    ```java
    public interface Displayable extends GameObject {
        Point getPosition();
        void update(Graphics graphics);
    }
    ```
    

위 코드를 보면 Displayable 인터페이스가 GameObject를 확장한다는 사실에 주목!! 

위 코드는 Displayable 타입의 모든 인스턴스는 GameObject 타입의 인스턴스 집합에 포함된다는 것을 의미한다. → 타입 계층 구성

- Collidable
    
    ```java
    public interface Collidable extends Displayable {
        boolean collideWith(Collidable other);
    }
    ```
    

화면에 표시되는 Displayable 타입의 인스턴스들 중에는 다른 요소들과의 충돌을 처리하기 위한 객체들이 존재한다. 충돌을 체크하는 객체들은 모두 화면에 표시 가능해야 하기 때문에 **Collide 타입은 Displayable 타입의 서브타입**이어야 한다. **Displayable 타입은 GabeObject의 서브타입이므로 Collidable 타입은 자동적으로 GameObject의 서브타입**이 된다.

- Effect
    
    ```java
    public interface Effect extends GameObject {
        void activate();
    }
    ```
    

화면에 표시되지 않더라도 게임에 효과를 뷰여할 수 있는 부가적인 요소(배경음악, 효과음 등)은 Effect로 정의하고 GameObject의 서브타입이 된다.

- Player
    
    ```java
    public class Player implements Collidable {
        @Override
        public String getName() {
            return null;
        }
    
        @Override
        public boolean collideWith(Collidable other) {
            return false;
        }
    
        @Override
        public Point getPosition() {
            return null;
        }
    
        @Override
        public void update(Graphics graphics) {
    
        }
    }
    ```
    

타입에 속할 객체들을 구현해보자!! 인터페이스로 정의한 타입을 구현하기 위해 클래스를 사용!! Player는 화면에 표시돼야 할뿐만 아니라 화면 상에 표현된 다른 객체들과의 충돌을 체크해야 한다. 따라서 Playable은 Collide 타입의 인스턴스여야한다.

- Monster
    
    ```java
    public class Monster implements Collidable {
        @Override
        public String getName() {
            return null;
        }
    
        @Override
        public boolean collideWith(Collidable other) {
            return false;
        }
    
        @Override
        public Point getPosition() {
            return null;
        }
    
        @Override
        public void update(Graphics graphics) {
        }
    }
    ```
    

플레이어를 공격할 Monster 역시 Collidable 타입이 정의한 행동을 제공.

- Sound
    
    ```java
    public class Sound implements Effect {
        @Override
        public String getName() {
            return null;
        }
    
        @Override
        public void activate() {
        }
    }
    ```
    

효과음은 Effect 인터페이스를 구현해야한다.

- Explosion
    
    ```java
    public class Explosion implements Displayable, Effect {
        @Override
        public String getName() {
            return null;
        }
    
        @Override
        public Point getPosition() {
            return null;
        }
    
        @Override
        public void update(Graphics graphics) {
        }
    
        @Override
        public void activate() {
        }
    }
    ```
    

폭발 효과는 화면에 표시될 수 있어야 하고 충돌 등의 특정 조건에 의해 활성화되는 Effect의 일종이다. Sound와 달리 다른 요소들과의 충돌 여부를 체크할 필요는 없기에 Displayable과 인터페이스를 구현

**타입과 타입을 구현한 클래스 사이의 관계를 표시한 그림.**

<img width="738" alt="Untitled 2" src="https://github.com/HJC96/Obejct/assets/87226129/93b8279e-16f8-4eaf-b2ae-d564989ff395">


우리는 두 가지 사실을 알 수 있다.

1. 여러 클래스가 동일한 타입을 구현할 수 있다.
    1. player와 monster는 다른 클래스지만 이 두 클래스의 인스턴스들은 Collidable 인터페이스를 구현하고 있기 때문에 동일한 메시지에 응답할 수 있다.
2. 하나의 클래스가 여러 타입을 구현할 수 있다.
    1. Explosion의 인스턴스는 Displayable 인터페이스와 동시에 Effect 인터페이스도 구현한다.

영화 예매 시스템에서도 영화의 할인 조건을 구현한 타입 계층을 구현하기 위해 자바의 인터페이스와 클래스를 사용했다.

```java
public interface DiscountCondition {
    boolean isSatisfiedBy(Screening screening);
}

public class SequenceCondition implements DiscountCondition {
    private int sequence;

    public SequenceCondition(int sequence) {
        this.sequence = sequence;
    }

    public boolean isSatisfiedBy(Screening screening) {
        return screening.isSequence(sequence);
    }
}
public class PeriodCondition implements DiscountCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public PeriodCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
        this.dayOfWeek = dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }

    public boolean isSatisfiedBy(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
}
```

할인 조건이라는 타입을 정의하기 위해 자바 인터페이스로 DiscountCondition을 정의했고 클래스인 SequenceCondition과 PeriodCondition은 DiscountCondition 타입으로 분류될 객체들에 대한 구현을 담고있다.

## 추상 클래스를 이용한 타입 계층 구현

클래스 상속을 이용해 구현을 공유하면서도 결합도로 인한 부작용을 피하는 방법 → **추상 클래스**

```java
public abstract class DiscountPolicy {
		private List<DiscountCondition> conditions = new ArrayList<>();
		
		public DiscountPolicy(DiscountCondition ... conditions){
			this.conditions = Array.asList(conditions);
		}
	  public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : getConditions()) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return screening.getMovieFee();
    }
	
	abstract protected Money getDiscountAmount(Screening screening);
}

```

```java
public class AmountDiscountPolicy extends DiscountPolicy {
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return discountAmount;
    }
}

public class PercentDiscountPolicy extends DiscountPolicy {
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return screening.getMovieFee().times(percent);
    }
}
```

구체 클래스로 타입을 정의해서 상속받는 방법과 추상 클래스로 타입을 정의해서 상속받는 방법 사이에는 두 가지 중요한 차이점이 있다.

1. 추상화의 정도: AmountDiscountPolicy와 PercentDiscountPolicy가 DiscountPolicy 내부 구현이 아닌 추상 메서드의 시그니처에만 의존. DiscountPolicy는 할인 조건을 판단하는 고차원의 모듈이고 Amount, Percent는 계산하는 저차원의 모듈
2. 상속을 사용하는 의도: 앞선 예제의 Phone은 상속을 염두에 두고 설계된 것이 아니다. 반면 DiscountPolicy는 처음부터 상속을 염두에 두고 설계된 클래스이다. DiscountPolicy는 추상 메서드를 제공함으로써 상속 계층을 쉽게 확장할 수 있게하고 결합도로 인한 부작용을 방지할 수 있는 안전망을 제공한다!! 

<img width="737" alt="Untitled 3" src="https://github.com/HJC96/Obejct/assets/87226129/0105ed1f-0ce7-44cd-bbe1-86d97fb11bd0">

## 추상 클래스와 인터페이스 결합하기

- 클래스와 단일 상속으로는 여러 타입으로 분류되는 타입을 해결할 수 없을 수도 있다.
- 인터페이스를 이용하는 방법도 몇몇 언어에서는 구현 코드를 포함시킬 수 없다는 문제가 있다.

→ 인터페이스를 이용해 타입을 정의하고 코드 공유의 필요가 있다면 추상 클래스를 이용해 코드 중복 방지!!~ **골격 구현 추상 클래스**

DiscountPolicy 타입은 추상 클래스를 이용해서 구현했기 때문에 DiscountPolicy 타입에 속하는 모든 객체들은 하나의 상속 계층 안에 묶여야 하는 제약을 가진다. 이때 **DiscountPolicy 타입으로 분류될 수 있는 객체들이 구현 시에 서로 다른 상속 계층에 속할 수 있도록 만들고 싶다고 가정**해보자! 가장 좋은 방법은 **인터페이스**와 **추상 클래스를 결합**하는 것이다. DiscountPolicy 타입을 추상 클래스에서 인터페이스로 변경하고 공통 코드를 담을 **골격 구현 추상 클래스인 DefaultDiscountPolicy**를 추가함으로써 상속 계층이라는 굴레를 벗어날 수 있다. 

```java
public interface DiscountPolicy {
    Money calculateDiscountAmount(Screening screening);
}
    
public abstract class DefaultDiscountPolicy implements DiscountPolicy{
	private List<DiscountCondition> conditions = new ArrayList<>();

	public DefaultDiscountPolicy(DiscountCondition ... conditions){
		this.conditions = Arrays.asList(conditions);
	}

	@Override
	public Money calculateDiscountAmount(Screening screening){
		for(DiscountCondition each: conditions){
			if(each.isSatisfiedBy(screening)){
				return getDiscountAmount(screening);
				}
			}

		return screening.getMovieFee();
	}

abstract protected Money getDiscountAmount(Screening screening);
}
```

이제 AmountDiscountPolicy와 PercentDisocuntPolicy의 부모 클래스를 DefaultDiscountPolicy로 변경하자!

```java
public class AmountDiscountPolicy extends DefaultDiscountPolicy{ ... }
public class PercentDiscountPolicy extends DefaultDiscountPolicy{ ... }
```

다음과 같은 타입 계층이 될것이다!!

<img width="775" alt="Untitled 4" src="https://github.com/HJC96/Obejct/assets/87226129/a0fbc778-9808-4928-b52e-b1a27daa7b30">

인터페이스와 추상 클래스를 함께 사용하는 방법은 추상 클래스만 사용하는 방법에 비해 두 가지 장점이 있다.

- 다양한 구현 방법이 필요할 경우 새로운 추상 클래스를 추가해서 쉽게 해결할 수 있다.
    - 예를들어 금액 할인 정책을 더 빠른 속도로 처리할 수 있는 방법, 메모리를 더 적게 차지하는 방법을 구현해놓고 적절한 방법을 선택할 수 있다.
- 이미 부모 클래스가 존재하는 클래스라고 하더라도 인터페이스를 추가함으로써 새로운 타입으로 쉽게 확장할 수 있다.
    - DiscountPolicy 타입이 추상 클래스로 구현돼 있는 경우에 이 문제를 해결할 수 있는 유일한 방법은 상속 계층을 다시 조정하는 것뿐이다.

## 덕 타이핑

“어떤 새가 오리처럼 걷고, 오리처럼 헤엄치며, 오리처럼 꽥꽥 소리를 낸다면 나는 이 새를 오리라고 부를 것이다"

**덕 테스트**란 어떤 대상의 행동이 오리와 같다면 그것을 오리라는 타입으로 취급해도 무방하다는 것이다!

하지만 자바 같은 대부분의 정적 타입 언어에서는 덕 타이핑을 지원하지 않는다. 다음을 보자!

```java
public interface Employee {
    Money calculatePay(double taxRate);
}

public class HourlyEmployee {
    private String name;
    private Money basePay;
    private int timeCard;

    public HourlyEmployee(String name, Money basePay, int timeCard) {
        this.name = name;
        this.basePay = basePay;
        this.timeCard = timeCard;
    }

    public Money calculatePay(double taxRate) {
        return basePay.times(timeCard).minus(basePay.times(timeCard).times(taxRate));
    }
}

public class SalariedEmployee {
    private String name;
    private Money basePay;

    public SalariedEmployee(String name, Money basePay) {
        this.name = name;
        this.basePay = basePay;
    }

    public Money calculatePay(double taxRate) {
        return basePay.minus(basePay.times(taxRate));
    }
}
```

SharedEmployee와 HourlyEmployee 클래스는 Employee 인터페이스에 정의된 calculatePay 오퍼레이션과 동일한 시그니처를 가진 퍼블릭 메서드를 포함하고 있다.

이때 둘 다 calculatePay라는 동일한 퍼블릭 인터페이스를 공유하기 때문에 동일한 타입으로 취급할 수 있다고 예상할 것이다. 하지만 자바 같은 대부분의 정적 타입 언어에서는 두 클래스를 동일한 타입으로 취급하기 위해서는 코드 상의 타입이 동일하게 선언돼 있어야한다!

```java
public Money calculate(Employee employee, double taxRate){
	return employee.calculatePay(taxRate);
}

calculate(new SalariedEmployee(...), 0.01); // 컴파일 에러
calculate(new HourlyEmployee(...), 0.01);   // 컴파일 에러
```

반면 **런타임에 타입을 결정하는 동적 타입 언어는 특정한 클래스를 상속받거나 인터페이스를 구현하지 않고도 객체가 수신할 수 있는 메시지의 집합으로 객체의 타입**을 결정할 수 있다.

```ruby
class SalariedEmployee
  def initialize(name, basePay)
    @name = name
    @basePay = basePay
  end
    
  def calculatePay(taxRate)
    @basePay - (@basePay * taxRate)
  end
end

class HourlyEmployee < Employee
  def initialize(name, basePay, timeCard)
    @name = name
    @basePay = basePay
    @timeCard = timeCard
  end
  
  def calculatePay(taxRate)
    (@basePay * @timeCard) - (@basePay * @timeCard) * taxRate
  end
end

def calculate(employee, taxRate)
  employee.calculatePay(taxRate)
end
```

루비 같은 동적 타입 언어에서는 명시적으로 동일한 클래스를 상속받거나 동일한 인터페이스를 구현하지 않더라도 시그니처가 동일한 메서드를 가진 클래스는 같은 타입으로 취급할 수 있다.

```ruby
def calculate(employee, taxRate)
	employee.calculatePay(taxRate)
end
```

```ruby
calculate(SalariedEmployee.new(...), 0.01) // 성공!
calculate(HourlyEmployee.new(...), 0.01) // 성공! 
```

이것이 바로 덕 타이핑이다. 덕 타이핑은 **타입이 행동에 대한 것이라는 사실을 강조**한다. 두 객체가 동일하게 행동한다면 내부 구현이 어떤 방식이든 상관없다!

다음은 C# 언어에서 dynamic 키워드를 사용해 덕 타이핑을 흉내낸 것이다.

```csharp
public class SalariedEmployee
	{
		private string name;
		private decimal basePay;

		public SalariedEmployee(String name, decimal basePay)
		{
			this.name = name;
			this.basePay = basePay;
		}

		public decimal CalculatePay(decimal taxRate)
		{
			return basePay - basePay * taxRate;
		}
	}

	public class HourlyEmployee
	{
		private String name;
		private decimal basePay;
		private int timeCard;

		public HourlyEmployee(String name, decimal basePay, int timeCard)
		{
			this.name = name;
			this.basePay = basePay;
			this.timeCard = timeCard;
		}

		public decimal CalculatePay(decimal taxRate)
		{
			return (basePay * timeCard) - (basePay * timeCard) * taxRate;
		}
	}

	public class TaxOffice
	{
		public decimal Calculate(dynamic employee, decimal taxRate)
		{
			return employee.CalculatePay(taxRate);
		}
	}
```

```csharp
public decimal Calculate(dynamic employee, dynamic taxRate)
{
	return employee.CalculatePay(taxRate);
}
```

```csharp
Calculate(new SalariedEmployee(...), 0.01); // 성공!
Calculate(new HourlyEmployee(...), 0.01); // 성공!
```

위와 같이 dynamic 키워드를 추가하면 CalculatePay 메시지에 응답할 수 있는 어떤 객체라도 파라미터로 전달하는 것이 가능해진다!

하지만 덕 타이핑을 사용하면 **컴파일 시점에 발견할 수 있는 오류를 실행 시점으로 미루게 되기 때문에 설계의 유연성을 얻는 대신 코드의 안전성을 약화**시킬 수 있다.

반면 컴파일타임 체크를 통해 타입 안전성을 보장하는 언어도 있다. C++에서 제너릭 프로그래밍을 구현하는 템플릿은 **타입 안전성**과 **덕 타이핑**이라는 두 가지 장점을 성공적으로 조합한다.

SalariedEmployee와 HourlyEmployee 클래스를 C++로 구현해보자

```cpp
using namespace std;

class SalariedEmployee
{
private: 
  string name;
  long base_pay;

public:
  SalariedEmployee(string name, long base_pay);
  long calculate_pay(double tax_rate);
};

SalariedEmployee::SalariedEmployee(string name, long base_pay)
{
  this->name = name;
  this->base_pay = base_pay;
}

long SalariedEmployee::calculate_pay(double tax_rate)
{
  return base_pay - (base_pay * tax_rate);
}

class HourlyEmployee
{
private: 
  string name;
  long base_pay;
  int time_card;

public:
  HourlyEmployee(string name, long base_pay, int timeCard);
  long calculate_pay(double tax_rate);
};

HourlyEmployee::HourlyEmployee(string name, long base_pay, int time_card)
{
  this->name = name;
  this->base_pay = base_pay;
  this->time_card = time_card;
}

long HourlyEmployee::calculate_pay(double tax_rate)
{
  return (base_pay * time_card) - (base_pay * time_card) * tax_rate;
}
```

코드에서 보면 알 수 있듯이 SalariedEmployee와 HourlyEmployee 클래스는 동일한 시그니처를 가지는 퍼블릭 메서드인 calculate_pay(double tax_rate)를 구현하고 있지만 두 클래스를 동일한 타입으로 묶어주는 어떤 명시적인 타입 선언도 존재하지 않는다.

하지만 아래와 같이 템플릿을 사용하면 관계가 없는 두 클래스를 동일한 타입으로 취급하는 것이 가능하다!

```cpp
template <typename T>
long calculate(T employee, double tax_rate)
{
  return employee.calculate_pay(tax_rate);
}
```

Calculate 함수는 첫 번째 패러미터로 임의의 타입 T의 인스턴스를 취하는 함수 템플릿이다. calculate 함수가 타입 파라미터 T에게 요구하는 것은 단 한가지다! **인자로 전달되는 객체의 클래스가 calculate_pay** **메시지를 이해**해야 한다는 것이다!

따라서 calculate 함수의 첫 번째 파라미터에 대해 덕 타이핑을 지원한다. 더 반가운 소식은 첫 번째 T 타입 인자로 전달되는 객체가 calculate_pay 함수를 구현하고 있는지를 런타임이 아닌 **컴파일 시점에 체크할 수 있다**는 것이다. 따라서 C++의 템플릿 시스템은 **정적 타입 언어의 장점인 타입 안전성까지도 보장**해준다.

```cpp
int main()
{
  std::cout << calculate(SalariedEmployee("Salaried", 1000), 0.01) << std::endl;
  std::cout << calculate(HourlyEmployee("Hourly", 100, 10), 0.01) << std::endl;
}
```

하지만 C++ 템플릿은 호출되는 각각의 타입에 대해 calculate 함수의 복사본을 만든다. 즉, SalariedEmployee와 HourlyEmployee 클래스의 인스턴스를 calculate(T employee, double tax_rate) 함수의 첫 번째 인자로 사용해서 호출하면 컴파일러가 calculate(SalariedEmployee employee, double tax_rate)함수와 calculate(HourlyEmployee employee, double tax_rate) 함수라는 **개별적인 두 개의 함수를 내부적으로 생성**한다는 것이다. 따라서 C++ 템플릿을 사용해서 덕 타이핑의 타입 안전성을 보장하는 대가로 거대한 크기의 프로그램이라는 비효율성을 감수해야만 한다.

## 믹스인과 타입 계층

- **믹스인**은 객체를 생성할 때 코드 일부를 섞어 넣을 수 있도록 만들어진 일종의 추상 서브클래스다.
- **믹스인의 목적**은 다양한 객체 구현 안에서 동일한 ‘행동’을 중복 코드 없이 재사용할 수 있게 만드는 것이다.

이해를 돕기 위해 스칼라 언어에서 믹스인을 구현하기 위해 제공하는 **트레이트**를 살펴보자. 다양한 어플리케이션을 작성하다 보면 동일한 타입의 객체들을 비교해야 할 필요가 있는데 이 문제를 위해 스칼라는 비교와 관련된 공통적인 구현을 믹스인해서 재사용할 수 있게 Ordered라는 트레이트를 제공한다. Ordered 트레이트는 내부적으로 추상 메서드 compare를 사용해 <, >, ≤, ≥ 연산자를 구현한다.

```scala
trait Ordered[A] extends Any with Comparable[A]{
	def compare(that: A): Int
	def < (that:A): Boolean = (this compare that) < 0 
	def > (that:A): Boolean = (this compare that) > 0 
	def <= (that:A): Boolean = (this compare that) <= 0 
	def >= (that:A): Boolean = (this compare that) >= 0
	def compareTo(that: A): Int = compare(that)
}
```

이제 비교 연산자를 추가하고 싶은 클래스에 Ordered 트레이트를 믹스인하고 추상 메서드를 compare를 오버라이딩하기만 하면 공짜로 <, >, ≤, ≥ 연산자를 퍼블릭 인터페이스에 추가할 수 있게 된다. 다음은 Money 클래스에 비교연산자를 추가한 예이다!

```scala
case class Money(amount: Long) extends Ordered[Money] {
  def + (that: Money): Money = Money(this.amount + that.amount)
  def - (that: Money): Money = Money(this.amount - that.amount)
  def compare(that: Money): Int = (this.amount - that.amount).toInt 
}
object Main extends App {
  println(Money(10) < Money(20))
}
```

- Ordered 트레이트는 구현뿐만 아니라 퍼블릭 메서드를 퍼블릭 인터페이스에 추가하기 때문에 이제 Money는 Ordered 트레이트를 요구하는 모든 위치에서 Ordered를 대체할 수 있다.
- 이것은 서브타입의 요건인 리스코프 치환 원칙을 만족시키기 때문에 Money는 Ordered 타입으로 분류될 수 있다.
- 믹스인은 간결한 인터페이스를 가진 클래스를 풍부한 인터페이스를 가진 클래스로 만들기 위해 사용될 수 있다.
    - 물론 풍부한 인터페이스를 정의한 트레이트의 서브타입으로 해당 클래스를 만드는 부수적인 효과도 얻으면서!
    

자바의 경우에는 믹스인을 구현하기 위해 **디폴트 메서드를 사용할 수 있으며, 이를 통해 간결한 인터페이스를 가진 클래스를 풍부한 인터페이스를 가진 클래스로 변경**할 수 있다!!

```java
public interface DiscountPolicy {
    default Money calculateDiscountAmount(Screening screening) { // 이제 추상 클래스를 사용하지 않고 DiscountPolicy 인터페이스를 상속받는 것으로도 쉽게 Amount, PercenctDiscountPolicy를 추가할 수 있을 것이다!
        for(DiscountCondition each : getConditions()) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return screening.getMovieFee();
    }

    List<DiscountCondition> getConditions();
    Money getDiscountAmount(Screening screening);
}
```

위와 같이 DiscountPolicy 인터페이스의 calculateDiscountAmount 오퍼레이션을 디폴트 메서드로 구현하면 더 이상 기본 구현을 제공하기 위해 인터페이스를 구현하는 추상 클래스를 만들 필요가 없을 것이다!! 

**디폴트 메서드의 한계**

1. getConditions 오퍼레이션과 getDiscountAmount 메서드가 내부적으로 두 개의 메서드를 사용하기에 메서드의 구현을 제공한다.
2. 문제는 이 메서드들이 인터페이스에 정의되었기 때문에 클래스 안에서 퍼블릭 메서드로 구현돼야 한다!!
    1. 기존에는 getDiscountAmount메서드의 가시성은 protected 였다 ㅠㅠ 이제는 디폴트 메서드 안에서 사용되니 public으로…
    2. getConditions 메서드 같은 경우 문제가 더 심각한데, 클래스 내부에서 DiscountConditions의 목록을 관리한다는 사실을 외부에 공개할뿐만 아니라 public 메서드를 제공함으로써 이 목록에 접근할 수 있게 해준다…! → 캡슐화 약화…
    

게다가 이 방법은 AmountDiscountPolicy와 PercentDiscountPolicy 클래스 사이의 코드 중복을 완벽하게 제거해 주지도 못한다!!

다음 코드를 봐~~

```java
public class AmountDiscountPolicy implements DiscountPolicy {
    private Money discountAmount;
    private List<DiscountCondition> conditions = new ArrayList<>();

    public AmountDiscountPolicy(Money discountAmount,
                                DiscountCondition... conditions) {
        this.discountAmount = discountAmount;
        this.conditions = Arrays.asList(conditions);
    }

    @Override
    public List<DiscountCondition> getConditions() {
        return conditions;
    }

    @Override
    public Money getDiscountAmount(Screening screening) {
        return discountAmount;
    }
}
```

```java
public class PercentDiscountPolicy implements DiscountPolicy {
    private double percent;
    private List<DiscountCondition> conditions = new ArrayList<>();

    public PercentDiscountPolicy(double percent,
                                 DiscountCondition... conditions) {
        this.percent = percent;
        this.conditions = Arrays.asList(conditions);
    }

    @Override
    public List<DiscountCondition> getConditions() {
        return conditions;
    }

    @Override
    public Money getDiscountAmount(Screening screening) {
        return screening.getMovieFee().times(percent);
    }
}
```

그럼 왜 디폴트 메서드를 추가했냐?

→ 인터페이스로 추상 클래스의 역할을 대체하려는 것이 아니라 기존에 널리 사용되고 있는 인터페이스에 새로운 오퍼레이션을 추가할 경우에 발생하는 하위 호환성 문제를 해결하기 위함…!!!!

따라서 타입을 정의하기 위해 디폴트 메서드를 사용할 생각이라면 그 한계를 명확하게 알아두기 바란다.
