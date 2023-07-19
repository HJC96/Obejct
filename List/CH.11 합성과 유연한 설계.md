# CH.11 합성과 유연한 설계

https://docs.oracle.com/javase/8/docs/api/java/util/Set.html#add-E-

### **상속을 합성으로 변경하기**

**상속과 합성 비교**

|  | 의존성 해결 | 결합도 | 관계 |
| --- | --- | --- | --- |
| 상속 | 컴파일 타임 | 높음 | 클래스와 정적인 관계 |
| 합성 | 런 타임 | 낮음 | 객체 사이의 동적인 관계 |

**상속 남용시 직면할 수 있는 문제**

1. 불필요한 인터페이스 상속 문제
    1. 자식 클래스에게는 부적합한 부모 클래스의 오퍼레이션이 상속되기 때문에 자식 클래스 인스턴스의 상태가 불안정해짐

**상속을 합성으로 바꾸는 방법**

자식 클래스에 선언된 **상속 관계를 제거**하고 부모 클래스의 인스턴스를 **자식 클래스의 인스턴스 변수로 선언**

**해결 방법**

각 클래스의 인스턴스를 내부에 포함한 후 클래스의 퍼블릭 인터페이스에서 제공하는 오퍼레이션들을 이용해 필요한 기능들을 구현

**소스 코드**

```java
public class Properties {
    private Hashtable<String, String> properties = new Hashtable <>();

    public String setProperty(String key, String value) {
        return properties.put(key, value);
    }

    public String getProperty(String key) {
        return properties.get(key);
    }
}
```

```java
public class Stack<E> {
    private Vector<E> elements = new Vector<>();

    public E push(E item) {
        elements.addElement(item);
        return item;
    }

    public E pop() {
        if (elements.isEmpty()) {
            throw new EmptyStackException();
        }
        return elements.remove(elements.size() - 1);
    }
}
```

1. 메서드 오버라이딩의 오작용 문제
    1. 자식 클래스가 부모 클래스의 메서드를 오버라이딩할 때 자식 클래스가 부모 클래스의 메서드 호출 방법에 영향을 받는 문제
    

**소스 코드**

```java

public class InstrumentedHashSet<E> {
    private int addCount = 0;
    private Set<E> set;

    public InstrumentedHashSet(Set<E> set) {
        this.set = set;
    }

    public boolean add(E e) {
        addCount++;
        return set.add(e);
    }

    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

}
```

InstrumentedHashSet의 경우에는 HashSet이 제공하는 퍼블릭 인터페이스를 그대로 제공해야 하며,

HashSet에 대한 구현 결합도는 제거하면서도 퍼블릭 인터페이스는 그대로 상속 받을 수 있는 방법

```java

public class InstrumentedHashSet<E> implements Set<E> {
    private int addCount = 0;
    private Set<E> set;

    public InstrumentedHashSet(Set<E> set) {
        this.set = set;
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return set.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

    @Override public boolean remove(Object o) {
        return set.remove(o);
    }

    @Override public void clear() {
        set.clear();
    }

    @Override public boolean equals(Object o) {
        return set.equals(o);
    }

    @Override public int hashCode() {
        return set.hashCode();
    }

    @Override public Spliterator<E> spliterator() {
        return set.spliterator();
    }

    @Override public int size() {
        return set.size();
    }

    @Override public boolean isEmpty() {
        return set.isEmpty();
    }

    @Override public boolean contains(Object o) {
        return set.contains(o);
    }

    @Override public Iterator<E> iterator() {
        return set.iterator();
    }

    @Override public Object[] toArray() {
        return set.toArray();
    }

    @Override public <T> T[] toArray(T[] a) {
        return set.toArray(a);
    }

    @Override public boolean containsAll(Collection<?> c) {
        return set.containsAll(c);
    }

    @Override public boolean retainAll(Collection<?> c) {
        return set.retainAll(c);
    }

    @Override public boolean removeAll(Collection<?> c) {
        return set.removeAll(c);
    }
}
```

1. 부모 클래스와 자식 클래스의 동시 수정 문제
    1. 부모 클래스와 자식 클래스 사이의 개념적인 결합으로 인해 부모 클래스를 변경할 때 자식 클래스도 함께 변경해야 하는 문제

→ 해결 불가. 하지만 여전히 상속보다는 합성을 사용하는게 좋다

→ 향후 playlist의 내부 구현을 변경하더라도 파급효과를 최대한 PersonalPlaylist 내부로 캡슐화할 수 있기 때문에.

**결론**

→ 합성은 상속과 비교했을때 코드를 안정적으로 유지해준다.

### **상속으로 인한 조합의 폭발적인 증가**

상속으로 인해 결합도가 높아졌을때 발생하는 문제점

- 하나의 기능을 추가하거나 수정하기 위해 **불필요하게 많은 수의 클래스를 추가하거나 수정**해야 한다.
- 단일 상속만 지원하는 언어에서는 상속으로 인해 오히려 **중복 코드의 양이 늘어날 수 있다**.

**(예제)**

**상속을 이용해 기본 정책 구현하기**

⇒ RegularPhone과 NightlyDiscountPhone의 인스턴스만 단독으로 생성한다는 것은 부가 정책은 적용하지 않고 오직 기본 정책으로만 요금을 계산한다는 것

**기본 정책에 세금 정책 조합하기**

⇒ 부모 클래스에 추상 메서드를 추가하면 모든 자식 클래스들이 추상 메서드를 오버라이딩해야 하는 문제가 발생

⇒ 모든 추상 메서드의 구현이 동일하므로 유연성을 유지하면서 중복 코드를 제거할 수 있는 방법은 Phone에서 afterCalculated 메서드에 대한 기본 구현을 함께 제공하는 것

<img width="687" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/6d9093ce-f520-4247-a6b7-cdc4e5fc4072">


⇒ 훅 메서드란? 예제에서 afterCalculated 메서드를 말하며 기본 구현을 제공하는 메서드

⇒ TaxableNightlyDiscountPhone과 TaxableRegularPhone 코드가 중복이 많이 됨 (중복 코드 문제)

**기본 정책에 기본 요금 할인 정책 조합하기**

<img width="703" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/a88c10bf-c854-474f-961a-17915b8d6f62">


⇒ RateDiscountableNightlyDiscountPhone과 RateDiscountableRegularPhone 클래스의 코드가 중복이 많이 됨.

**중복 코드의 덫에 걸리다**

- 상속을 이용한 해결 방법은 모든 가능한 조합별로 자식 클래스를 하나씩 추가하는 것.
- 따라서 일반 요금제의 계산 결과에 세금 정책을 조합한 후 기본 요금 할인 정책을 추가하고 싶다면 새로운 클래스를 추가해야한다.

<img width="705" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f2f56595-5fc5-4f50-9eb3-a6dd23159528">


**새로운 기본 정책을 추가한다면?**

- 고정 요금제 정책을 추가하기 위해서는… 5개의 클래스가 필요
- 이처럼 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스를 추가해야 하는 경우를 **클래스 폭발**이라고 한다
- 이 문제는 단순히 새로운 기능을 추가할 때뿐만 아니라 기능을 수정할 때도 문제가 된다.(중복 코드가 많기 때문에)

 ****

<img width="700" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/28888c8f-5f28-4410-a072-75867ce3decf">


**결론**

→ 합성은 상속과 비교했을때 코드를 유연하게 유지해준다.

### **합성 관계로 변경하기**

합성은 컴파일 타임 관계를 런타임 관계로 변경함으로 클래스 폭발, 조합의 폭발 문제를 해결한다. How? 합성을 사용하면 구현이 아닌 **퍼블릭 인터페이스에 대해서만 의존**할 수 있기 때문에 런타임에 객체의 관계를 변경할 수 있다. 

- 기본 정책 합성하기
    
    먼저 **기본 정책**과 **부가 정책**을 포괄하는 `RatePolicy` 인터페이스 추가
    
    ```java
    public interface RatePolicy{
    	Money calculateFee(Phone phone);
    }
    ```
    
    **기본 정책**
    
    일반 요금제와 심야 할인 요금제는 개별 요금을 계산하는 방식을 제외한 전체 처리 로직이 동일하기 때문에 이 중복 코드를 담을 추상 클래스 `BasicRatePolicy` 를 추가
    
    ```java
    public abstract class BasicRatePolicy implements RatePolicy{
    	@Overide
    	public Money calculateFee(Phone phone){
    		Money result = Money.ZERO;
    		
    		for(Call call : phone.getCall()){
    			result.plus(calculateCallFee(call));
    		}
    	
    		return result;
    	}
    	
    	protected abstract Money calculateCallFee(Call call);
    }
    ```
    
    ```java
    public class RegularPolicy extends BasicRatePolicy {
        private Money amount;
        private Duration seconds;
    
        public RegularPolicy(Money amount, Duration seconds) {
            this.amount = amount;
            this.seconds = seconds;
        }
    		@Override
    		protected Money calculateCallFee(Call call){
    			return amount.times(call.getDuration().getSeconds()/seconds.getSeconds());
    		}	
    	}
    ```
    
    ```java
    public class NightlyDiscountPolicy extends BasicRatePolicy {
    		생성자...
        
    		@Override
    		protected Money calculateCallFee(Call call){
    			if( call.getFrom().getHour() >=20)			
    				return nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
    			
    			return regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
    		}	
    	}
    
    ```
    
    이제 기본 정책을 이용해 요금을 계산할 수 있도록 Phone수정
    
    ```java
    public class Phone{
    	**private RatePolicy ratePolicy;**
    	private List<Call> calls = new ArrayList<>();
    
    	**public Phone(RatePolicy ratePolicy){
    		this.ratePolicy = ratePolicy;
    	}**
    	
    	public List<Call> getCalls(){
    		return Collections.unmodifiableList(calls);
    	}
    	
    	public Money calculateFee(){
    		return ratePolicy.calculateFee(this);
    	}
    }
    ```
    
    ```java
    일반요금제 규칙
    Phone phone = new Phone(new RegularPolicy(Money.wons(10),Duration.ofSeconds(10));
    
    심야 할인 요금제 규칙
    Phone phone = new Phone(new NightlyDiscountPolicy(Money.wons(10),Duration.ofSeconds(10));
    ```
    
    <img width="640" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/cd27bc90-490b-47fa-bd44-a842c37f2677">

    

단순히 클래스를 선택하는 상속과 별 다른게 없어 보이는데…! → 놉!

부가정책이 추가되면 합성의 강력함을 느낄수있다.

- 부가 정책 적용하기
    - 부가 정책은 기본 정책이나 다른 부가 정책의 인스턴스를 참조할 수 있어야 한다. 다시 말해서 부가 정책의 인스턴스는 어떤 종류의 정책과도 합성될 수 있어야 한다.
    - Phone의 입장에서는 자신이 기본 정책의 인스턴스에게 메세지를 전송하고 있는지, 부가 정책의 인스턴스에게 메세지를 전송하고 있는지를 몰라야 한다. 다시 말해서 기본 정책과 부가 정책은 협력 안에서 동일한 ‘역할’을 수행해야 한다. 이것은 부가 정책이 기본 정책과 동일한 RatePolicy인터페이스(calculateFee())를 구현해야 한다는 것을 의미한다.
    
    부가 정책을 AdditionalRatePolicy 추상 클래스로 구현
    
    ```java
    public abstract class AdditionalRatePolicy implements RatePolicy{
    	private RatePolicy next;
    	
    	public AdditionalRatePolicy(RatePolicy next){
    		this.next = next;
    	}
    
    	@Overide
    	public Money calculateFee(Phone phone){
    		Money fee = next.calculateFee(phone);
    		return afterCalculated(fee);
    		
    	}
    	
    	protected abstract Money afterCalculated(Money fee);
    }
    ```
    
    ```java
    //세금 정책
    public class TaxablePolicy extends AdditionalRatePolicy{
    	public double taxRatio;
    
    	public TaxablePolicy(double taxtRatio, RatePolicy next){
    		super(next);
    		this.taxtRatio = taxtRatio;
    	}
    
    	@Override
    	protected Money afterCalculated(Money fee){
    		return fee.plus(fee.times(taxtRatio));
    	}
    }
    ```
    
    ```java
    //기본 요금 할인 정책
    public class RateDiscountablePolicy extends AdditionalRatePolicy{
    	private Money discountAmount;
    
    	public TaxablePolicy(double taxtRatio, Money discountAmount){
    		super(next);
    		this.discountAmount = discountAmount;
    	}
    
    	@Override
    	protected Money afterCalculated(Money fee){
    		return fee.minus(discountAmount);
    	}
    }
    ```
    
    위에 코드를 활용해서 기본 정책과 부가 정책 합성하기
    
    ```java
    Phone phone = new Phone( new TaxablePolicy(0.05, new RegularPolicy(...));
    
    일반 요금제에 기본 요금 할인 정책을 조합
    Phone phone = new Phone( new TaxablePolicy(0.05, new RateDiscountablePolicy(Money.wons(1000), new RegularPolicy(...));
    
    세금 정책과 기본 요금 할인 정책 적용 순서 바꾸기
    Phone phone = new Phone( new RateDiscountablePolicy(Money.wons(1000), new TaxablePolicy(0.05, new RegularPolicy(...));
    
    동일한 정책을 심야 할인 요금제에?
    Phone phone = new Phone( new RateDiscountablePolicy(Money.wons(1000), new TaxablePolicy(0.05, new NightlyDiscountPolicy (...));
    
    ```
    
    <img width="698" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/edae0686-d32c-4e1f-8eba-c802ccd2e368">

    
    - 상속은 구현을 재사용하고 합성은 객체의 인터페이스를 재사용한다. 객체 합성이 클래스 상속보다 더 좋은 방법이다.
    

코드를 재사용 할 수 있는 유용한 기법 하나 더~~

### 믹스인

상속과 클래스를 기반으로 하는 재사용 방법을 사용하면 클래스의 확장과 수정을 일관성 있게 표현할 수 있는 추상화의 부족으로 인해 변경하기 어려운 코드를 얻게 된다. 따라서 구체적인 코드를 재사용하면서도 낮은 결합도를 유지할 수 있는 유일한 방법은 재사용에 적합한 추상화를 도입하는 것이다. 

믹스인 (mixin):객체를 생성할 때 코드 일부를 클래스 안에 섞어 넣어 재사용하는 기법을 가리키는 용어

합성은 실행 시점에 객체를 조합하는 재사용 방법이라면 믹스인은 컴파일 시점에 필요한 코드 조각을 조합하는 재사용 방법이다. 

<img width="730" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/286e44ad-cf93-4c61-ac7a-76f349c18271">


**코드로 믹스인 보기~**

기본 정책 구현하기

```java
abstract class BasicRatePolicy{
	def calculateFee(phone: Phone):Money = 
		phone.calls.map(calculateCallFee(_)).reduce(_+_)
	
	protected def calculateCallFee(call: Call): Money;
}
```

**일반 요금제**

```java
class RegularPolicy(val amount:Money, val seconds:Duration) extends BasicRatePolicy{
override protected def calculateCallFee(call: Call):Money = amount*(call.duration.getSeconds/seconds.getSeconds) }
```

**심야 할인 요금제**

```java
class NightlyDiscoutPolicy(
	val nightlyAmount:Money, 
	val regularAmount:Money, 
	val seconds:Duration) extends BasicRatePolicy{

	override protected def calculateCallFee(call: Call) : Money = 
	if(call.from.getHour>=22)
		nightlyAmount * (call.duration.getSeconds/seconds.getSecond)
	else
		regularAmount * (call.duration.getSeconds/seconds.getSecond)
	}
}
amount*(call.duration.getSeconds/seconds.getSeconds) 
	
}
```

부가 정책 구현하기 ( 여기서 차이가 나옴!!)

**세금 정책**

```java
trait TaxablePolicy extends BasicRatePolicy{
	def taxRate: Double
	
	override def calculateFee(phone: Phone):Money={
		val fee = super.calculateFee(phone)
		fee + fee*taxRate
	**}
}
```

포인트 

- TaxablePolicy가 BasicRatePolicy를 확장하는 점
    
    → 이것은 상속의 개념이 아니라 TaxablePolicy가 BasicRatePolicy나 BasicRatePolicy의 자손에 해당되는 경우에만 믹스인 될 수 있다는 것을 의미
    

상속이랑 다를게 없어 보이는데..??

1. 상속은 정적이지만 믹스인은 동적이다. 상속은 부모 클래스와 자식 클래스의 관계를 코드를 작성하는 시점에 고정시켜 버리지만 믹스인은 제약을 둘 뿐 실제로 어떤 코드에 믹스인 될 것인지를 결정하지 않는다.
    
    우리의 TaxablePolicy 트레이트는 어떤 코드에 믹스인 될 것인가? 알 수 없다. 실제로 트레이트를 믹스인하는 시점에 가셔야 믹스인할 대상을 결정할 수 있다. 
    
2. 상속의 경우 일반적으로 this 참조는 동적으로 결정되지만 super참조는 컴파일 시점에 결정된다. 상속은 재사용 가능한 문맥을 고정시키지만 트레이트는 문맥을 확장 가능하도록 열어 놓는다. 

이런 면에서 믹스인은 상속보다 합성과 유사하다. 합성은 독립적으로 자상된 객체들을 실행 시점에 조합해서 더 큰 기능을 만들어내는 데 비해 믹스인은 독립적으로 작성된 트레이트와 클래스를 코드 작성 시점에 조합해서 더 큰 기능을 만들어낼 수 있다. 

**비율 할인 정책**

```java
trait RateDiscountablePolict extends BasicRatePolicy{
	def discountAmount: Money
	
	override def calculateFee(phone: Phone):Money={
		val fee = super.calculateFee(phone)
		fee - discountAmount
	}
}
```

부가 정책 트레이트 믹스인하기

믹스인하려는 대상 클래스의 부모 클래스가 존재하는 경우 부모 클래스는 extends를이용해 상속받고 트레이트는 with를 이용해 믹스인해야 한다. 이를 트레이트 조합이라고 부른다. 

```java
class RateDiscountableRegularyPolicy(
	amount:Money,
	seconds:Duration,
	val discountAmount:Money)
extends RegularPolicy(amount,seconds)
with RateDiscountablePolicy
```

선형화(linerization)

쌓을 수 있는 변경

전통적으로 믹스인은 특정 한 클래스의 메서드를 재사용하고 기능을 확장하기 위해 사용돼 왔다. 

Ex) BasicRatePolicy의 calculateFee 메서드의 기능을 확장하기 위해 믹스인 사용

믹스 인은 상속 계층 안에서 확장한 클래스보다 더 하위에 위치하게 된다. 다시 말해서 믹스인은 대상 클래스의 자식 클래스처럼 사용될 용도로 만들어지는 것이다. TaxablePolicy와 RateDiscountablePolicy는 BasicRatePolicy에 조합되기 위해 항상 상속 계층의 하위에 믹스인 됐다는 것을 기억하라. 따라서 믹스인을 추상 서브 클래스(abstract subclass)라고 부르기도 한다.
