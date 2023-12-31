# Appendex C_동적인 협력, 정적인 코드

협력을 구성하기 위해서는 동적인 객체가 필요하다. 우리는 이것을 표현하기 위해 정적인 텍스트라는 도구를 이용한다. 프로그램은 고정된 텍스트라는 형식 안에 갇혀 있으면서도 객체의 모든 변화 가능성을 담아야 한다. 

이것은 프로그래머가 객체지향 프로그램을 작성하기 위해서는 동적 모델(프로그램 실행 구조를 표현하는 움직이는 모델)과 정적 모델(코드의 구조를 담는 고정된 모델)을 동시에 마음속에 그려야 한다는 것을 의미한다. 

동적 모델 - 객체는 다른 **객체**와 **협력**하면서 애플리케이션의 기능을 수행함.

정적 모델 - 타입과 관계로 구성됨.

두 가지 모델 중 우선순위가 되어야 하는 것은? 동적모델

정적 모델은 동적 모델에 의해 주도 돼야 하고 동적 모델이라는 토대 위에 세워져야 한다. 프로그램 코드 안에 담아지는 정적모델은 객체 사이의 협력에 기반해야 한다. 이것이 핵심이다. 

고려해야 할 점!! 바로 변경을 수용할 수 있도록 만들어야 함.

- 코드 응집도가 높고, 결합도가 낮으며 단순해서 쉽게 이해할 수 있는 코드
- 동일한 코드를 이용해 다양한 컨텍스트에서 동작 가능한 협력을 만들 수 있는 코드

## 동적 모델과 정적 모델

### 행동이 코드를 결정한다.

정적 모델을 미리 결정하고 객체의 행동을 정적 모델에 맞추면 안된다. 

객체가 외부에 제공하는 행동을 제외한 채 개념 사이의 관계에 기반해 두 객체의 정적 모델을 구상한다면 다음 그림과 같이 Penguin이 Bird의 자식 클래스로 구현할 것이다.

<img width="317" alt="Untitled" src="https://github.com/HJC96/Obejct/assets/87226129/12d08778-76e4-4d02-97c5-3db94c2a50d0">

하지만 fly라는 행동을 제공한다면 정적 모델의 구조는 다음과 같이 변경된다. 

<img width="317" alt="Untitled 1" src="https://github.com/HJC96/Obejct/assets/87226129/012e9ae6-9eea-46a1-9524-0967adbfa40c">


### 변경을 고려하라

핸드폰 과금 시스템의 초기 모델을 통해 변경을 고려하지 않고 정적 모델을 작성하면, 유지 보수하기 어려운 코드가 만들어지는 문제 보기

<img width="929" alt="Untitled 2" src="https://github.com/HJC96/Obejct/assets/87226129/fbb32b6a-ff3d-4c9a-b3ed-fe43038708ab">


초기 모델은 상속 계층에 속한 객체들이 협력 안에서 다양한 정책에 따라 요금을 계산하는 책임을 수행해야 한다는 객체의 행동을 잘 표현한다. 하지만 변경이라는 측면에서는 좋은 설계가 아니다. 이 설계는 다양한 정책을 조합하면 조합할수록 중복코드가 기하 급수적으로 증가한다. 게다가 유연하지도 않다. 요금정책을 변경하려면 인스턴스들 사이에 상태를 복사해야 하기 때문이다.

<img width="995" alt="Untitled 3" src="https://github.com/HJC96/Obejct/assets/87226129/028b5c19-a7b9-4fff-9506-90c577776568">


상속을 합성 계층으로 변경하여 요금 정책을 다양하게 조합하더라도 중복 코드가 발생하지 않게 하였고 요금 정책을 변경하고 싶다면 RatePolicy 인스턴스의 종류를 변경하기만 하며 된다. 

## 도메인 모델과 구현

### 도메인 모델에 관하여

객체 지향 분석 설계에서 제안하는 지침 중 하나는 소프트웨어의 도메인에 대해 고민하고 도메인 모델을 기반으로 소프트웨어를 구축하라는 것이다. 이 지침을 따르면 개념과 소프트웨어 사이의 표현적 차이를 줄일 수 있기 때문에 이해하고 수정하기 쉬운 소프트웨어를 만들 수 있다. 

어라!? 위에 내용이랑 반대 되지 않는가? 아뉭~

여기서 중요한 점은 도메인 모델을 작성하는 것이 목표가 아니라 출발점!이라는 것이다. 도메인 안에 개념들을 기반으로 출발하되 객체들의 협력이 도메인 모델에 맞지 않다면 몇 가지 개념만 남기고 도메인 모델을 과감히 수정하라

변경에 유연하게 대응할 수 있는 타입의 구현 방법 예제로 보기~

### 몬스터 설계하기

- 주인공 캐릭터를 공격하는 다양한 종류의 몬스터가 등장하는 게임을 설계해보자!
- 설계자들은 용과 트롤은 몬스터의 일종이기 때문에 다음과 같은 도메인 모델에서 출발하는 것이 적절하다고 결정했다!!
    - 모든 몬스터가 공격할 수 있다는 요구사항을 수용할 수 있다.
    - 계층에 속하는 모든 클래스들이 서브타입 관계를 만족하도록 구현할 수 있다.

<img width="765" alt="Untitled 4" src="https://github.com/HJC96/Obejct/assets/87226129/a302f986-0e68-4c7c-a7c4-ffc929366f94">


```java
public abstract class Monster {
	private int health;
	
	public Monster(int health) {
		this.health = health;
	}
	
	abstract public String getAttack();
}
```

```java
public class Dragon extends Monster {
	public Dragon() {
		super(230);
	}

	@Override
	public String getAttack() {
		return "용은 불을 내뿜는다";
	}
}
```

```java
public class Troll extends Monster {
	public Troll() {
		super(48);
	}

	@Override
	public String getAttack() {
		return "트롤은 곤봉으로 때린다";
	}
}
```

여기까지는 깔끔하지만, 새로운 몬스터를 추가해 달라는 요청이 들어오면 문제가 생긴다.

→ 몬스터별 하나의 클래스를 추가하고 getAttack 메서드를 오버라이딩하는 일은 꽤나 지루할 것.

→ 변경을 쉽게 지원하도록 수정해보자!

→ 현재 설계는 기존 코드를 수정하지 않고도 새로운 몬스터를 추가할 수 있기에 **개방 - 폐쇄 원칙**을 준수하는 좋은 설계이다. 하지만 더 좋은 방법은 새로운 클래스를 추가하지 않고도 새로운 몬스터를 추가하는 방법일 것이다!

→ 몬스터 종류별로 새로운 서브클래스를 추가하는 대신 몬스터가 품종을 가지도록 설계를 개선할 수 있다.

```java
public class Breed {
	private String name;
	private int health;
	private String attack;

	public Breed(String name, int health, String attack) {
		this.name = name;
		this.health = health;
		this.attack = attack;
	}

	public int getHealth() {
		return health;
	}

	public String getAttack() {
		return attack;
	}
}
```

각 몬스터 종류는 Breed 클래스의 인스턴스를 포함하는 Monster 클래스로 모델링할 수 있다.

```java
public class Monster {
	private int health;
	private Breed breed;
	
	public Monster(Breed breed) {
		this.health = breed.getHealth();
	}
	
	public String getAttack() {
		return breed.getAttack();
	}
}
```

```java
Monster dragon = new Monster(new Breed("용", 230, "용은 불은 내뿜는다"));
Monster troll = new Monster(new Breed("트롤", 48, "트롤은 곤봉으로 때린다"));
```

우리가 만드는 게임 안에 수백 가지 종류의 몬스터가 존재한다면, 각 종류별로 매번 새로운 클래스를 만들고 수정하는 일은 끔찍할 정도로 지루한 반복 작업일 것이다!!

다음  두 개의 클래스만으로 이 문제를 수정가능했다!

1. 시스템 안의 모든 몬스터가 수행해야 하는 **행동을 정의한 Monster 클래스**
2. 몬스터의 **타입을 정의하는 Breed 클래스**

<img width="786" alt="Untitled 5" src="https://github.com/HJC96/Obejct/assets/87226129/682d1fc8-3ef8-43c2-850c-116289484995">


이것은 상속 대신 합성을 사용하라는 설계 지침을 따르는 예이다. 여기서 합성을 사용한 이유는 Dragon이나 Troll 같은 새로운 Monster 타입이 추가될 때마다 새로운 클래스를 추가하고 싶지 않기 때문이다!

이처럼 어떤 인스턴스가 다른 인스턴스의 타입을 표현하는 방법을 **TYPE OBJECT 패턴**이라고 부른다!

### **행동과 변경을 고려한 도메인 모델**

**초기에 고안한 도메인 모델**

- 도메인 모델에 표현된 개념과 관계를 기반으로 협력에 필요한 객체의 후보를 도출
- 구현 클래스의 이름과 관계를 설계

**하지만 도메인 모델을 그대로 카피는 노노!** 왜냐하면 객체는 변경을 위한 것이기 때문에!  더 쉬운 모델이 떠올랐다면 과감하게 초기 아이디어를 버려라!!

그림 C.6은 C.5보다 다양한 종류의 몬스터 구조를 구현할 수 있기에 유용하다. 여기서 더 나아가 다음과 같이 몬스터의 종류를 JSON 형식으로 서술한다면 그림 C.6의 도메인 전체에 대해 이해하기가 더 쉬워질 것이다!

```java
{ "breeds" :
  [
    { "name" : "용" , "health" : 230, "attack" : "용은 불을 내뿜는다" },
    { "name" : "트롤", "health" : 48, "attack" : "트롤은 곤봉으로 때린다" }
  ]
}
```

간단한 JSON 역직렬화기를 구현하면 우리가 정리한 JSON 데이터를 실제 어플리케이션에서 사용할 수 있다.

또 다른 예… 핸드폰 과금 시스템의 기본 정책과 부가 정책!!

<img width="718" alt="Untitled 6" src="https://github.com/HJC96/Obejct/assets/87226129/6f8581ce-5aae-4c40-b431-a94b07df4fc0">


위 그림은 개념들의 분류 체계를 표현한 것이 아니라 요금제를 구성하는 각 요소들이 실제로 요금을 계산할 때 어떤 순서로 처리돼야 하는지를 표현한 것이다.

**다시 말해, 요금을 계산하는 실행 시점의 모습을 표현한 동적 모델이다…!!!!**

우리는 이 동적 모델을 가장 잘 표현할 수 있는 구조로 코드를 작성하려고 노력했고 그 결과물이 그림 C.4의 정적모델이다!!

**결론**

도메인 모델이 단순히 정적 모델의 형태를 띨 필요가 없으며 도메인 모델의 구조가 코드와 다를 필요가 없다는 것이다! 도메인 모델은 코드를 위한 것이다! 

중요한 것은 도메인 모델을 봤을 때 도메인의 개념 뿐만 아니라 코드도 함께 이해될 수 있는 구조를 찾는 것이다!

### 분석 모델, 설계 모델, 그리고 구현 모델

**각 모델의 개념**

- 분석 모델: 해결 방법에 대한 언급 없이 문제 도메인을 설명하는 모델
- 설계 모델: 분석 모델이 완성되면 이를 바탕으로 기술적인 관점에서 솔루션을 서술한 모델
- 구현 모델: 앞서 두 모델에서 만들어진 청사진을 기반으로 구현 모델을 만든다.

**도메인 모델이 코드와 동일한 형태를 가진다는 것**은 **분석, 설계, 구현에 걸쳐 동일한 모델을 사용한다는 것**이다!!

사실 객체지향 패러다임이 과거의 다른 패러다임과 구별되는 가장 큰 차이점은 **소프트웨어를 개발하기 위한 전체 주기 동안 동일한 설계 기법과 모델링 방법을 사용할 수 있다는 것**이다!!

**그 외 여러 팁들….(?)**

- 분석 모델과 설계 모델의 하이브리드한 특징이 가장 좋은 모델을 낳는 토양이라면 프로그래밍 언어를 사용해서 구현한 코드가 이 모델을 최대한 반영하는 것이 가장 이상적일 것이다.
    - 만약 설계 모델의 일부가 적용 기술 내에서 구현 불가능하다면 설계 모델을 변경해야 한다!
    - 프로그래밍은 설계의 한 과정이며 설계는 프로그래밍을 통해 개선된다!
- 프로젝트 내에서 분석 모델을 설계 모델로 변환하는 작업에 많은 시간을 소비하고 있다면 설계 모델을 도메인을 반영하도록 수정하고 분석 모델을 폐기처분하자!
    - 프로세스를 위한 맹목적인 작업에 개발자들의 소중한 시간과 체력을 낭비하고 있을 뿐이다
- 코드와 모델의 차이를 줄이기 위해서는 도메인과 코드 간의 차이가 적어야 한다!
    - 객체지향의 가장 큰 힘은 도메인을 표현하는 방법과 프로그램 코드를 표현하는 방법이 동일하다는 것이다!
- 분석 모델과 설계 모델과 구현 모델이 다르다는 생각을 버려라!
    - 도메인의 개념과 객체 사이의 협력을 잘 버무려 코드에 반영하기 위해 고민하고 프로그래밍하는 동안 분석과 설계와 구현에 대해 동시에 고민하고 있는 것이다!
