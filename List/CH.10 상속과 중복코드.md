# CH.10 상속과 중복코드

객체지향의 대표적인 재사용 기법인 상속에 관해 다룬다. 조금 놀랄 수도 있겠지만 10장의 주제는 코드 재사용을 위해 상속을 사용하지 말라는 것이다.

### 상속과 중복 코드

**중복 코드 문제**

중복 코드는 변경을 방해하기때문에 제거해야한다. 

**중복 코드 판단 기준**

- 코드의 모양이 아닌 변경시 수정여부klfl;skadjfl;kjsad;lfk  ****

**반복하지 마라 DRY(Don’t Repeat Yourself) 원칙**

> 모든 지식은 시스템 내에서 단일하고, 애매하지 않고, 정말로 믿을 만한 표현 양식을 가져야 한다.
> 

- 한 달에 한번씩 가입자별로 전화 요금을 계산하는 간단한 애플리케이션
    
    ```java
    public class Call{
    	private LocalDateTime from;
    	private LocatDateTime to;
    
    	public Call(LocalDataTime from, LocalDateTime to){
    		this.from = from;
    		this.to = to;
    	}
    	
    	public Duration getDuration(){
    		return Dration.between(from, to);
    	}
    	
    	public LocalDateTime getFrom(){
    		return from;
    	}
    }
    ```
    
    ```java
    public class Phone{
    	private Money amount;
    	private Duration seconds;
    	private List<Call> calls = new ArrayList<>();
    
    	public Phone(Money amount, Duration seconds){
    		this.amount = amount;
    		this.seconds = seconds;
    	}
    	
    	public void call(Call call){
    		calls.add(call);
    	}
    
    	public List<Call> getCalls(){
    		return calls;
    	}
    
    	public Money getAmount(){
    		return amount;	
    	}
    	
    	public Duraion getSeconds(){
    		return seconds;
    	}
    	
    	public Money calculateFee(){
    		Money result = Money.ZERO;
    		for(Call call : calls){
    			result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
    			
    			return result;
    		}	
    	}
    }
    ```
    
    ```java
    Phone phone = new Phone(Money.wons(5), Duration.ofSeconds(10));
    phone.call(new Call(LocalDateTime.of(2018, 1, 1, 12, 10, 0),
    										LocalDateTime.o(2018, 1, 1, 12, 11, 0)));
    phone.call(new Call(LocalDateTime.of(2018, 1, 2, 12, 10, 0),
    										LocalDateTime.o(2018, 1, 2, 12, 11, 0)));
    
    phone.calculateFee(); //=> Money.wons(60)
    
    ```
    

심야 할인 요금제라는 새로운 요금 방식 추가 요청!!!( 심야 할인 요금제)

```java
public class NightlyDiscountPhone{
	**private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;
	private Money regularAmount;**
	~~private Money amount;~~
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public NightlyDiscountPhone(Money **nightlyAmount**,Money regular**Amount**, Duration seconds){
	}
	
	public void call(Call call){
		calls.add(call);
	}

	public List<Call> getCalls(){
		return calls;
	}

	public Money getAmount(){
		return amount;	
	}
	
	public Duraion getSeconds(){
		return seconds;
	}
	
	public Money calculateFee(){
		Money result = Money.ZERO;
		for(Call call : calls){
		//**nightlyAmount**,regularAmount 일때 분기처리 
			result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			
			return result;
		}	
	}
}
```

별 문제 없어 보이지만… 

통화 요금에 부과할 세금을 계산하는 부분 추가!! 단, 부가되는 세율은 가입자의 핸드폰 마다 다르다!!

세율을 저장할 변수 ,  calculateFee() 수정 

→ phone과 nightlyDiscountPhone 둘다 수정해야 하는데, 많은 코드 더미 속에서 어떤 코드가 중복인지 파악하는게 쉬운일이 아니고, 하나만 수정하고 나갔을 경우 큰 문제를 일으킨다. 

→ 중복 코드는 새로운 중복 코드를 부른다. (일관성을 해침, 버그가 늘어나고, 코드를 변경하는 속도는 점점 낮아진다.)

두 클래스 사이에 중복 코드를 제거하는 방법

1. 클래스를 합치기
    
    타입에 따라 로직을 분기 시켜 두 클래스 합치기 →  하지만 낮은 응집도와 높은 결합도라는 문제에 시달리게 됨..
    
    → 절차지향과 다를바 없음.
    
2. 상속
    
    기존 코드는 두고(phone) nightlyDiscountPhone이 상속을 받아 코드를 작성 → 부모 클래스의 개발자가 쎄웠던 가정이나 추론 과정을 정확하게 이해해야 한다. 이것은 자식 클래스의 작성자가 부모 클래스의 구현 방법에 대한 정확한 지식을 가져야 한다는 것을 의미한다. → 결합도를 높이고, 상속이 초래하는 부모 클래스와 자식 클래스의 강한 결합이 코드를 수정하기 어렵게 만든다. 
    
    > 상속을 위한 경고 1
    자식 클래스의 메서드 안에서 super 참조를 이용해 부모 클래스의 메서드를 직접 호출할 경우 두 클래스는 강하게 결합된다. Super 호출을 제거할 수 잇는 방법을 찾아 결합도를 제거하라
    > 
    
    → 취약한 기반 클래스 문제 (상속 관계로 연결된 자식 클래스가 부모 클래스의 변경에 취약해지는 현상)
    

### 취약한 기반 클래스 문제

캡슐화위협

객체 지향의 기반은 캡슐화를 통한 변경의 통제

**불필요한 인터페이스 상속 문제**

Vector : 임의의 위치에서 요소를 추출하고 삽입할 수 있는 리스트 자료 구조

Stack : LIFO

> 상속을 위한 경고 2
상속받은 부모 클래스의 메서드가 자식 클래스의 내부 구조에 대한 규칙을 깨트릴 수 있다.
> 

**메서드 오버라이딩 오작동 문제**

```java
public class InstrumentedHashSet<E> extends HashSet<E>{
  private int addCount = 0;
	
	@Override
	public boolean add(E e){
		addCount++;
		return super.add(e);
	}

	@Override
	public boolean addAll(Collection<? extends E> c){
		addCount += c.size();
		return super.addAll(c);
	}
}
```

```java
InstrumentedHashSet<String> languages = new InstrumentedHashSet<>():
languages.addAll(Arrays.asList("java". "ruby", "scala")):

//=> addCount값이 3이라고 생각되겠지만,
6결과가 나옴 InstrumentedHashSet의 addAll이 실행되고, 
부모의 addAll 이실행되는데 부모 안에서 또 add 가되서 결과적으로 6

```

해결 방안 

1. InstrumentedHashSet 의 addAll 지우기
    
    → 추후에 HashSet의 addAll 메서드가  add 메세지를 전송하지 않게 되면 카운트 누락 가능성
    
2. 미래의 수정까지 감안한 더 좋은 해결책은 super을 사용하지 않고 작성
    
    → 오버라이딩된 addAll 의 메서드 구현이 HashSet와 동일.. , 또한 부모 클래스의 코드를 그대로 가져오는 방법이 항상 가능한 것도 아님
    

> 상속을 위한 경고 3
자식 클래스가 부모 클래스의 메서드를 오버라이딩 하는 경우 클래스가 자신의 메서드를 사용하는 방법에 자식 클래스가 결합될 수 있다.
> 

조슈아 블로치는 클래스가 상속되기를 원한다면 상속을 위해 클래스를 설계하고 문서화 해야 한다고 말함.. → 설계는 트레이드 오프 활동… 상속은 코드 재사용을 위해 캡슐화를 희생시킨다!

**부모 클래스와 자식 클래스의 동시 수정 문제**

음악 목록을 추가할 수 있는 플레이리스트 구현

```java
public class Song{
	private String singer;
	private String title;

	public Song(String singer, String title){
		...
	}	
	
	+ getSinger()
	+ getTilte()
}
```

```java
public class PlayList{
	private List<Song> tracks = new ArrayList<>();
	
	public void append(Song song){
		getTracks().add(song);		
	}	

	public LIst<song> getTracks(){
		return tracks;
	}
}
```

플레이리스트에서 노래를 삭제할 수 있는 기능이 추가 요청

```java
public class PersonalPlayList extends Playlist{
	public void remove(Song song){
		getTracks().remove(song);
	}	
}
```

문제는 지금 부터..!!! 요구사항이 변경되어 PlayList에서 노래의 목록 뿐만 아니라 가수 별 **노래의 제목도 함께 관리**해야 한다고 가정

```java
public class PlayList{
	private List<Song> tracks = new ArrayLIst<>();
	**private Map<String, String> singers = new HashMap<>();**
	
	public void append(Song song){
		getTracks().add(song);		
	}	

	public List<song> getTracks(){
		return tracks;
	}
	
	**public Map<String, String> getSingers(){
		return singers;
	}**
}
```

→ 위 소스가 정상적 동작하려면… PersonalPlayList 의 remove 메서드도수정해야 함.. 

```ruby
public class PersonalPlayList extens Playlist{
	public void remove(Song song){
		getTracks().remove(song);
		getSingers().remove(song.getSinger());
	}	
}
```

> 상속을 위한 경고4
> 
> 
> 클래스를 상속하면 결합도로 인해 자식 클래스와 부모 클래스의 구현을 영원히 변경하지 않거나, 자식 클래스와 부모 클래스를 동시에 변경하거나 둘 중 하나를 선택할 수 밖에 없다. 
> 

## Phone 다시 살펴보기

상속으로 인해 발생하는 피해 최소화하기 위한 방법으로 가장 일반적인 방법은 자식 클래스와 부모 클래스 모두 **추상화**에 의존하도록 수정!

**코드 중복 제거 원칙**

1. 두 메서드가 유사하게 보인다면 차이점을 메서드로 추출하라
    1. 메서드 추출을 통해 두 메서드를 동일한 형태로 보이게 만들 수 있다.
2. 부모 클래스의 코드를 하위로 내리지 말고 자식 클래스의 코드를 상위로 올려라
    1. 자식 클래스의 코드를 올리는 편이 재사용성과 응집도 측면에서 더 뛰어나다

**소스코드**

1. 차이점을 메서드로 추출

```jsx
//calculateCallFee: Phone과 NightlyDiscountPhone 차이점을 메서드로 추출
calculateCallFee(call) 
```

1. 부모클래스 추가 후 중복 코드를 부모 메서드로 올리기
    1. 컴파일 시 발생하는 오류를 해결하며 코드를 완성 
        
        Tip. 공통 코드를 옮길 때 인스턴스 변수보다 메서드를 먼저 이동시킴 → 컴파일 에러를 통해 꼭 필요한 인스턴스 변수만 옮길 수 있다. 
        

```java
public abstract class AbstractPhone {
		(2)    
		private List<Call> calls = new ArrayList<>(); // 컴파일시 발생한 오류, 부모 클래스로 옮긴다.

	  (1)
    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
						(3)
            result = result.plus(calculateCallFee(call));
        }

        return result;
    }

		// Phone과 Nightly의 내부 구현이 달라추상 메서드를 만들어 준 것
    abstract protected Money calculateCallFee(Call call);  
}
```

**추상화가 핵심이다**

<img width="608" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/ac096782-e62d-437f-8d5e-75698c1a3e36">


부모와 자식 클래스 모두 추상화에 의존하게 됨으로써 다음과 같은 이점을 얻을 수 있었다. 

- 단일 책임 원칙
    - phone에는 일반 요금제를 처리하는데 필요한 인스턴스 변수와 메서드만, NightDiscountPhone에는심야 할인 요금제와 관련된 인스턴스 변수와 메서드만 존재하게 된다. 이로써 각 클래스는 서로 다른 변경의 이유를 가질 수 있게 되었다. 이제 abstract Phone 은 전체 통화 목록을 계산하는 방법이 바뀔 경우에만, phone은일반 요금제의 통화 한건을 계산하는 방식이 바뀔 경우에만, NightDiscountPhone은심야 할인 요금제의 통화 한건을 계산하는 방식이 바뀔 경우에만 변경 된다.  ( 단일 책임 원칙)
- 낮은 결합도
    - 자식들이 부모 클래스에서 정의한 추상 메서드인 calculateFee에만 의존하게 되었다. calculateFee메서드의 시그니처가 변경되지 않는 한 부모 클래스의 내부 구현이 변경 되더라도 자식 클래스는 영향 받지않게 되었다.
- 개방 폐쇄 원칙
    - 새로운 요금제를 추가하게 되면 AbstractPhone을 상속 받고 calculateFee 메서드만 오버라이딩 하면 된다. 다른 클래스는 수정할 필요가 없다.

1. 의도를 드러내는 이름 선택

```java
public abstract class Phone { ... }

public class RegularPhone extends Phone { ... }

public class NightlyDiscountPhone extends Phone { ... }
```

<img width="578" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/30d646ff-b6a7-40a2-ac2c-9cb96dec4fde">


**세금 추가하기** 

1. Phone에 인스턴스 변수 taxRate를 추가했고 두 인스턴스 변수의 값을 초기화하는 생성자를 추가

```java
public abstract class Phone {
    private double taxRate; // 추가
    private List<Call> calls = new ArrayList<>();

    public Phone(double taxRate) { // 추가
        this.taxRate = taxRate;
    }

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }

        return result.plus(result.times(taxRate)); // 추가
    }

    protected abstract Money calculateCallFee(Call call);
}
```

1. 자식클래스도 수정

```java
public class RegularPhone extends Phone {
    private Money amount;
    private Duration seconds;

    public RegularPhone(Money amount, Duration seconds, double taxRate) {
        super(taxRate);
        this.amount = amount;
        this.seconds = seconds;
    }

---------------------------------------------------------------------------------------------

public class NightlyDiscountPhone extends Phone {
    private static final int LATE_NIGHT_HOUR = 22;

    private Money nightlyAmount;
    private Money regularAmount;
    private Duration seconds;

    public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate) {
        super(taxRate);
        this.nightlyAmount = nightlyAmount;
        this.regularAmount = regularAmount;
        this.seconds = seconds;
    }
```

**이전 코드와 비교**

인스턴스 초기화 로직을 변경하는 것이 두 클래스에 동일한 세금 계산 코드를 중복시키는 것보다는 현명한 선택이다.

### 차이에 의한 프로그래밍

**의미**

- 익숙한 개념을 이용해 새로운 개념을 쉽고 빠르게 추가하는 것

**목표**

- 중복 코드를 제거하고 코드를 재사용하는 것
- 달성 방법
    - 상속
        - 여러 클래스에 공통적으로 포함돼 있는 중복 코드를 하나의 클래스로 모은다.
        - 원래 클래스들에서 중복 코드를 제거한 후 중복 코드가 옮겨진 클래스를 상속 관계로 연결한다.
        - 컴파일 에러들을 하나하나 해결해 가면 성공~
    - 합성(Better)

<img width="605" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/8740564e-5e3f-49ec-806c-a538794b9265">
