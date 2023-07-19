# CH.13 서브클래싱과 서브타이핑

**지난 개념**

상속의 용도

1. 타입 계층
    1. 다형적으로 동작하는 객체들의 관계에 기반해 확장 가능하고 유연한 설계를 얻을 수 있다
2. 코드 재사용
    1. 부모클래스와 자식 클래스가 강하게 결합되어 변경하기 어렵다

**그렇다면 타입 계층이란 무엇인가!?**

※ 객체지향 프로그래밍 vs 객체기반 프로그래밍

**객체기반 프로그래밍**이란 상태와 행동을 캡슐화한 객체를 조합해서 프로그램을 구성하는 방식을 가리킨다. 객체지향 프로그래밍 역시 객체기반 프로그래밍의 한 종류다.

종종 객체기반 프로그래밍이 다른 의미로 사용되기 때문에 혼란을 초래하는 경우가 있는데, 객체기반 프로그래밍이 자바스크립트와 같이 **클래스가 존재하지 않는 프로토타입 기반 언어를 사용한 프로그래밍 방식을 가리키기 위해 사용되는 경우**가 바로 그것이다.

## 타입

**타입을 바라보는 다양한 관점**

**개념 관점**

- 타입: 우리가 인지하는 세상의 사물 종류를 의미한다.
    - 프로그래밍 언어
- 인스턴스: 어떤 대상이 타입으로 분류될 때 그 대상을 타입의 인스턴스라고 부른다.
    - 자바, 루비, 자바스크립트 등
- 객체: 타입의 인스턴스를 객체라고 부른다.(대명사 같은 느낌)
- 심볼: 타입에 이름을 붙인 것
    - 앞에서 ‘프로그래밍 언어’가 타입의 심볼에 해당
- 내연: 타입의 정의로서 타입에 속하는 객체들이 가지는 공통적인 속성이나 행동을 가리킨다
    - 프로그래밍 언어의 정의인 컴퓨터에게 특정한 작업을 지시하기 위한 어휘와 문법적 규칙의 집합이 바로 내연에 속한다.
- 외연: 타입에 속하는 **객체들의 집합**이다. ‘프로그래밍 언어’ 타입의 경우에는 자바, 루비, 자바스크립트, C가 속한 집합이 외연을 구성한다.

<img width="710" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/eb957a6a-93bb-4277-84d3-1c30e57a1339">


**프로그래밍 언어 관점**

- 타입: 비트 묶음에 의미를 부여하기 위해 정의된 **제약과 규칙**을 가리킨다.
- 타입의 목적
    - 타입에 수행될 수 있는 유효한 오퍼레이션 집합을 정의한다.
        - 모든 객체지향 언어들은 객체의 타입에 따라 적용 가능한 연산자의 종류를 제한함.
            - 자바 ‘+’ 연산자는 다른 클래스의 인스턴스에 대해서는 사용할 수 없다.
            - C++, C# 연산자 오버로딩을 통해 ‘+’ 연산자를 사용하는 것이 가능
    - 타입에 수행되는 오퍼레이션에 대해 미리 약속된 문맥을 제공한다.
        - 자바의 a + b 연산을 예로들면 a, b 타입이 int라면 두 수를 더할 것
        - 객체를 생성하는 방법에 대한 문맥을 결정하는 것은 바로 객체의 타입
            - new 연산자
- 타입 목적 달성시 얻을 수 있는 것
    - 적용 가능한 오퍼레이션의 종류와 의미를 정의함으로써 코드의 의미를 명확하게 전달
        - 개발자의 실수 방지

**객체지향 패러다임 관점**

지금까지의 내용을 바탕으로 타입을 다음 두 가지 관점에서 정의 가능

- 개념 관점에서 타입이란 공통의 특징을 공유하는 대상들의 분류
- 프로그래밍 언어 관점에서 타입이란 동일한 오퍼레이션을 적용할 수 있는 인스턴스들의 집합

조합  

→ 프로그래밍 언어의 관점에서 타입은 호출 가능한 오퍼레이션의 집합을 정의

→ 객체지향 프로그래밍에서 오퍼레이션은 객체가 수신할 수 있는 메시지를 의미

객체의 타입이란 객체가 수신할 수 있는 메시지의 종류를 정의하는 것 → 퍼블릭 인터페이스

즉, 객체의 **퍼블릭 인터페이스가 타입을 결정**한다

 

 

### **타입 계층**

**타입계층**

**포함하는 타입**

- 포함되는 타입보다 더 많은 인스턴스를 가진다.
- 외연 관점에서는 더 크고 내연 관점에서는 더 일반적이다.

 

**포함되는 타입**

- 포함하는 타입보다 더 적은 인스턴스를 가진다
- 외연 관점에서는 더 작고 내연 관점에서는 더 특수하다.

**타입계층을 일반화와 특수화 관계로 표현**

타입 계층을 구성하는 두 타입 간의 관계에서 더 일반적인 타입을 슈퍼타입이라고 부르고 더 특수한 타입을 서브타입이라고 부른다.

**슈퍼타입**

- 더 일반적인 타입
- 어떤 타입의 정의를 좀 더 보편적이고 추상적으로 만드는 과정을 의미 - 내연관점
- 일반적인 타입의 인스턴스 집합은 특수한 타입의 인스턴스 집합을 포함하는 슈퍼셋 - 외연관점

**서브타입**

- 더 특수한 타입
- 어떤 타입의 정의를 좀 더 구체적이고 문맥 종속적으로 만드는 과정 - 내연관점
- 일반적인 타입의 인스턴스 집합에 포함된 서브셋 - 외연관점

**퍼블릭 인터페이스 관점에서 슈퍼타입과 서브타입**

**슈퍼타입**

- 서브타입이 정의한 퍼블릭 인터페이스를 일반화시켜 상대적으로 범용적이고 넓은 의미로 정의한 것

**서브타입**

- 슈퍼타입이 정의한 퍼블릭 인터페이스를 특수화시켜 상대적으로 구체적이고 좁은 의미로 정의한 것

→ 강조점: 서브타입의 인스턴스는 슈퍼타입의 인스턴스로 간주될 수 있다.

### 서브클래싱과 서브타이핑

어떤 조건을 만족시켜야만 타입 계층을 위해 올바르게 상속을 사용했다고 말할 수 있을까?

- 상속 관계가 is-a 관계를 모델링하는가?
    - 일반적으로 “[자식 클래스]는 [부모 클래스]다”라고 말해도 이상하지 않다면 **상속을 사용할 후보**로 간주할 수 있다.
- **클라이언트 입장에서 부모 클래스의 타입으로 자식 클래스를 사용해도 무방한가?** ← 초점을 맞추어라
    - 부모 클래스와 자식 클래스의 차이점을 몰라야 한다.
        - 이를 자식 클래스와 부모 클래스 사이의 **행동 호환성**이라고 부른다.

(예제)

**두 가지 사실**

- 펭귄은 새다.
- 새는 날 수 있다.

```java
public class Bird{
	public void fly(){ ... }
}

public class Penguin extends Bird {
	...
}
```

어휘적으로 펭귄은 새지만 만약 새의 정의에 날 수 있다는 행동이 포함된다면 펭귄은 새의 서브타입이 될 수 없다.

→ 하지만 어떤 어플리케이션에서 새에게 날 수 있다는 행동을 기대하지 않고 단지 울음 소리를 낼 수 있다는 행동만 기대한다면 새와 펭귄을 타입 계층으로 묶어도 무방하다.

→ 행동에 따라 타입 계층을 구성해야 한다는 사실을 잘 보여줌.

**행동 호환성**

펭귄과 새라는 단어는 두 타입을 is - a 관계로 묶고 싶을 만큼 매혹적이다. 하지만 새와 펭귄의 **서로 다른 행동 방식**은 이 둘을 동일한 타입 계층으로 묶어서는 안된다고 경고한다. 

- 두 타입 사이에 행동이 호환될 경우에만 타입 계층으로 묶어야한다.
    - 이때 행동 호환여부를 판단하는 기준은 **클라이언트 관점**
    - penguin이 Bird의 서브타입이 아닌 이유는 클라이언트 입장에서 모든 새가 날 수 있다고 가정하기 때문이다.
        - 중요한 것은 클라이언트의 기대

다음과 같이 클라이언트가 날 수 있는 새만을 원한다고 가정해보자.

```java
public void flyBird(Bird bird){
  // 인자로 전달된 모든 bird는 날 수 있어야 한다.
	bird.fly();
}
```

현재 Penguin은 Bird의 자식 클래스이기 때문에 컴파일러는 업캐스팅을 허용한다. 따라서 flyBird 메서드의 인자로 Penguin의 인스턴스가 전달되는 것을 막을 수 없다. → 수정이 필요함

**상속 관계를 유지하며 문제해결을 위해 시도해 볼 수 있는 세 가지 방법**

1. Penguin의 fly 메서드를 오버라이딩해서 내부 구현을 비워둔다

```java
public class Penguin extends Bird {
	...
	@override
	public void fly(){}
}
```

Penguin에게 fly 메시지를 전송하더라도 아무 일도 일어나지 않는다. 하지만 이 방법은 어떤 행동도 수행하지 않기 때문에 모든 bird가 날 수 있다는 클라이언트의 기대를 만족시키지 못한다.

→ Penguin과 Bird의 행동은 호환되지 않기때문에 올바른 타입 계층이라고 할 수 없다.

1. Penguin의 fly 메서드를 오버라이딩한 후 예외를 던지게 하는 것

```java
public class Penguin extends Bird {
	...
	@override
	public void fly(){
		throw new UnsupportedOperationException();
	}
}
```

이 경우 flyBird 메서드에 전달되는 인자의 타입에 따라 메서드가 실패하거나 성공하게 된다. 하지만 flyBird 메서드는 모든 bird가 날 수 있다고 가정하지 UnsupportedOperationException 예외가 던져질 것으로 기대하지는 않았을 것이다.

1. flyBird 메서드를 수정해서 인자로 전달된 bird의 타입이 Penguin이 아닐 경우에만 fly 메시지를 전송하도록 하는 것이다.

```java
public void flyBird(Bird bird){
  // 인자로 전달된 모든 bird가 Penguin의 인스턴스가 아닐 경우에만
	// fly() 메시지를 전송한다	
if(!(bird instanceof Penguin)){
	bird.fly();
	}
}
```

하지만 이 방법 역시 Penguin 이외에 날 수 없는 또다른 새가 상속 계층에 추가된다면 어떻게 할 것인지에 대한 문제가 남는다. → 새로운 타입을 추가할 때마다 코드 수정을 요구하기때문에 개방-폐쇄 원칙을 위반한다.

**문제해결 방법**

클라이언트의 기대에 맞게 상속 계층을 분리

```java
public class Bird{
 ...
}

public class FlyingBird extends Bird {
 public void fly() { ... }
 ...
}

public class Penguin extends Bird{
 bird.fly();
}

```

다음 코드는 새에 날 수 없는 새와 날 수 있는 새 두 부류가 존재하며, 펭귄은 날 수 없는 새에 속한다는 사실을 표현한다.

```java
public void flyBird(FlyingBird bird){
 bird.fly();
}
```

이제 flyBird 메서드는 FlyingBird 타입을 이용해 날 수 있는 새만 인자로 전달돼야 한다는 사실을 코드로 명시할 수 있다.

<img width="728" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/a0037445-9598-49b2-9722-9911b4522d53">


→행동 호환성을 만족 시키며 이제 FlyingBird 타입의 인스턴스만이 fly 메시지를 수신할 수 있다.

이 문제를 해결하는 다른 방법은 클라이언트에 따라 인터페이스를 분리하는 것이다.

<img width="724" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/902c1c20-8a18-4357-97a5-dafc3ad2647a">


- 하나의 클라이언트가 오직 fly 메시지만 전송하기 원한다면 이 클라이언트에게는 fly 메시지만 보여야한다
- 다른 클라이언트가 오직 walk 메시지만 전송하기 원한다면 이 클라이언트에게는 walk 메시지만 보여야한다

→ fly 오퍼레이션을 가진 Flyer 인터페이스와 walk 오퍼레이션을 가진 Walker 인터페이스로 분리하는 것이다.

이제 Bird와 Penguin은 자신이 수행할 수 있는 인터페이스만 구현할 수 있다.

**만약 Penguin이 Bird의 코드를 재사용해야 한다면 어떻게 해야할까?**

<img width="728" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/dc4dc553-ae3f-4fea-8fd6-2635cc1b70ff">


Penguin이 하나의 인터페이스만 구현하고 있기 때문에 문법상으로는 Penguin이 Bird를 상속받더라도 문제가 안 되겠지만 Penguin의 퍼블릭인터페이스에 fly 오퍼레이션이 추가되기 때문에 이 방법을 사용할 수는 없다.

→ 합성을 사용하는 것이 낫다

**이러한 설계의 장점**

Client1의 기대가 바뀌어서 Flyer의 인터페이스가 변경돼야 한다고 가정해보자. 이 경우 Flyer에 의존하고 있는 Bird가 영향을 받지만 변경의 영향은 Bird에서 끝난다.

이처럼 인터페이스를 클라이언트의 기대에 따라 분리함으로써 변경에 의해 영향을 제어하는 설계 원칙을 **인터페이스 분리 원칙**이라고 부른다

**명심**

자연어에 현혹되지 말고 요구사항 속에서 클라이언트가 기대하는 행동에 집중하라.

### 서브클래싱과 서브타이핑

언제 상속을 사용하고 어떤 상속이 올바른지에 대한 고민된다.

그래서 나온게 상속을 사용하는 **두 가지 목적**

- 서브클래싱: 다른 클래스의 코드를 재사용할 목적으로 상속을 사용하는 경우를 가리킨다.
    - 자식 클래스와 부모 클래스의 행동이 호환되지 않기 때문에 자식 클래스의 인스턴스가 부모 클래스의 인스턴스를 대체할 수 없다.
    - 서브 클래싱을 **구현 상속** 또는 **클래스 상속**이라고 부르기도 한다.
- 서브타이핑: 타입 계층을 구성하기 위해 상속을 사용하는 경우를 가리킨다.
    - 자식 클래스와 부모 클래스의 행동이 호환되기 때문에 자식 클래스의 인스턴스가 부모 클래스의 인스턴스를 대체할 수 있다.
    - 이때 **부모 클래스는 자식 클래스의 슈퍼타입**이되고 **자식 클래스는 부모 클래스의 서브타입**이 된다.
    - 이를 **인터페이스 상속**이라고 부르기도 한다.
    - **행동 호환성**을 만족시키며 이는 부모 클래스에 대한 자식 클래스의 **대체 가능성**을 의미한다.

행동 호환성과 대체 가능성을 따르는 지침 → 리스코프 치환 원칙

### 리스코프 치환 원칙

**개념**

- “서브타입은 그것의 기반 타입에 대해 대체 가능해야 한다.”는 것으로 클라이언트가 “차이점을 인식하지 못한 채 기반 클래스의 인터페이스를 통해 서브클래스를 사용할 수 있어야 한다"는 것이다.
- 앞에서 논의한 행동 호환성을 설계 원칙으로 정리한 것이다. 자식 클래스가 부모 클래스와 행동 호환성을 유지함으로써 부모 클래스를 대체할 수 있도록 구현된 상속 관계만을 서브타이핑이라고 불러야한다.

10장에서 살펴본 Stack과 Vector는 리스코프 치환 원칙을 위반하는 전형적인 예다. 클라이언트가 부모 클래스인 Vector에 대해 기대하는 행동을 Stack에 대해서는 기대할 수 없기 때문에 행동 호환성을 만족시키지 않기 때문이다. is - a 관계의 애매모호함을 설명하기 위해 예로 들었던 Penguin과 Bird 역시 리스코프 치환 원칙을 위반한다.

```java
public class Rectangle{
	private int x, y, width, height;
	
	public Rectangle(int x, int y, int width, int height){
		this.x = x;
		this.y = y;
		this.width = width;
		this.height = hieght;
		}
	public int getWidth(){
		return width;
	}

	public void setWidth(int width){
		this.width = width;
	}

	public int getHeight(){
		return height;
	}

	public int setHeight(int height){
		this.height = height;
	}		

	public int getArea(){
		return width * height;
	}
```

이 어플리케이션에 Square를 추가하자. 정사각형은 직사각형의 특수한 경우이고 직사각형은 정사각형의 일반적인 경우이기 때문에 정사각형과 직사각형은 어휘적으로 is-a 관계가 성립한다.

<img width="722" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/e5e0e1a0-d596-4cb0-91e3-d763086a0600">


정사각형은 너비와 높이가 동일해야 한다. 따라서 Square 클래스는 width와 height를 동일하게 설정해야 한다. 구현된 Square 클래스는 Square 제약 사항을 강제할 수 있도록 생성자에서 width 하나만 인자로 취하며 height의 값을 width와 동일한 값으로 설정한다. 

```java
public class Square extends Rectangle{
	public Square(int x, int y, int size){
		super(x, y, size, size);
		}
	
	@Override
	public void setWidth(int width){
		super.setwidth(width);
		super.setHeight(width);
	}

	@Override
	public void setHeight(int height){
		super.setwidth(height);
		super.setHeight(height);
	}
```

Square는 Rectangle의 자식 클래스이기 때문에 Rectangle이 사용되는 모든 곳에서 Rectangle로 업캐스팅 될 수 있다. 이때 Rectangle과 협력하는 클라이언트는 직사각형의 너비와 높이가 다르다고 가정한다.

```java
public void resize(Rectangle rectangle, int width, int height){
		rectangle.setwidth(width);
		rectangle.setHeight(width);
		assert rectangle.getWidth() == width && rectangle.getHeight() == height; 
	}
```

그러나 위 코드에서 resize 메서드의 인자로 Rectangle 대신 Sqaure를 전달한다고 가정해보자. 위 코드에서는 Square의 너비와 높이는 항상 더 나중에 설정된 height 값으로 설정된다. 따라서 다음과 같이 width와 height 값을 다르게 설정할 경우 메서드 실행이 실패할 것이다.

```java
Square square = new Sqaure(10, 10, 10);
resize(square, 50, 100);
```

**결론**

- Square가 Rectangle의 서브타입이라고 입을 모을 것이나 클라이언트와의 협력 관계 속으로 모델을 밀어 넣는 순간 서브타입이 올바르지 않다는 사실을 깨닫게 될 것이다.
    - resize 메서드의 관점에서 Rectangle 대신 Square를 사용할 수 없기 때문에 Square는 Rectangle이 아니다.
- 위 관계는 서브클래싱 관계이며  is-a라는 말이 우리의 직관에서 벗어난다.
- 대체 가능성을 결정하는 것은 클라이언트이며 상속이 서브타이핑을 위해 사용될 경우에만 is-a 관계이다.

**리스코프 치환 원칙은 유연한 설계의 기반이다**

리스코프 치환 원칙은 클라이언트가 어떤 자식 클래스와도 안정적으로 협력할 수 있는 상속 구조를 구현할 수 있는 가이드 라인을 제공하며, 클라이언트 입장에서 퍼블릭 인터페이스의 행동 방식이 변경되지 않는다면 클라이언트의 코드를 변경하지 않고도 새로운 자식 클래스와 협력할 수 있게 된다.

**소스코드(8장 중복할인 정책)**

```java
public class OverlappedDiscountPolicy extends DiscountPolicy {
    private List<DiscountPolicy> discountPolicies = new ArrayList<>();

    public OverlappedDiscountPolicy(DiscountPolicy ... discountPolicies) {
        this. discountPolicies = Arrays.asList(discountPolicies);
    }

    @Override
    protected Money getDiscountAmount(Screening screening) {
        Money result = Money.ZERO;
        for(DiscountPolicy each : discountPolicies) {
            result = result.plus(each.calculateDiscountAmount(screening));
        }
        return result;
    }
}
```

→ 중복할인 정책을 구현하기 위해 기존의 DiscountPolicy 상속 계층에 새로운 자식 클래스인OverlappedDiscountPolicy를 추가하더라도 클라이언트를 수정할 필요가 없다.

사실 위의 설계는 **의존성 역전 원칙과 개방-폐쇄 원칙, 리스코프 치환 원칙**이 한데 어우러져 설계를 확장가능하게 만든 대표적인 예이다.

의존성 역전 원칙: 구체 클래스인 Movie와 OverlappedDiscountPolicy 모두 추상 클래스인 DiscountPolicy에 의존한다. **상위 수준의 모듈인 Movie와 하위 수준의 모듈인 OverlappedDiscountPolicy는 모두 추상 클래스인 DiscountPolicy에 의존한다.** 따라서 이 설계는 DIP를 만족한다.

리스코프 치환 원칙: DiscoutPolicy와 협력하는 Movie의 관점에서 DiscountPolicy 대신 OverlappedDiscountPolicy와 협력하더라도 아무런 문제가 없다. **다시 말해 OverlappedDiscountPolicy는 클라이언트에 대한 영향 없이도 DiscouintPolicy를 대체할 수 있다.** 따라서 이 설계는 LSP를 만족한다.

개방-폐쇄 원칙: 중복 할인 정책이라는 새로운 기능을 추가하기 위해 DiscountPolicy의 자식 클래스인 Overlapped DiscountPolicy를 추가하더라도 Movie에는 영향을 끼치지 않는다. **다시 말해서 기능 확장을 하면서 기존 코드를 수정할 필요는 없다.** 따라서 이 설계는 OCP를 만족한다.

리스코프 치환 원칙이 개방-폐쇄 원칙을 어떻게 지원하는지 눈여겨 보길 바란다. 자식 클래스가 클라이언트의 관점에서 부모 클래스를 대체할 수 있다면 기능 확장을 위해 자식 클래스를 추가하더라도 코드를 수정할 필요가 없어진다. 따라서 **리스코프 치환 원칙은 개방-폐쇄 원칙을 만족하는 설계를 위한 전제 조건**인다.

<img width="727" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/3c7658b5-4ab0-41fb-8fcf-9b22b6a0f97a">


**타입 계층과 리스코프 치환 원칙**

클래스 상속은 타입 계층을 구현할 수 있는 다양한 방법 중 하나일 뿐이며 클래스 상속을 사용하지 않고 서브타이핑 관계를 구현 가능하다.

그렇다면… 클라이언트 관점에서 자식 클래스가 부모 클래스를 대체할 수 있다는 것은 무엇을 의미하는가?

### 계약에 의한 설계와 서브타이핑

클라이언트와 서버 사이의 협력을 의무와 이익으로 구성된 계약의 관점에서 표현하는 것을 **계약에 의한 설계**라고 부른다.

**계약에 의한 설계**

- 사전조건
    - 클라이언트가 정상적으로 메서드를 실행하기 위해 만족시켜야 하는 것
- 사후조건
    - 메서드가 실행된 후에 서버가 클라이언트에게 보장해야 하는 것
- 클래스 불변식
    - 메서드 실행 전과 실행 후에 인스턴스가 만족시켜야 하는 클래스 불변식

리스코프 치환 원칙과 계약에 의한 설계 사이의 관계를 다음과 같은 한 문장으로 요약할 수 있다.

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        this.title = title;
        this.runningTime = runningTime;
        this.fee = fee;
        this.discountPolicy = discountPolicy;
    }

    public Money getFee() {
        return fee;
    }

    public Money calculateMovieFee(Screening screening) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}

```

Movie는 DiscountPolicy의 인스턴스에게 calculateDiscountAmount 메시지를 전송하는 클라이언트다. DiscountPolicy는 Movie의 메시지를 수신한 후 할인 가격을 계산해서 반환한다.

```java
public abstract class DiscountPolicy {
    public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }
        return screening.getMovieFee();
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```

계약에 의한 설계에 따르면 협력하는 클라이언트와 슈퍼타입의 인스턴스 사이에는 어떤 계약이 맺어져있다. 클라이언트와 슈퍼타입은 이 계약을 준수할 때만 정상적으로 협력할 수 있다.

리스코프 치환 원칙은 서브타입이 그것의 슈퍼타입을 대체할 수 있어야 하고 클라이언트가 차이점을 인식하지 못한 채 슈퍼타입의 인터페이스를 이용해 서브타입과 협력할 수 있어야 한다고 말한다. 클라이언트의 입장에서 서브타입은 정말 슈퍼타입의 ‘한 종류'여야 하는 것이다.

**코드의 암묵적인 조건**

DiscountPolicy의 calculateDiscountAmount 메서드는 인자로 전달된 screening이 null인지 여부를 확인하지 않는다. 하지만 screening에 null이 전달된다면 screening.getMovieFee()가 실행될 때 NullPointerException 예외가 던져질 것이다.

Screening에 null이 전달되는 것은 우리가 기대했던 것이 아니다. 왜냐하면 calculateDiscountAmount 메서드는 클라이언트가 전달하는 screening의 값이 null이 아니고 영화 시작 시간이 아직 지나지 않았다고 가정할 것이기 때문. assert을  사용해 다음과 같이 사전조건을 표현할 수 있다.

```java
assert screening != null && screening.getStartTime().isAfter(LocalDateTime.now());
```

Movie의 calculateMovieFee 메서드를 살펴보면 DiscountPolicy의 calculateDiscountAmount 메서드의 반환값에 어떤 처리도 하지 않고 fee에서 차감하고 있음을 알 수 있다. 따라서 calculateDiscountAmount 메서드의 반환 값은 항상 null이 아니어야 한다. 사후조건은 다음과 같다.

```java
assert amount != null && amount.isGreaterThanOrEqual(Money.ZERO);
```

다음 코드는 calculateDiscountAmount 메서드에 사전조건과 사후조건을 추가한 것이다. 사전조건은 checkPrecondition 메서드로, 사후조건은 checkPostcondition 메서드로 구현돼 있다.

```java
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening) {
        checkPrecondition(screening);

        Money amount = Money.ZERO;
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                amount = getDiscountAmount(screening);
                checkPostcondition(amount);
                return amount;
            }
        }

        amount = screening.getMovieFee();
        checkPostcondition(amount);
        return amount;
    }

    protected void checkPrecondition(Screening screening) {
        assert screening != null &&
                screening.getStartTime().isAfter(LocalDateTime.now());
    }

    protected void checkPostcondition(Money amount) {
        assert amount != null && amount.isGreaterThanOrEqual(Money.ZERO);
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```

calculateDiscountAmount 메서드가 정의한 사전조건을 만족시키는 것은 Movie의 책임이다. 따라서 Movie는 사전조건을 위반하는 screening을 전달해서는 안 된다.

```java
public Money calculateMovieFee(Screening screening) {
        if (screening == null ||
                screening.getStartTime().isBefore(LocalDateTime.now())) {
            throw new InvalidScreeningException();
        }

        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
```

<img width="719" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/88ad2f80-8bc9-48fe-9daf-38227b7f820f">


위 그림에 표현된 DiscountPolicy의 자식 클래스인 AmountDiscountPolicy, percentDiscountPolicy, OverlappedDiscounPolicy는 Movie와 DiscountPolicy 사이에 체결된 계약을 만족시키기에 Movie 입장에서 이 클래스들은 DiscountPolicy를 대체할 수 있기 때문에 서브타이핑 관계라고 할 수 있다.

### 서브타입과 계약

계약의 관점에서 상속이 초래하는 가장 큰 문제는 자식 클래스가 부모 클래스의 메서드를 오버라이딩할 수 있다는 것이다.

**이때 기존 사전조건보다 더 강력한 사전조건을 정의한다고 해보자.**

```java
public class BrokenDiscountPolicy extends DiscountPolicy {

    public BrokenDiscountPolicy(DiscountCondition... conditions) {
        super(conditions);
    }
	
		@Override
    public Money calculateDiscountAmount(Screening screening) {
        checkPrecondition(screening);             // 기존의 사전조건
				checkStrongerPrecondition(screening);     // 더 강력한 사전조건

        Money amount = screening.getMovieFee();
        checkPostcondition(amount);               // 기존의 사후조건
        return amount;
    }

    private void checkStrongerPrecondition(Screening screening) {
	    assert screening.getEndTime().toLocalTime().isBefore(LocalTime.MIDNIGHT);
		}
		
		@Override
    protected Money getDiscountAmount(Screening Screening){
			return Money.ZERO;
		}
}
```

BrokenDiscountPolicy 클래스가 DiscountPolicy 클래스의 자식 클래스이기 때문에 컴파일러는 아무런 제약 없이 업캐스팅을 허용한다. 

문제는 Movie가 오직 DiscountPolicy의 사전조건만 알고 있다는 점이다. Movie는 DiscountPolicy가 정의하고 있는 사전조건을 만족시키기 위해 null이 아니면서 시작시간이 현재 시간 이후인 Screening을 전달할 것이다. 따라서 자정이 지난 후에 종료되는 Screening을 전달하더라도 문제가 없을 것이라고 가정할 것이다.

안타깝게도 BrokenDiscountPolicy의 사전조건을 이를 허용하지 않기 때문에 협력은 실패하고 만다.

- 서브타입에 더 강력한 사전조건을 정의할 수 없다.

**그렇다면 반대로 사전조건을 제거해서 약화시킨다면 어떻게 될까?**

```java
public class BrokenDiscountPolicy extends DiscountPolicy {

    public BrokenDiscountPolicy(DiscountCondition... conditions) {
        super(conditions);
    }
	
		@Override
    public Money calculateDiscountAmount(Screening screening) {
        // checkPrecondition(screening);             // 기존의 사전조건 제거

        Money amount = screening.getMovieFee();
        checkPostcondition(amount);               // 기존의 사후조건
        return amount;
    }

    private void checkStrongerPrecondition(Screening screening) {
	    assert screening.getEndTime().toLocalTime().isBefore(LocalTime.MIDNIGHT);
		}
		
		@Override
    protected Money getDiscountAmount(Screening Screening){
			return Money.ZERO;
		}
}
```

BrokenDiscountPolicy는 Screening에 대한 사전조건을 체크하지 않지만 Movie는 DiscountPolicy가 정의한 사전조건을 만족시키기 위해 null이 아니며 현재 시간 이후에 시작하는 Screening을 전달한다는 것을 보장하고 있다. 클라이언트는 이미 자신의 의무를 충실히 수행하고 있기 때문에 이 조건을 체크하지 않는 것이 기존 협력에 어떤 영향도 미치지 않는다. 이 경우 아무런 문제도 발생하지 않는 것이다.

- 서브타입에 슈퍼타입과 같거나 더 약한 사전조건을 정의할 수 있다.

**만약 사후조건을 강화한다면 어떨까?**

```java
public class BrokenDiscountPolicy extends DiscountPolicy {

    public BrokenDiscountPolicy(DiscountCondition... conditions) {
        super(conditions);
    }
	
		@Override
    public Money calculateDiscountAmount(Screening screening) {
        checkPrecondition(screening);             // 기존의 사전조건

        Money amount = screening.getMovieFee();
        checkPostcondition(amount);               // 기존의 사후조건
        checkStrongerPostcondition(amount);       // 더 강력한 사후조건

        return amount;
    }

    private void checkStrongerPostcondition(Money amount) {
	    assert amount.isGreaterThanOrEqual(Money.wons(1000));
		}
	}
```

amount가 null이 아니고 최소 1000원 이상은 돼야한다는 새로운 사후조건을 추가.

Movie는 DiscountPolicy의 사후조건만 알고 있다. 따라서 최소한 0원보다 큰 금액을 반환받기만 하면 협력이 정상적으로 수행됐다고 가정한다. 따라서 BrokenDiscountPolicy가 1000원 이상의 금액을 반환하는 것은 Movie와 DiscountPolicy 사이에 체결된 계약을 위반하지 않는다.

- 서브타입에 슈퍼타입과 같거나 더 강한 사후조건을 정의할 수 있다.

**만약 사후조건을 약화한다면 어떨까?**

```java
public class BrokenDiscountPolicy extends DiscountPolicy {

    public BrokenDiscountPolicy(DiscountCondition... conditions) {
        super(conditions);
    }
	
		@Override
    public Money calculateDiscountAmount(Screening screening) {
        checkPrecondition(screening);             // 기존의 사전조건

        Money amount = screening.getMovieFee();
        
				// checkPostcondition(amount);          // 기존의 사후조건
        checkWeakerPostcondition(amount);       // 더 약한 사후조건

        return amount;
    }

    private void checkWeakerPostcondition(Money amount) {
	    assert amount != null;
		}
	}
```

변경된 코드에서는 요금 계산 결과가 마이너스라도 그대로 반환할 것이다. Movie는 자신이 협력하는 객체가 DiscountPolicy의 인스턴스라고 생각하기 때문에 반환된 금액이 0원보다는 크다고 믿고 예매 요금으로 사용할 것이다. 이것은 예매 금액으로 마이너스 금액이 설정되는, 원하지 않았던 결과로 이어지고 만다. 이 예로부터 다음과 같은 사실을 알 수 있다.

- 서브타입에 더 약한 사후조건을 정의할 수 없다.
