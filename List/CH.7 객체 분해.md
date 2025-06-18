# CH.7 객체 분해

‘객체 분해'에서는 추상화의 한 가지 방법인 분해의 역사를 다룬다. 프로시저 추상화와 데이터 추상화 사이의 갈등과 분쟁의 역사를 이해하면 기능 분해에서 시작해서 객체지향에 이르기까지 소프트웨어 패러다임의 변화를 자연스럽게 이해하게 될 것이다.

# 프로시저 추상화와 데이터 추상화
**개념**
- 프로시저 추상화: 소프트웨어가 **무엇을 해야하는지** 추상화
- 데이터 추상화: 소프트웨어가 **무엇을 알아야 하는지** 추상화

**시스템 분해 방법**
1. 프로시저 추상화
   1. (프로시저) 기능 분해(알고리즘 분해)
2. 데이터 추상화
   1. (데이터) 타입 추상화 - 추상 데이터 타입
   2. (데이터) 프로시저 추상화 - 객체 지향

**객체 지향:** **프로그래밍 언어 관점**
-> 데이터 추상화와 (데이터 중심)프로시저 추상화를 함께 포함한 클래스를 이용해 시스템을 분해

# 프로시저 추상화와 기능 분해
## **시스템 분해 방법의 역사: 기능 (승) vs 데이터 (패)**
- 기능은 오랜시간 동안 시스템을 분해하기 위한 기준으로 사용됨
- 기능 분해(알고리즘 분해)의 관점에서 추상화의 단위는 프로시저
  - 내부의 상세한 구현을 몰라도 인터페이스만 알면 프로시저를 사용할 수 있기에 **추상화**라고 부름
  - 시스템은 프로시저를 단위로 분해됨
  - 정보 은닉의 가능성을 제시하지만 한계가 있다.
- 전통적인 기능 분해 방법은 하향식 접근법

## **기능 분해 방법** **예제: 급여 관리 시스템**
~~~
급여 = 기본급 - ( 기본급 * 소득세율)
~~~

**기능 분해를 위한 3단계 접근**
~~~
1. 최상위 문장 기술
   1. 직원의 급여를 계산한다
~~~

2. 좀 더 세분화된 절차로 구체화
~~~
1. 직원의 급여를 계산한다
   1. 사용자로부터 소득세율을 입력받는다.
   2. 직원의 급여를 계산한다
   3. 양식에 맞게 결과를 출력한다
~~~

2. 이전 문장의 추상화 수준을 감소 시킨다 (반복)
~~~
1. 직원의 급여를 계산한다
   1. 사용자로부터 소득세율을 입력받는다.
      1. ”세율을 입력하세요: ”라는 문장을 화면에 출력한다
      2. 키보드를 통해 세율을 입력받는다. 
   2. 직원의 급여를 계산한다
      1. 전역 변수에 저장된 직원의 기본급 정보를 얻는다
      2. 급여를 계산한다
   3. 양식에 맞게 결과를 출력한다
      1. ”이름: {직원명}, 급여: {계산된 금액}” 형식에 따라 출력 문자열을 생성
~~~
**짚고 넘어갈 부분**
- ‘급여 관리 시스템’을 입력 받아 출력을 생성하는 **커다란 하나의** **메인 함수로 간주**하고 기능 분해
- 기능이 우선이고, 데이터는 기능의 뒤를 따름 -> **유지 보수에 다양한 문제 야기**

## **기능 분해 방법** **예제: 급여 관리 시스템**

```ruby
#encoding: UTF-8
$employees = ["직원A", "직원B", "직원C"]
$basePays = [400, 300, 250]

def main(name)
  taxRate = getTaxRate()
  pay = calculatePayFor(name, taxRate)
  puts(describeResult(name, pay))
end

def getTaxRate()
  print("세율을 입력하세요: ")
  return gets().chomp().to_f()
end

def calculatePayFor(name, taxRate)
  index = $employees.index(name)
  basePay = $basePays[index]  
  return basePay - (basePay * taxRate)
end

def describeResult(name, pay)
  return "이름 : #{name}, 급여 : #{pay}"
end

main("직원A")
```

**트리 형태로 표현한 급여 관리 시스템** 

<img width="699" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c73f13f3-4501-4db9-b5c2-f3562dd3ac9b">


- 구조적, 체계적, 논리적인 개발 절차를 제시

## 하향식 기능 분해의 문제점
**이러한 설계는 겉으로는 이상적이나, 실제 적용시 다음의 문제가 발생할 수 있다.**
1. 시스템은 하나의 메인 함수로 구성되어 있지 않다.
   1. 시간이 지나면서 점점 새로운 요구사항이 추가된다.
   2. 결국 처음에는 중요하게 생각했던 메인 함수는 여러개의 동등한 수준의 함수 집합으로 성장하게 될 것이다.
2. 기능 추가나 요구사항 변경으로 인해 메인 함수를 빈번하게 수정해야 한다.
   1. 새로운 기능을 추가할 때 마다 매번 메인 함수를 수정해야한다.
3. 비즈니스 로직이 사용자 인터페이스와 강하게 결합된다.
   1. 사용자 인터페이스는 변경이 잦고, 비즈니스 로직은 변경이 드물다.
4. 하향식 분해는 너무 이른 시기에 함수들의 실행 순서를 고정시키기 때문에 유연성과 재사용성이 저하된다.
   1. 메인 함수가 작은 함수로 분해되기 위해서는 우선 함수들의 순서를 결정해야 하기 때문이다.
5. **데이터 형식이 변경될 경우 파급효과를 예측할 수 없다.(가장 큰 문제)**
   1. 어떤 데이터를 어떤 함수가 사용하는지 추적하기가 어렵다. 따라서 데이터 변경시 어떤 함수가 영향을 받을지 예상하기 어렵다.
-> 근본적인 문제: 변경에 취약한 설계

## 언제 하향식 분해가 유용한가?
설계가 어느 정도 **안정화**된 후에는 설계의 **다양한 측면을 논리적으로 설명하고 문서화**하기에 용이하다.
## 모듈
## 정보 은닉과 모듈
정보은닉 : 외부에 감춰야 하는 비밀(복잡성, 변경 가능성)에 따라 시스템을 분할하는 모듈 분할의 원리
시스템을 모듈 단위로 분해하기 위한 기본 원리로 시스템에서 자주 변경되는 부분을 상대적으로 덜 변경되는 안정적인 인터페이스 뒤로 감춰야 한다는 것이 핵심 → 높은 응집도, 낮은 결함도

**모듈의 장점과 한계**
**장점:**
- 모듈 내부의 변수가 변경되더라도 모듈 내부에만 영향을 미친다.
  - 어떤 데이터가 변경되었을때 영향을 받는 함수를 찾기 위해 해당 데이터를 정의한 모듈만 검색하면 된다. -> 파급효과 제어 가능
- 비지니스 로직과 사용자 인터페이스에 대한 관심사를 분리한다.
  - 상대적으로 비지니스 로직은 잘 변하지 않으며 인터페이스는 잘 변한다. 모듈로 생성함으로서 사용자 인터페이스에 대한 요구는 main에서처리 하면 된다.
- 전역 변수와 전역 함수를 제거함으로서 네임스페이스 오염(namespace pollution)을 방지한다.
  - 변수와 함수를 모듈 내부에 포함시키기 때문에 다른 모듈에서도 동일한 이름을 사용할 수 있게 된다.

**한계:** 
- 정보 은닉이라는 개념을 통해 데이터라는 존재를 설계의 중심 요소로 부각 → 기능이 아니라 데이터를 중심으로 시스템을 분해→ 데이터와 함수가 통합된 한 차원 높은 추상화를 제공하는 설계 단위
- 인스턴스의 개념을 제공하지 않는다.

## 데이터 추상화와 추상 데이터 타입
타입(type): **변수에 저장할 수 있는 내용물의 종류와 변수에 적용될 수 있는 연산의 가짓수**
추상 데이터 타입을 구현하려면 다음과 같은 특성을 위한 프로그래밍 언어의 지원이 필요하다.
1. 타입 정의를 선언할 수 있어야 한다.
~~~java
public class Stack {
    // 내부 데이터 (은닉)
    private int[] elements;
    private int top = -1;
    private int size;

    // 생성자
    public Stack(int size) {
        this.size = size;
        this.elements = new int[size];
    }
}   
~~~
2. 타입의 인스턴스를 다르기 위해 사용할 수 있는 오퍼레이션의 집합을 정의할 수 있어야 한다.
~~~java
    public void push(int value) {
        if (top < size - 1) {
            elements[++top] = value;
        }
    }

    public int pop() {
        if (top >= 0) {
            return elements[top--];
        }
        throw new IllegalStateException("Stack is empty");
    }
~~~
3. 제공된 오퍼레이션을 통해서만 조작할 수 있도록 데이터를 외부로부터 보호할 수 있어야 한다.
~~~java
    // elements는 private이므로 외부에서 직접 접근 불가능
~~~
4. 타입에 대해 여러 개의 인스턴스를 생성할 수 있어야 한다.
~~~java
public class Main {
    public static void main(String[] args) {
        Stack s1 = new Stack(10);
        Stack s2 = new Stack(5);

        s1.push(1);
        s2.push(100);
    }
}
~~~

추상 데이터 타입을 이용한 급여 관리 시스템 개선
1. 내부의 캡슐화할 데이터 결정
```ruby
Employee = Struct.new(:new, :basePay, :hourly,:timeCard) do
End
```

1. 오퍼레이션 결정
```ruby
Employee = Struct.new(:new, :basePay, :hourly,:timeCard) do
	def calculatePay(taxRate)
		if(hourly) then
			return calculateHourlyPay(taxRate)
		end
		return calculateSalariedPay(taxRate)
	end

private
	def caculateHourlyPay(taxRate)
		return (basePay * timeCard) - (basePay * timeCard) * taxRate
	end

	def caculateSalariedPay(tzxRate)
		return basePay - (basePay * taxRate)
	end
end

Employee = Struct.new(:new, :basePay, :hourly,:timeCard) do
	def monthlyBasePay()
		if(hourly) then return 0 end
		return basePay
	end
end

```

클라이언트 코드
```ruby
$employees =[
	Employee.new("직원A",400,false,0),
	Employee.new("직원B",400,false,0),
	Employee.new("직원C",400,false,0),
	Employee.new("아르바이트A",1,true,120),
	Employee.new("아르바이트B",1,true,120),
	Employee.new("아르바이트C",1,true,120)
]

def caculatePay(name)
	taxRate = getTaxRate()
	for each in $employees
		if (each.name == name) then employee = each; break end
	end
	pay = employee.cacluatePay(taxRate)
	puts(describeResult(name, pay))
end

def sumOfBasePays()
	result = 0
	for each in $employees
		result += each.monthlyBasePay()
	end
puts(result)
end
```
-> 이전에 비해 많이 개선 되었다. 하지만 여전히 데이터와 기능을 분리해서 바라보고 있다. 즉, 추상 데이터 타입은 말 그대로 시스템의 상태를 저장할 데이터를 표현한다. 추상 데이터 타입으로 표현된 데이터를 이용해서 기능을 구현하는 핵심 로직은 추상 데이터 타입 외부에 존재한다.

여기서 드는 생각… 클래스는 추상 데이터 타입인가? 클래스는 무엇인가??

### 클래스
클래스는 추상 데이터 타입인가? **꼭 틀린말은 아니지만… 맞다고 하기도 애매**

**공통점**: 모두 외부에서는 객체의 내부 속성에 직접 접근할 수 없으며 오직 퍼브릭 인터페이스를 통해서만 외부와 의사소통 할 수 있음
**차이점**: 핵심적인 차이는 클래스는 상속과 다형성을 지원하는 데 비해 추상 데이터 타입은 지원하지 못한다는 점

쿸의 정의를 빌리자면 추상 데이터 타입은 타입을 추상화한 것이고 클래스는 절차를 추상화한것 이다.

**추상 데이터 타입이 오퍼레이션을 기준으로 타입을 묶는 방법이라면 객체지향은 타입을 기준으로 오퍼레이션을 묶는다.** 

추상 데이터 타입은 오퍼레이션을 기준으로 타입을 묶는다.
<img width="696" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/ef8c7328-0d83-4759-8348-5e132af4ae7c">


객체지향은 타입을 기준으로 오퍼레이션을 묶는다. 
<img width="696" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f5b90480-84c7-44c5-8f47-ab5d153bc0b1">


**추상 데이터 타입에서 클래스로 변경하기**
```ruby
class Employee
	attr_reader : name, basePay
	
	def initialize(name, basePay)
		@name = name
		@basePay = basePay
	end

	def calculatePay(taxRate)
		raise NotImplementedError
	end

	def monthlyBasePay()
		raise NotImplementedError
	end
end
```
```ruby
class SalariedEmployee < Employee
	def initialize(name, basePay)
			super(name, basePay)
		end
	
		def calculatePay(taxRate)
			return basePay - (basePay * taxRate)
		end
	
		def monthlyBasePay()
			return basePay
		end
	end
	
```
```ruby
class HourlyEmployee < Employee
	attr_reader :timeCard
	def initialize(name, basePay, timeCard)
			super(name, basePay)
			@timeCard = timeCard
		end
	
		def calculatePay(taxRate)
			return basePay * timeCard - (basePay * timeCard * taxRate)
		end
	
		def monthlyBasePay()
			return 0
		end
	end

```
```ruby
$employees =[
	SalariedEmployee.new("직원A",400,false,0),
	SalariedEmployee.new("직원B",400,false,0),
	SalariedEmployee.new("직원C",400,false,0),
	HourlyEmployee.new("아르바이트A",1,true,120),
	HourlyEmployee.new("아르바이트B",1,true,120),
	HourlyEmployee.new("아르바이트C",1,true,120)
]
```
객체를 생성하고 나서는 객체의 클래스가 무엇인지는 중요하지 않다. 클라이언트 입장에서는 두 인스턴스 모두 Employee의 인스턴스인 것 처럼 다룰 수 있다. 

항상 절차를 추상화 하는 객체 지향 설계 방식을 따라야 하는가? 추상 데이터 타입은 모든 경우에 최악의 선택인가? 

**변경을 기준으로 선택하라**
**단순히 클래스를 쓴다고 객체지향 프로그래밍이 아니다. 타입을 기준으로 절차를 추상화해야 객체지향 프로그래밍이다.** -> **객체지향 프로그래밍이란, 함수를 따로 두는 것이 아니라, 그 함수가 처리해야 할 데이터를 가진 타입(객체) 안에 로직을 넣는 것이다.**

설계는 변경과 관련된 것이다. 타입 추가라는 변경의 압력이 더 강한 경우에는 객체지향의 손을 들어줘야 한다. 추상 데이터 타입의 경우 새로운 타입을 추가하려면 타입을 체크하는 클라이언트 코드를 일일이 찾아 수정해야 하지만 객체지향의 경우에는 간단하게 새로운 클래스를 상속 계층에 추가하기만 하면 된다. 이에 반해 변경의 주된 압력이 오퍼레이션을 추가하는 것이라면 추상 데이터 타입의 승리를 선언해야 한다. 객체지향의 경우 새로운 오퍼레이션을 추가하기 위해서는 상속 계층에 속하는 모든 클래스를 한번에 수정해야 한다. 

→ 새로운 타입을 빈번하게 추가해야 한다면 객체지향의 클래스 구조
→ 새로운 오퍼레이션을 빈번하게 추가해야 한다면 추상데이터 타입
예시 코드
~~~java
// ✅ 객체지향 설계: 타입 추가에 유리, 연산 추가에 불리
abstract class Shape {
    // 모든 도형은 area() 연산을 제공해야 함
    abstract double area();
    // 연산 추가 시 → 여기 추가해야 하고,
    // ↓ 모든 하위 클래스에도 구현 필요
    // abstract void draw(); // 연산 추가에 불리
}

class Circle extends Shape {
    double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }

    // 연산 추가 시 여기도 수정 필요
    // @Override
    // void draw() { System.out.println("원을 그립니다."); }
}

class Rectangle extends Shape {
    double width, height;

    Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    double area() {
        return width * height;
    }

    // 연산 추가 시 여기도 수정 필요
    // @Override
    // void draw() { System.out.println("사각형을 그립니다."); }
}

// ▶ 새로운 타입 Triangle 추가 시
class Triangle extends Shape {
    double base, height;

    Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }

    @Override
    double area() {
        return base * height / 2;
    }

    // draw() 연산 추가 시 여기도 수정 필요
}

// → ✅ 새로운 타입 추가는 쉽다 (기존 코드 수정 거의 없음)
// → ❌ 연산 추가 시 모든 클래스에 메서드 추가 필요

// ------------------------------------------------------

class CircleADT {
    double radius;
}

class RectangleADT {
    double width, height;
}

class TriangleADT {
    double base, height;
}

class ShapeUtils {
    // ▶ ADT 스타일: 연산 추가에 유리, 타입 추가에 불리

    static double area(Object shape) {
        if (shape instanceof CircleADT) {
            CircleADT c = (CircleADT) shape;
            return Math.PI * c.radius * c.radius;
        } else if (shape instanceof RectangleADT) {
            RectangleADT r = (RectangleADT) shape;
            return r.width * r.height;
        } else if (shape instanceof TriangleADT) {
            TriangleADT t = (TriangleADT) shape;
            return t.base * t.height / 2;
        }
        throw new IllegalArgumentException("Unknown shape");
    }

    // ▶ 새로운 연산 추가 예: draw()
    static void draw(Object shape) {
        if (shape instanceof CircleADT) {
            System.out.println("원을 그립니다.");
        } else if (shape instanceof RectangleADT) {
            System.out.println("사각형을 그립니다.");
        } else if (shape instanceof TriangleADT) {
            System.out.println("삼각형을 그립니다.");
        }
    }

    // → ✅ 연산 추가는 간단히 함수만 하나 더 만들면 됨
    // → ❌ 새로운 타입 추가 시 모든 연산 함수에 조건 추가 필요
}

public class Main {
    public static void main(String[] args) {
        // 객체지향 방식
        Shape s1 = new Circle(3);
        Shape s2 = new Rectangle(2, 5);
        System.out.println(s1.area());
        System.out.println(s2.area());

        // ADT 방식
        Object c = new CircleADT(); 
        ((CircleADT) c).radius = 3;
        Object r = new RectangleADT(); 
        ((RectangleADT) r).width = 2; 
        ((RectangleADT) r).height = 5;

        System.out.println(ShapeUtils.area(c));
        System.out.println(ShapeUtils.area(r));

        ShapeUtils.draw(c);
        ShapeUtils.draw(r);
    }
}
~~~


**타입 계층과 다형성은 협력이라는 문맥 안에서 책임을 수행하는 방법에 관해 고민한 결과물이어야 하며 그 자체가 목적이 되어서는 안된다.** 

객체지향에서는 타입 변수를 이용한 조건문을 다형성으로 대체한다. 클라이언트가 객체의 타입을 확인한 후 적절한 메서드를 호출하는 것이 아니라 객체가 메세지를 처리할 적절한 메서드를 선택한다. 
<img width="691" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/b46fb835-aa6c-4f1d-a946-437b75cd04e2">

개방 - 폐쇄 원칙 (Open-Closed Principle, OCP) : 소프트웨어 개체(클래스, 모듈, 함수 등등)은 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.
```cpp
/* 클래스 */
public class Animal {
  ...
}
/* 객체와 인스턴스 */
public class Main {
  public static void main(String[] args) {
    Animal cat, dog; // '객체'

    // 인스턴스화
    cat = new Animal(); // cat은 Animal 클래스의 '인스턴스'(객체를 메모리에 할당)
    dog = new Animal(); // dog은 Animal 클래스의 '인스턴스'(객체를 메모리에 할당)
  }
}
https://gmlwjd9405.github.io/2018/09/17/class-object-instance.html
```