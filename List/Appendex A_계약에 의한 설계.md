# Appendex A_계약에 의한 설계

인터페이스 만으로 객체의 행동에 관한 다양한 관점을 전달하기 어렵다. 

따라서 명령의 부수효과를 쉽고 명확하게 표현할 수 있는 커뮤니케이션인 계약에 의한 설계가 필요하다.

계약의 의한 설계는 

협력에 필요한 다양한 제약과 부수효과를 명시적으로 정의하고 문서화 할 수 있음

클라이언트 개발자는 오퍼레이션의 구현을 살펴보지 않더라도 객체의 사용법을 쉽게 이해 할 수 있음

계약은 실행 가능하기 때문에 구현에 동기화돼 있는지 여부를 런타임에 검증할 수 있음(실행가능한 검증 도구로 사용 가능)

클래스의 부수효과를 명시적으로 문서화함으로써 명확하게 커뮤니케이션 할 수 있음

### 01 협력과 계약

인터페이스는 메세지의 이름과 파라미터 목록을 시그니처를 통해 전달할 수 있지만, 협력을 위해 필요한 약속과 제약은 인터페이스를 통해 전달할 수 없다. 

```csharp
Class Event {
	public bool IsSatisfied(RecrringScedule schdule){...}
	
	public void Reschedule(RecrringScedule schedule){
		**Contract.Requires(IsSatisfied(schdule);**
		...
	}
}
```

- 문서화 가능
- 제약조건의 만족 여부를 실행 중에 체크가능
- 이런 조건들을 코드로부터 추출해서 문서를 만들어주는 자동화 도구 제공

→ 계약에 의한 설계를 사용하면 제약 조건을 명시적으로 표현하고 자동으로 문서화를 할 수 있을뿐만 아니라 실행을 통해 검증할 수 있다. 

### 02 계약에 의한 설계

버트란드 마이어 계약에 의한 설계 기법 고안해냄.

- 협력에 참여하는 각 객체는 계약으로부터 **이익**을 기대하고 이익을 얻기 위해 **의무**를 이행한다.
- 협력에 참여하는 각 객체의 이익과 의무는 객체의 인터페이스 상에 **문서화**된다.

의도를 드러내는 인터페이스를 만들면 오퍼레이션의 시그니처만으로도 어느 정도까지는 클라이언트와 서버가 협력을 위해 수행해야 하는 제약 조건을 명시 할 수 있다. 


![520F4A48-AB1A-45BA-87B7-56F138D2AA70](https://github.com/HJC96/Obejct/assets/87226129/aca7ebb0-45a4-4ca1-b827-0a9904969881)

![5A1C371![5A1C371A-4860-42DB-926B-C9950B3FDF07](https://github.com/HJC96/Obejct/assets/87226129/06c42fb3-a34a-4ac5-a495-0264463131a1)


서버는 자신이 처리할 수 있는 범위의 값들을 클라이언트가 전달할 것이라고 기대한다. 클라이언트는 자신이 원하는 값을 서버가 반환할 것이라고 예상한다. 클라이언트는 메세지 전송 전과 후의 서버의 상태가 정상일 것이라고 기대한다. 

- 사전조건 (클라이언트의 의무)
    - 클라이언트가 정상적으로 메서드를 실행하기 위해 만족시켜야 하는 것
- 사후조건 (서버의 의무)
    - 메서드가 실행된 후에 서버가 클라이언트에게 보장해야 하는 것
- 불변식
    - 메서드 실행 전과 실행 후에 인스턴스가 만족시켜야 하는 클래스 불변식
    

코드로 보자~~

.NET 프레임 워크 4.0에 표준으로 추가된 Code Contracts를 이용해 C#으로 작성

사전 조건

```csharp
public Reservation Reserve(Customer customer, int audienceCount)
{
	Contract.Requires(customer != null);
	Contract.Requires(audienceCount >= 1);
	return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
}
```

`var reservation = secreening.Reserve(null,2);` 하면 ConstractException 예외 발생

사후조건

용도

- 인스턴스 변수의 상태가 올바른지를 서술하기 위해
- 메서드에 전달된 파라미터의 값이 올바르게 변경됐는지를 서술하기 위해
- 반환값이 올바른지를 서술하기 위해

다음과 같은 두가지 이유로 사전조건보다 정의하는 것이 어려울 수 있다. 

- 한 메서드 안에 return 문이 여러 번 나올 경우
- 실행 전과 실행 후의 값을 비교해야 하는 경우
    
    → 하지만 라이브러리에서 간편한 방법을 제공해서 걱정 노노
    

```csharp
public Reservation Reserve(Customer customer, int audienceCount)
{
	Contract.Requires(customer != null);
	Contract.Requires(audienceCount >= 1);
	**Contract.Ensures**(Constract.Result<Reservation>() != null);
	return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
}
```

```csharp
public decimal Buy(Ticket ticket)
{
	Contract.Requires(ticket != null);
	**Contract.Ensures(Contract.Result<decimal>()>0);**
	if(bag.Invited)
	{
		bag.Ticket = ticket;
		return 0;
	}
	else
	{
		bag.Ticket = ticket;
		bag.MinusAmount(ticket.Fee);
		retrun ticket.Fee;
	}
}
```

```csharp
public string Middle(string text)
{
	Contract.Requires(text !=null && text.Length>=2);
	//Contract.Requires(Contract.Result<string>().Length<text.Length);
	Contract.Requires(Contract.Result<string>().Length<Constract.OldValue<string>(text).Length);	
	text = text.Substring(1, text.Length - 2);
	return text.Trim();
}
```

불변식

특징

- 불변식은 클래스의 모든 인스턴스가 생성된 후에 만족돼야 한다. 이것은 클래스에 정의된 모든 생성자는 불변식을 준수해야 한다는 것을 의미한다.
- 불변식은 클라이언트에 호출 가능한 모든 메서드에 의해 준수돼야 한다. 메서드가 실행되는 중에는 객체의 상태가 불안정한 상태로 빠질 수 있기 때문에 불변식을 만족시킬 필요는 없지만 메서드 실행 전과 메서드 종료 후에는 항상 불변식을 만족하는 상태가 유지돼야 한다.

Code Contracts 에서는 Contract.Invariant 메서드를 이용해 불변식을 정의할 수 있다. 불변식은 생성자 실행 후, 메서드 실행 전, 메서드 실행 후에 호출 돼야 하는데 Code Contracts 는 ContractInvarianMethod 애트리뷰트가 지정된 메서드를 불변식으로 체크해야 하는 모든 지점에 자동으로 추가한다. 

```csharp
public class Screening
{
	private Movie movie;
	private int sequence;
	private DateTime whenScreend;

	[ContractInvarianMethod]
	private void Invariant()
	{
		Contract.Invariant(movie != null);
		Contract.Invariant(sequence>= 1);
		Contract.Invariant(whenScreend > DateTime.Now);
	}
}
```

Code Contracts 덕분에 객체의 생성자나 메서드 실행 전후 불변식을 직접 호출해야 하는 수고를 들일 필요가 없다. 

### 계약에 의한 설계와 서브타이핑

리스코프 치환 원칙 : 슈퍼타입의 인스턴스와 협력하는 클라이언트의 관점에서 서브타입의 인스턴스가 슈퍼타입을 대체하더라도 협력에 지장이 없어야 한다.

규칙

1. 협력에 참여하는 객체에 대한 기대를 표현하는 **계약 규칙**
    - 서브타입에 더 강력한 사전조건을 정의할 수 없다.
    - 서브타입에 더 완화된 사후 조건을 정의할 수 없다.
    - 슈퍼타입의 불변식은 서브타입에서도 반드시 유지돼야 한다.
2. 교체 가능한 타입과 관련된 가변성 규칙
    - 서브타입의 메서드 파라미터는 반공변성을 가져야 한다.
    - 서브타입의 리턴 타입은 공변성을 가져야 한다.
    - 서브타입은 슈퍼타입이 발생시키는 예외와 다른 타입의 예외를 발생시켜서는 안된다.

코드를 통해 보기 11장에 핸드폰 과금 시스템 합성 버전을 예시로 살펴볼 예정

**계약 규칙**

```csharp
//기본정책과 부가정책을 구현하는 모든 객체들이 실체화해야 하는 인터페이스
public interface RatePolicy {
	Money calculateFee (List<Call> calls);
}
```
![FE262B41-FEE9-4CC0-BB57-8CE8A8373D3B](https://github.com/HJC96/Obejct/assets/87226129/36d99b01-bd6b-4865-ab53-0f8d70d97f79)

이 클래스들은 정말로 RatePolicy의 서브타입인가? 다시 말해서 리스코프 치환 원칙을 만족하는가? → RatePolicy의 클라이언트인 Phone과 체결한 계약을 준수하는지 살펴보면 알지~ 

이해를 돕기 위해 요금 청구서를 발행하는 publicBill 메서드를 Phone에 추가

```csharp
public class Bill{
	private Phone phone;
	private Money fee;
	
	public Bill(Phone phone, Money fee){
		if(phone == null)
			throw new IllegalArgumentException();

		if(fee.isLeeThan(Money.Zero)
			throw new IllegalArgumentException();

		this.phone = phone;
		this.fee = fee;
	}
}
```

```csharp
public class Phone{
	**private** RatePolicy ratePolicy;
	private List<Call> calls = new ArrayList<>();

	public Phone(RatePolicy ratePolicy){
		this.ratePolicy = ratePolicy;
	}
	
	public void Call (Call call){
		return calls.add(call);
	}
	
	public Bill publicBill(){
		return new Bill(this, ratePolicy.**calculateFee(calls));
		//calculateFee(calls)의 반환값은 0보다 커야 하고 (사후조건)
		//그럴러면 calls가 null이아니여야 한다.(사전 조건)**
	}
}
```

RatePolicy 인터페이스를 구현하는 클래스가 RatePolicy의 서브타입이 되기 위해서는 위에 정의한 사전조건과 사후 조건을 만족해야 한다. 

BasicRatePolicy과 AdditionalRatePolicy를 RatePolicy의서브 타입으로 만들기 

```csharp
public abstract class BasicRatePolicy implements RatePolicy{
	@Overide
	public Money calculateFee(List<Call> calls){
		**//사전조건
		assert calls != null**	
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result.plus(calculateCallFee(call));
		}

		//사후조건
		assert result.isGreaterThanOrEqual(Money.Zero)
		return result;
	}
	
	protected abstract Money calculateCallFee(Call call);
}
```

```csharp
public abstract class AdditionalRatePolicy implements RatePolicy{
	private RatePolicy next;
	
	public AdditionalRatePolicy(RatePolicy next){
		this.next = next;
	}

	@Overide
	public Money calculateFee(List<Call> calls){
		**//사전조건
		assert calls != null**	

		Money fee = next.calculateFee(phone);
		Money result = afterCalculated(fee);
		
		**//사후조건
		assert result.isGreaterThanOrEqual(Money.Zero)**

		return result;
	}
	
	protected abstract Money afterCalculated(Money fee);
}
```

지금부터는 이 예제를 이용해 앞에서 설명한 계약규칙과 가변성 규칙에 대해 자세히 살펴보자~

### 계약 규칙

- 서브타입에 더 강력한 사전 조건을 정의할 수 없다.
    
    한 번도 통화가 발생하지 않은 Phone에 대한 청구서를 발행하는 시나리오를 고려해보자
    
    ```csharp
    Phone phone = new Phone(new ReaularPolicy(Money.wons(100), Duration.ofSeconds(10)));
    Bill bill = phone.publisBill();
    ```
    
    calculateFee의 사전 조건에 인자가 null일경우를 제외하고는 모든 값을 허용하기 때문에 위 코드는 사전 조건을 위반하지 않는다. 
    
    하지만 ReqularPolicy의 부모 클래스인 BasicPolicy에 calls가 빈리스트여서 안된다는 사전조건을 추가하면 어떻게 될까? 위에 코드는 실행되지 않을 것이다. 
    
    사전조건을 만족시키는 것은 클라이언트의 책임이다. 클라이언트인 phone은 오직 RatePolicy인터페이스만 알고 있기 때문에 RatePolicy가 null을 제외한 어떤 calls라도 받아들인다고 가정한다. 따라서 빈 List를 전달하더라도 문제가 발생하지 않는다고 예상할 것이다.  BasicPolicy에 사전조건에 새로운 조건을 추가함으로써 둘 사이에 계약이 위반된다. 
    
    만약에 반대로 사전조건을 완화시킬 경우 즉 BasicPolicy의 calls가 null인자를 전달해도 예외가 발생하지 않도록 수정하면? 괜츈 
    
    다시 한번 강조하지만 사전조건을 보장해야 하는 책임은 클라이언트에게 있기 때문에 그리고 이미 클라이언트인 Phone은 RatePolicy의 calculateFee 오퍼레이션을 호출 할 때 인자가 null이아닌 값을 전달하도록 보장하고 있을 것이다. 결과적으로 사전 조건을 완화시키는 것은 리스코프 치환 원칙을 위반하지 않는다. 
    
- 서브타입에 더 완화된 사후조건을 정의할 수 없다.
    
    RatePolicy의 calculateFee 오퍼레이션의 반환값이 0보다 작은 경우를 다뤄보자 
    
    ```csharp
    //10초당100원을 부과하는 일반요금제에 
    //1000원을 할인해주는 기본 요금 할인 정책을 적용하는 시나리오 
    Phone phone 
    			= new Phone(
    					new RateDiscountablePolicy(Money.wons(1000),
    						new RegularPolicy(Money.wons(100), Duration.ofSeconds(10))));			
    			)
    //통화내역은 하나인데 통화시간은 1분 -> 60/10 * 100 원 = 총 600원
    //기본 요금 할인 정책 600 - 1000 = -400..??
    phone.call(new Call(LocalDateTime(2022,1,1,10,10),
    										LocalDateTime.of(2022,1,1,10,11)));
    Bill bill = phone.publicBilll();
    ```
    
    - 사후 조건을 만족시킬 책임은 ?
        
        서버에게 있다. 
        
        따라서 클라이언트인 phone은 반환된 요금이 0원인지 0보다 큰지를 확인할 의무가 없으며 사후조건을 위반하는 책임은 전적으로 서버인 RateDiscountablePolicy가 져야 한다. RateDiscountablePolicy는 계약을 만족시킬 수 없다는 사실을 안 즉시 예외를 발생시키기 때문에 calculateFee오퍼레이션은 정상적으로 실행되지 않고 종료된다. 
        
    
    RateDiscountablePolicy가 사후 조건을 완화시켜 마이너스 요금이 반환되더라도 예외가 발생하지 않도록 수정하게 되면? Bill에서 예외를 발생시키게 되고 예외 스택 트레이스는 Bill 생성자가 문제라고 지적한다.. 우리는 그 예외 스택 트레이스를 근거로 Bill에 전달된 마이너스 금액을 계산해낸 위치를 추척해야 할 것이다. 
    
    기존에 Phone과 RatePolicy 사이에 체결된 계약을 위반했기 때문에 발생한것.. 클라이언트Phone의 입장에서 AdditionalRatepolicy는 더 이상 RatePolicy의 서브타입이 아니다.. → 결국 사후 조건을 완화시키는 서버는 클라이언트의 관점에서 수용할 수 없기 때문에 슈퍼타입을 대체할 수 없다. 사후조건 완화는 리스코프 치환 원칙 위반이다. 
    
    강화는? Phone은반환된 요금이 0원보다 크기만 하다면 아무런 불만을 가지지 않기 때문에 사후 조건 강화는 계약에 영향을 미치지 않는다. 
    
    - **일찍 실패하기**
        
        마이너스 금액을 그대로 사용하는 것보다 처리 종료하는 것이 올바른 선택이다. 
        
        방금 불가능한 뭔가가 발생했다는것을 코드가 발견한다면 프로그램은 더 이상 유효하지 않다고 할 수 있다. 이 시점 이후로 하는 일은 모두 수상쩍게 된다. 되도록 빨리 종료해야 한다. 일반적으로 죽은 프로그램이 입히는 피해는 절름발이 프로그램이 끼치는 것보다 훨씬 덜한 법이다. 
        
- 슈퍼타입의 불변식은 서브타입에서도 반드시 유지돼야 한다.
    
    불변식은 메서드가 실행되기 전과 후에 반드시 만족시켜야 하는 조건이다. 
    
    ```csharp
    public abstract clss AdditionalRatePolicy implements RatePolicy{
    	protected RatePolicy next;
    
    	public AdditionalRatePolicy (RatePolicy next){ //next가 protected변수 이기 때문에 null로 변경 가능성 있음 
    		this.next = next;
    		//불변식
    		assert next != null;
    	}
    
    	@Override
    	public Money calculateFee(List<Call> calls){
    		//불변식
    		assert next != null;
    		//사전조건
    		assert Calls != null;
    
    		....
    		
    		//사후조건
    		assert result.isGreaterThanOrEqual(Money.ZERO);
    		assert next != null;
    		return result;
    		
    	}
    
    }
    ```
    
    자식 클래스가 계약을 위반할 수 잇는 코드를 작성하는 것을 막을 수 잇는 유일한 방법은 인스턴스 변수의 가시성을 protected가 아니라 private으로 만드는 것!
    
    모든 인스턴스 변수의 가시성은 private으로 제한돼야 함 → 만약 자식 클래스에서 인스턴스 변수의 상태를 변경하고 싶다면? → 부모 클래스에 protected 메서드를 제공하고 이 메서드를 통해 불변식을 체크하게 해야 함. 
    

### 가변성 규칙

- 서브타입은 슈퍼타입이 발생시키는 예외와 다른 타입의 예외를 발생시켜서는 안된다.
    
    ```java
    public class EmptyCallException extends RuntimeException{...}
    
    public interface RatePolicy{
    	Money calculateFee(List<Call> calls) **throws EmptyCallException;**
    }
    ```
    
    ```java
    //슈퍼 타입의 계약을 준수하기 위해 다음과 같이 구현됌.
    public abstract class BasicRatePolicy implements RatePolicy{
    	@Override
    	public Money calculateFee(List<Call> calls){
    		if(calls == null || calls.isEmpty())
    			**throw new EmptyCallException();**
    	}
    }
    ```
    
    RatePolicy와 협력하는 메서드가 있다고 가정할 때, 이 메서드는 EmptyCallException 예외가 던져질 경우 이를 캐치한 후 0을 반환한다. 
    
    ```java
    public void calculate(RatePolicy policy, List<Call> calls){
    	try{
    		return policy.calculateFee(calls);
    	}catch(EmptyCallException ex){
    		return Moeny.ZERO;
    	}
    }
    ```
    
    그런데 만약! RatePolicy를 구현하는 객체가 다른 예외 (NoneElementException)을 던진다고 가정해 보자 
    
    ```jsx
    public abstract class AdditionalRatePolicy implements RatePolicy{
    	@Override
    	public Money calculateFee(List<Call> calls){
    		if(calls == null || calls.isEmpty())
    			**throw new** NoneElementException**();**
    	}
    }
    ```
    
    NoneElementException가 EmptyCallException 클래스의 자식 클래스라면 AdditionalRatePolicy는 RatePolicy를 대체할 수 있지만
    
    ```jsx
    public class EmptyCallException  extends RuntimeException {...}
    public class NoneElementException extends EmptyCallException{...}
    ```
    
    상속 계층이 다르면 하나의 catch문으로 두 예외를 모두 처리할 수 없기 때문에 NoneElementException은 예외 처리에서 잡히지 않게 된다. 결과적으로 클라이언트 입장에서 협력의 결과가 예상을 벗아났기 떄문에 AdditionalRatePolicy는 RatePolicy를 대체할 수 없다. 
    
    ```jsx
    public class EmptyCallException  extends RuntimeException {...}
    public class NoneElementException extends RuntimeException{...}
    ```
    
    - 이 규칙의 변형이 존재
        
        부모 클래스에서 정하지 않은 예외를 발생시키는 경우 13장에서 Bird를 상속받은 Penguin의 예
        
        UnsupportedOperationException or 펭귄에서 fly에 아무것도 안하는 방법..
        
        둘다 클라이언트 관점에서 자식 클래스가 부모 클래스가 하는 일보다 더 적은일을 수행한다..
        
    
    클라이언트 관점에서 부모 클래스에 대해 기대했던 것보다 더 적은 일을 수행하는 자식 클래스는 부모 클래스와 동일하지 않다. 부모 클래스보다 못한 자식 클래스는 서브타입이 아니다. 
    
- 서브타입의 리턴 타입은 공변성을 가져야 한다.
    
    S가 T의 서브타입이라고 가정 했을 때
    
    **공변성 (covariance)** : S와 T사이의 서브타입 관계가 그대로 유지된다. 이 경우 해당 위치에서 서브타입인 S가 슈퍼타입인 T대신 사용될 수 있다. (리스코프 치환원칙)
    
    **반공변성(contravariance)** : S와 T 사이의 서브타입 관계 역전 , 슈퍼타입인 T가 서브타입인 S대신 사용 될 수 있다.
    
    **무공변성(invariance) :**  S와 T 사이에는 아무런 관계도 존재하지 않는 경우 둘이 서로 대체할수없음.
    
    이해를 돕기 위해 세개의 상속 계층을 살퍼보자! 세개의 상속 계층은 모두 리스코프 치환 원칙을 만족하도록 구현돼 있다고 가정
    
    
    ![54E18E17-B581-4078-B6E1-604BBC9230FE](https://github.com/HJC96/Obejct/assets/87226129/10245062-ea67-45da-8da6-52eb439fca45)

    ```csharp
    
    public class Publisher {}
    public class IndependentPublisher extends Publisher {}
    
    public class Book{
    	private Publisher publisher;
    	public Book(Publisher publisher){
    		this.publisher = publisher;	
    	}
    }
    
    public class Magazine extends Book{
    	public Magazine(Publisher publisher){
    		super(publisher);
    	}
    }
    
    //Book과 Magazine을 판매하는 판매처는 두 종류
    //거리에서 책을 판매하는 가판대
    //단 독립출판사(IndependentPublisher)가 출간한 책만 팔수 있음
    public class BookStall{
    	public Book sell(IndependentPublisher independentPublisher){
    		return new Bool(independentPublisher);
    	}
    }
    
    //전문적으로 잡지를 판매하는 매거진 스토어
    //단 독립출판사(IndependentPublisher)가 출간한 Magazine만 판매 가능
    public class MagazineStore extends BookStall{
    	@Override
    	public Book sell(IndependentPublisher independentPublisher){
    		return new Magazine(independentPublisher);
    	}
    }
    
    public class Customer{
    	private Book book;
    	public void order(BookStall booksStall){
    		this.Book = booksStall.sell(new IndependentPublisher());
    	}
    }
    ```
    
    ![6EDBAEEA-5814-41C1-ACD7-E4DEC72C9F17](https://github.com/HJC96/Obejct/assets/87226129/1697c150-52b2-4c5b-bbdf-efa7af03c855)

    지금까지 살펴본 서브타이핑은 단순히 서브타입이 슈퍼타입의 모든 위치에서 대체 가능하다는 것인데 공변성과 반공변성의 영역으로 들어서기 위해서는 타입의 관계가 아니라 **메서드의 리턴 타입과 파라미터 타입에 초점**을 맞춰야 한다!!
    
    리스코프 치환 원칙이 클라이언트 관점에서의 치환 가능성이므로 BookStall의 클라이언트 관점에서 리턴 타입 공변성 걔념을 살펴볼 필요가 있다. 
    
    ```java
    new Customer().order(new BookStall());
    new Customer().order(new MagazineStore());
    ```
    ![15888E27-F63B-4157-864F-6F71E08814AC](https://github.com/HJC96/Obejct/assets/87226129/6c6d1713-0798-4128-81f4-21c03891f76d)
    
    ![B04D24BA-0D1A-4322-8488-C6570B831F0D](https://github.com/HJC96/Obejct/assets/87226129/517716b3-87ef-46cb-b332-fb3817c0419e)

    
    이 처럼 부모 클래스에서 구현된 메서드를 자식 클래스에서 오버라이딩할 때 부모 클래스에서 선언한 반환 타입의 서브타입으로 지정할 수 있는 특성을 **리턴 타입 공변성** 이라고 부른다. 
    
    간단히 말해서 메서드를 구현한 클래스의 **타입 계층 방향과 리턴 타입의 타입 계층 방향이 동일한 경우**를 말한다~ 
    
    ![07C6D782-441B-4955-8713-209587318BC8](https://github.com/HJC96/Obejct/assets/87226129/8049c486-e849-466c-8c32-64a472e1c599)

    
    단 언어마다 지원여부 다름.. 자바는 리턴 타입 공변성 지원 C#은 컴파일 에러..
    
    C#에서는 Book 타입을 반환하는 BookStall의 sell메서드는 Magazine타입을 반환하는 MagazineStore의 sell 메서드와는 아무런 상관도 없다.. → 무공변성
    
    이론적으로는 메서드의 리턴 타입을 공변적으로 정의하면 리스코프 치환 원칙을 만족시킬 수 있지만 실제적으로는 언어의 지원 여부에 따라 리턴 타입 공변성을 사용하지 못할 수도 있다. 
    
- 서브타입의 메서드 파라미터는 반공변성을 가져야 한다.
    
    java에서는 반공변성 제공하지 않지만 있다고 가정하자..!
    
    ```java
    public class MagazineStore extends BookStall{
    	@Override
    	public Magazine sell(**Publisher publisher**){
    		return new Magazine(**publisher**);
    	}
    }
    ```
    
    ![21E9A3EE-680C-4D3C-BC2E-682A756FB826](https://github.com/HJC96/Obejct/assets/87226129/9bccf274-e65e-493c-b727-004f6bebacfb)

  
    ![FB6DAD19-BC72-4052-9DC2-9503B18936FD](https://github.com/HJC96/Obejct/assets/87226129/4fcb54e1-d2eb-41ad-a8da-c1472f78f51d)

    ![8E13D64F-35E9-4550-A16E-A1866CE8222E](https://github.com/HJC96/Obejct/assets/87226129/b68e6896-3d32-44eb-91ca-f1c1a73792af)

    
    이처럼 부모 클래스에서 구현된 메서드를 자식 클래스에서 오버라이딩 할때 파라미터 타입을 부모 클래스에서 사용한 파라미터의 슈퍼타입으로 지정할 수 잇는 특성을 파라미터 타입 반공변성 이라고 부른다. 
    
    간단히 말해서 파리멑 타입 반공변성이라느 메서드가 정의한 클래스의 타입 계층과 파라미터의 타입 계층의 방향이 반대인 경우 서브타입 관계를 만족한다는 것을 의미한다. 
    

### 다음주 꼭!!!!!!!!!

함수 타입과 서브타이핑

```java
def sell(publisher:IndependentPublisher) : Book = new Book(publisher)

//익명함수로 
(publisher:IndependentPublisher) => new Book(publisher)

익명함수를 허용하는 언어들은 객체의 타입 뿐만아니라 메서드의 타읍을 정의할 수 있게 허용
그리고 타입에서 정의한 시그니처를 준수하는 메서드들을 이 타입의 인스턴스로 간주함

// 파라미터 타입과 리턴 타입을 이용해 함수 타입 정의
IndependentPublisher => Book

BookStall이나 MagazineStore는 단 하나의 메서드만 포함하기 떄문에 별도의 클래스로 정의하지 않고
메서드로 정의해 Customer에 전달하는 것이 가능하다.

Class Customer{
	var book:Book = null
	
	def order(store: IndependentPublisher => Book) :Unit = {
		book = store(new IndependentPublisher())
	}
}

이제 IndependentPublisher를 파라미터로 받고 Book을 반환하는 BookStall의 sell 메서드를 클래스 정의 없이
함수 리터럴을 통해 파라미터로 전달할 수 잇다. 

new Order().order((publisher: IndependentPublisher) => new Book(publisher))

??서브타입 메서드가 슈퍼타입 메서드를 대체할수 있을까 ? 그렇다!
//아래코드 정상 실행 됌
new Customer().order((publisher:Publisher) => Magazine(publisher))

```

![AF7AFFC6-F2AB-417B-A831-99F31D99DC9A](https://github.com/HJC96/Obejct/assets/87226129/38ab88d2-6f79-4ac6-abd9-a1f3730433b2)
