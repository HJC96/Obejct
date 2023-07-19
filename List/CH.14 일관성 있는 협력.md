일관성 있는 협력 패턴을 적용하면 코드가 이해하기 쉽고 직관적이며 유연해진다.

- 핸드폰 과금 시스템 변경하기

기본정책 2개에서 4개로 확장

<img width="712" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/0dc5ea38-856f-4757-9a5d-51f88386543e">


조합 가능한 모든 경우의 수

<img width="714" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/d4e0590a-eeaf-4889-8836-26d194401613">


구현할 클래스의 구조

<img width="774" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f027b893-a537-48bf-9f8e-545829fe9879">


- 고정 요금 방식 구현하기
    
    ```ruby
    public class FixedFeePolicy extends BasicRatePolcy{
    	private Money amount;
    	private Duration duration;
    	
    	생성자
    	
    	@Override
    	protected Money calculateCallFee(Call call){
    		return amount.times(call.getDuration().getSeconds()/seconds.getSeconds());
    	}
    }
    ```
    
- 시간대별 방식 구현하기
    - 통화 시간을 분활하는 방법을 결정하는 클래스 추가
        
        ```ruby
        public class DateTimeInterval{
        	private LocalDateTime from;
        	private LocalDateTime to;
        	
        	public static DateTimeInterval of(LocalDateTime from, LocalDateTime to){
        		return new DateTimeInterval(frome, to);
        	}
        	
        	public static DateTimeInterval toMidnight(LocalDateTime from){
        		return new DaTimeInterval(
        							 from,
        							 LocalDataTime.of(from.toLocalDate(), LocalTime.of(23,59,59, 999_999_999)));
        	}
        	
        	public static DateTimeInterval fromMidnight(LocalDateTime to){
        		return new DateTimeInterval(
        								LocalDateTIme.of(to.toLocalDate(), LocalTime.of(0,0),
        								to);
        	}
        	public static DateTimeInterval during(LocalDate date){
        		return new DateTimeInterval(
        								LocalDateTime.of(date, LocalTime.of(0.0)),
        								LocalDateTime.of(date, LocalTime.of(23,59,59,999_999_999)));
        	}
        
        	private DateTimeInterval(LocalDateTime from, LocalDateTime to){
        		this.from = from;
        		this.to = to;
        	}
        	
        	public Duration duration(){
        		return Duration.betwween(from,to);	
        	}
        	
        	public LocalDateTime getFrom(){
        		return from;
        	}	
        
        	public LocalDateTime getTo(){
        		return to;
        	}
        		
        	public List<DateTimeInterval> splitByDay(){
        		if(days()>1){
        			return splitByDay(days());
        		}
        		return Arrays.asList(this);
        	}
        	
        	private int days(){ //from과 to사이에 포함된 날짜 수
        		return Period.between(from.toLocalDate(), to.toLocalDate())
        													.pulsDays(1)
        													.getDays();
        	}
        
        	private List<DateTimeInterval> splitByDay(int days){
        		List<DateTimeInterval> result = new ArrayList<>();
        		addFirstDay(result);
        		addMiddleDays(result, days);
        		addLastDasy(result);
        		return result;
        	}
        
        	private void addFirstDay(List<DateTimeInterval> result){
        		result.add(DateTimeInterval.toMidnight(from));
        	}
        
        	private void addMiddleDays(List<DateTimeInterval> result, int days){
        		for(int loop=1; loop<days; loop++}{
        			result.add(DateTimeInterval.during(from.toLocalDate().plusDays(loop)));
        	}
        	private void addLastDasy(List<DateTimeInterval> result){
        		result.add(DateTimeInterval.toMidnight(from));
        	}
        }
        ```
        
        ```ruby
        public class Call{
        	
        	private DateTimeInterval interval;
        	
        	public Call(LocalDateTime from, LocalDateTime to){
        		this.interval = new DateTimeInterval**(from, to);**
        	}
        	
        	public Duration getDuration(){
        		return interval.duration();
        	}
        	public LocalDateTime getFrom(){
        		return interval.from;
        	}	
        
        	public LocalDateTime getTo(){
        		return interval.to;
        	}
        
        	public DateTimeInterval getInterval(){
        		return interval;
        	}
        	
        	public List<DateTimeInterval> spliteByDay(){
        		return interval.spliteByDay();
        	}
        }
        ```
        
    
    ```ruby
    public class TimeOfDayDiscountPolicy extends BasicRatePolicy{
    	private List<LocalTime> starts = new ArrayList<>();
    	private List<LocalTime> ends = new ArrayList<>();
    	private List<LocalTime> durations = new ArrayList<>();
    	private List<LocalTime> amounts = new ArrayList<>();
    
    	@Override
    	protected Money calculateCallFee(Call call){
    		Money result = Money.Zero;
    		for(DateTimeInterval interval:call.spliteByDay()){
    			result.plus(amounts..get(loop).times(
    				Duration.between(from(interval, starts.get(loop)), 
    													to(interval,ends.get(loop))).getSeconds()
    													/durations.get(loop).getSeconds()));
    			}
    		}
    		return result;
    	}
    
    	private LocalTIme from(DateTimeInterval interval, LocalTime from){
    		return interval.getFrom().toLocalTime().isBefore(from) ?
    						from :
    						interval.getFrom().toLocalTime();
    	}
    	
    	private LocalTime to(DateTimeInterval interval, LocalTime to){
    		return interval.getTo().toLocalTime().isAfter(to)?
    					 to:
    					 interval.getTo().toLocalTime();
    	}
    }
    	
    ```
    
- 요일별 방식 구현하기
    
    ```ruby
    public class DayOfWeekDiscountRule{
    	private List<DayOfWeek> dayOfWeeks = new ArrayList<>();
    	private Duration duration = Duration.ZERO;
    	private Money amount = Money.ZERO;
    	
    	public DayOfWeekDiscountRule(List<DayOfWeek> dayOfWeeks, Duration duration, Money amount ){
    	생성자
    	
    	}
    
    	public Money calculate(DateTImeInterval interval){
    		if(dayOfWeeks.contains(interval.getFrom().getDayOfWeek()))
    			return amount.times(interval.duration().getSeconds()/duration.getSeconds());
    	
    		return Money.ZERO;
    	}
    
    }
    ```
    
    ```ruby
    public class DayOfWeekDiscountPolicy extends BasicRatePolicy{
    	private List<DayOfWeekDiscountRule> rules = new ArrayList<>();
    	
    	public DayOfWeekDiscountPolicy(List<DayOfWeekDiscountRule> rules){
    		this.rules = rules;
    	}
    	
    	@Override
    	protected Money calculateCallFee(Call call){
    		Money result = Money.ZERO;
    		for(DateTimeInterval interval : call.getInterval().splitByDay())
    			for(DayOfWeekDiscountRule rule:rules){
    				result.plus(rule.calculate(interval);
    		}
    		return result;
    	}
    }
    ```
    
- 구간별 방식 구현
    
    
    하기 앞서..
    
    어떻게 구현을 시작할 것인가?? 우선 BasicRatePolicy을 상속 받는 DurationDiscountPolicy클래스를 추가하고 calcultateCallFee를 Overriding 하고 나서 메서드 내부를 채우기 시작할 것이다. 
    
    그리고 구간별 방식 구현하기 위해 전체 통화 시간을 일정한 시간 간격으로 분할한 후 분할된 구간별로 규칙을 다르게 부과할 수 있어야 한다. 
    
    그런데 지금 고정 요금 방식, 시간대별, 요일별 구현 방식이 다르고 요금별 방식 구현하는데 새로운 구현 방식을 사용하면 코드 사이의 일관성은 더 어긋나게 된다. → 코드를 이해하기 어렵게 만든다. 
    
    ```ruby
    public class DurationDiscountRule extends FixedFeePolicy{
    	private Duration from;
    	private Duration to;	
    	
    	public DurationDiscountRule(Duration from, Duration to, Money amount, Duration seconds){
    		super(amount,secods);
    		this.from =from;
    		this.to = to;
    	}
    	
    	public Money calculate(Call call){
    		if(call.getDuration().copareTo(to)>0){
    			return Money.ZERO;
    		}
    		if(call.getDuration().copareTo(from)<0){
    			return Money.ZERO;
    		}
    		
    		//부모클래스의 calculateFee(phone)은 phone플래스를 파라미터로 받는다.
    		//calculateFee(phone)을 재사용하기 위해
    		//데이터에 전달할 용도로 임시 Phone을 만든다.
    		
    		Phone phone = new Phone(null);
    		phone.call(new Call(call.getFrom().plus(from),
    									 call.getDuration().campareTo(to)>0? call.getFrom().plus(to) : call.getTo()));
    		
    		return super.calculateFee(phone);
    	}
    }
    ```
    
    ```ruby
    public class DurationDiscountPolicy extends BasicRatePolicy{
    	private List<DurationDiscountRule> rules = new ArrayList<>();
    	
    	public DurationDiscountPolicy(List<DurationDiscountRule> rules){
    		this.rules = rules;
    	}
    	
    	@Override
    	protected Money calculateCallFee(Call call){
    		Money result = Money.ZERO;
    		for(DurationDiscountRule rule:rules){
    			result.plus(rule.calculate(call);
    		}
    		return result;
    	}
    }
    ```
    

### 설계의 일관성 부여하기

일관성 있는 설계를 어떻게 만들까? 

- 다양한 설계 경험을 익히라
- 널리 알려진 디자인 패턴을 학습하라

협력을 일관성 있게 만들기 위한 기본 지침

- 변하는 개념을 변하지 않는 개념으로부터 분리하라.
- 변하는 개념을 캡슐화하라.

<img width="703" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/b2726924-8799-44d9-8050-2a4c7611bf46">


<img width="594" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c3f60638-8a9a-4bd5-8704-8951ce8e1300">


영화 예매 시스템으로 돌아가보자~~

```csharp
public class ReservationAgency{
	public Reservation reserve(Screening screening, Customer customer, int audienceCount){
    **//할인 조건 종류 결정**				
		for(DiscountCondition condition : movie.getDiscountConditions()){
			if(condition.getType() == DiscountConditionType.RERIOD)
				//기간조건 일 경우
			else
				//회차 조건인 경우
		}

		**//할인 정책 결정** 
		if(discountable){
			switch(movie.getMovieType()){
				case AMOUNT_DISCOUNT:
							// 금액할인 정책인 경우 
				case PERCENT_DISCOUNT:
							// 비율 할인 정책인 경우 
				case NONE_DISCOUNT:
							// 할인 정책이 없는 경우 
			}
		}
		else
			//할인 적용이 불가능한 경우 
	}
}
```

Movie는 현재의 할인 정책이 어떤 종류인지 판단하지 않는다. 단지 DiscountPolicy로 향하는 참조를 통해 메시지를 전달할 뿐이다. 할인 정책의 구체적인 종류는 메시지를 수신한 객체의 타입에 따라 달라지며 실행할 메서드를 결정하는 것은 순전히 메시지를 수신한 객체의 책임이다. DiscountPolicy역시 할인 조건의 종류를 판단하지 않는다. 단지 DiscountDondition으로향하는 참조를 통해 메시지를 전달할 뿐이다. 객체지향적인 코드는 조건을 판단하지 않는다. 단지 다음 객체로 이동할 뿐이다. 

- 변하는 개념을 변하지 않는 개념으로부터 분리하라.
    - 할인 정책과 할인 조건의 타입을 체크하는 하나하나의 조건문이 개별적인 변경이였다 → 각 조건문을 개별적인 객체로 분리했고 이 객체들과 일관성 있게 협력하기 위해 타입 계층을 구성했다. 그리고 이 타입 계층을 클라이언트로부터 분리하기 위해 역할을 도입하고, 최정적으로 이 역할은 추상 클래스와 인터페이스로 구현했다. 결과적으로 변하는 개념을 별도의 서브타입으로 분리한 후 이 서브타입들을 클라이언트로부터 캡슐화 한 것이다.
- 변하는 개념을 캡슐화하라.

<img width="698" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/39558b5c-4691-4713-83e2-04f73fd89283">


<img width="717" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/e5ac4843-7e89-48ce-95cd-ccb8ae41f31e">


켑슐화 다시 살펴 보기

데이터 은닉(data hiding) : 오직 외부에 공개된 메서드를 통해서만 객체의 내부에 접근할 수 있게 제한함으로써 객체 내부의 상태 구현을 숨기는 기법 ( 클래스의 모든 인스턴스 변수는 private으로 선언해야 하고 오직 해당 클래스의 메서드 만이 인스턴스 변수에 접근 가능 )

캡슐화는 데이터 은닉 이상이다! 

캡슐화는 변하는 어떤 것이든 감추는 것이다. 

- Ex) 객체의 퍼블릭 인터페이스와 구현을 분리

<img width="647" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/6c3e9d98-2f18-4bc9-bee2-30dabdafaa64">


<img width="733" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/1217de81-06e6-46b8-9b77-a4562c5dae09">


- 데이터 캡슐화
- 메서드 캡슐화
- 객체 캡슐화 ( = 합성)
    - Movie 클래스는 DiscountPolicy 타입의 인스턴스 변수 discountPolicy를 포함하는데 private가시성을 가지기 때문에 둘 사이의 관계를 변경하더라도 외부에 영향을 미치지 않는다. 다시 말해서 객체와 객체 사이의 관계를 캡슐화한다.
- 서브타입 캡슐화 ( = 다형성의 기반)

캡슐화란 단지 데이터 은닉을 의미하는 것이 아니다. 코드 수정으로 인한 파급효과를 제어할 수 있는 모든 기법이 캡슐화의 일종이다. 

변경을 캡슐화할 수 있는 다양한 방법이 존재하지만 협력을 일관성 있게 만들기 위해 가장 일반적으로 사용하는 방법은 서브타입 캡슐화의 객체 캡슐화를 조합하는것

- 변하는 부분을 분리해서 타입 계층을 만든다.
    - 변하는 부분들의 공통적인 행동을 추상 클래스나 인터페이스로 추상화 한 후 변하는 부분들이 이 추상 클래스나 인터페이스를 상속받게 만든다. 이제 변하는 부분은 변하지 않는 부분의 서브타입이 된다.
- 변하지 않는 부분의 일부로 타입 계층을 합성한다.
    - 앞에서 구현한 타입 계층을 변하지 않는 부분에 합성한다. 변하지 않는 부분에서는 변경되는 구체적인 사항에 결합돼서는 안된다. 의존성 주입과 같이 결합도가 느슨하게 유지할 수 있는 방법을 이용해 오직 추상화에만 의존하게 만든다. 이제 변하지 않는 부분은 변하는 부분의 구체적인 종류에 대해서는 알지 못할 것이다. 변경이 캡슐화된 것이다. Movie가 DiscountPolicy를 합성 관계로 연결하고 생성자를 통해 해결한 이유가 바로 이 때문이다.

### 일관성 있는 기본 정책 구현하기

변경 분리하기

<img width="736" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/28cf2668-c2ca-4d49-9d76-82e579a7dd86">


<img width="616" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/386de5d0-4f51-4fa2-84ab-119438884d2e">


<img width="595" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f9066ce4-94b2-4a0a-a899-6ff830b0bc7b">


**변경 캡슐화하기**

변하지 않는 것은 규칙이다. 변하는 것은 적용조건이다. 따라서 규칙으로부터 적용조건을 분리해서 추상화한 후 시간대별, 요일별, 구간별 방식을 이 추상화의 서브타입으로 만든다. 이것이 서브타입 캡슐화다. 그 후에 규칙이 적용조건을 표현하는 추상화를 합성 관계로 연결한다. 이것이 객체 캡슐화다. 

FeeRule = 규칙

feePerDuration = 단위 요금

FeeCondition = 적용 조건을 구현한 인터페이스

<img width="725" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/8335f6b2-d0c2-437e-afe8-f46a2bfa3e8e">


**협력 패턴 설계하기**

<img width="731" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/3775de48-e9db-4b11-a989-c4c654c6a889">


협력은 BasicRatePolicy가 calculateFee메세지를 수신했을 때 시작된다. 

BasicRatePolicy의 calculateFee메서드는 인자로 전달받은 통화 목록 (List<Call>타입) 의 전체 요금을 계산한다. BasicRatePolicy는 각 목록에 포함된 각 Call별로 FeeRule의 calculateFee메서드를 실행한다. FeeRule은 하나의 Call에 대해 요금을 계산하는 책임을 수행한다. 

하나의 Call 요금을 계산하기 위해서는 두개의 작업이 필요하다. 하나는 전체 통화 시간을 각 ‘규칙’의 ‘적용조건’을 만족하는 구간들로 나누는 것이다. 다른 하나는 이렇게 분리된 통화 구간에 ‘단위요금’을 적용해서 요금을 계산하는 것이다. 

<img width="750" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/a71fc467-7d23-4461-a5bf-71c94ee9b8ed">


적용 조건을 가장 잘 알고 있는 정보 전문가인 FeeCondition에게할당

이렇게 분리된 통화 구간에 단위요금을 적용해서 요금을 계산하는 작업은 요금기준 정보 전문가인 FeeRule이담당 

<img width="795" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/cb59b580-1198-4907-b70c-36548e19c7dd">

**추상화 수준에서 협력 패턴 구현하기** 

```jsx
public interface FeeCondition{
	//적용조건에 만족하는 기간을 구한 후 List에 담아 반환
	List<DateTImeINterval> findTimeInterval(Call call);	
}
```

```jsx
public class FeeRule{
	private FeeCondition feeCondition;
	private FeePerDuration feePerDuration;

	public FeeRule(FeeCondition feeCondition, FeePerDuration feePerDuration){
		this.feeCondition = feeCondition;
		this.feePerDuration = feePerDuration;
	}
	
	public Money calculateFee(Call call){
		return feeCondition.findTimeInterval(call)
					 .stream()
					 .map(each -> feePerDuration.calculate(each))
					 .reduce(Money.ZERO, (first,second) -> first.plus(second));
	}
}
```

```jsx
public class FeePerDuration {
	private Money fee;
	private Duration duration;
	
	public FeePerDuration(Money fee, Duration duration){
		this.fee = fee;
		this.duration = duration;
	}

	public Money calculate(DateTimeInterval interval){
		return fee.times(Math.ceil((double)interval.duration().toNanos()/duration.toNanos()));
	}
}
```

```jsx
public class BasicRatePolicy implements RatePolicy{
	private List<FeeRule> feeRules = new ArrayList<>();
	
	public BasicRatePolicy (FeeRule ...feeFules){
		this.feeFules = Array.asList(feeFules);
	}

	@Override
	public Money calculateFee(Phone phone){
		return phone.getCalls()
								.stream()
								.map(call -> calculate(call))
								.reduce(Money.ZERO, (first, seconds)-> first.plus(second));
	}

	private Money calculate(Call call){
		return feeRules
									.stream()
                  .map(rule -> rule.calculateFee(call))
									.reduce(Money.ZERO, (first, seconds)-> first.plus(second));
	}

}
```

구체적인 협력 구현하기 

- 시간대별 정책
    
    ```jsx
    public class TimeOfDayFeeCondition implements FeeCondition{
    	private LocalTime from;
    	private LocalTime to; 
    	
    	public TimeOfDayFeeCondition(LocalTime from, LocalTime to) {
    		this.from = from;
    		this.to = to;
    	}
    	
    	@Override
    	public List<DateTimeInterval> findTimeIntervals(Call call){
    		reutrn call.getInterval().splitByDay()
    						.stream()
    						.filter(each -> from(each).isBefore(to(each)))
    						.map(each -> DateTimeInterval.of(
    																LocalDateTime.of(each.getFrom().toLocalDate(), from(each),
    																LocalDateTime.of(each.getTo().toLocalDate(), to(each),
    	 					.collect(Collectors.toList());
    	}
    
    	private LocalTime from(DateTimeInterval interval){
    		return interval.getFrom().toLocalTime().isBefore(from)?
    							from: interval.getFrom().toLocalTime();
    	}
    	private LocalTime to(DateTimeInterval interval){
    		return interval.getTo().toLocalTime().isAfter(to)?
    							to: interval.getTo().toLocalTime();
    	}
    }
    ```
    
- 요일별 정책
    
    ```jsx
    public class DayOfWeekFeeCondition implements FeeCondition{
    	private List<DayOfWeek> dayOfWeeks = new ArrayList<>();
    
    	public DayOfWeekCondition(DayofWeek ... dayOfWeeks){
    		this.dayOfWeeks = dayOfWeeks ;
    	}
    
    	@Override
    	public List<DateTimeInterval> findTimeIntervals(Call call){
    		reutrn call.getInterval()
    							 .splitByDay()
    	 						 .stream()
    						   .filter(each -> dayOfWeeks.contains(each.getFrom().getDayOfWeek()))
    	 						 .collect(Collectors.toList());
    
    	}
    }
    ```
    
- 구간별 정책
    
    ```jsx
    public class DurationFeeCondition implements FeeCondition{
    	private Duration from;
    	private Duration to;
    
    	public DurationFeeCondition (Duration from, Duration to){
    		this.from = from;;
    		this.to = to;
    	}
    	
    	@Override
    	public List<DateTimeInterval> findTimeIntervals(Call call){
    		if(call.getInterval().duration().compareTo(from) < 0)	
    			return Collections.emptyList();
    	
    		return Arrays.asList(DateTimeInterval.of(
    							call.getInterval().getFrom().plus(from),
    							call.getInterval().duration().compareTo(to)>0?
    									call.getInterval().getFrom().plus(to) :
    									call.getInterval().getTo()));
    	}
    }
    ```
    

변경을 캡슐화 해서 일관성 있게 만들면 어떤 장점을 얻을 수 있는지 정리~

- 변하지 않는 부분을 재사용 할 수 있다.
- 새로운 기능을 추가하기 위해 오직 변하는 부분만 구현하면 되기 때문에 기능을 쉽게 완성할 수 있다.
    
    → 코드의 재사용성이 향상되고 테스트해야 하는 코드의 양이 감소한다.
    
- 구조를 강제할 수 있기 때문에 설계의 일관성이 무너지지 않는다.
- 기존 코드를 이해하기 쉽다.

협력 패턴에 맞추기

고정 요금 정책은 규칙이라는 개념이 필요하지 않고 단위요금 정보만 있으면 충분한데… 어떡하지..

다른 협력..? No!가급적 기존의 협력 패턴에 맞추는 것이 가장 좋은 방법

```jsx
public class FIxedFeeCondition implements FeeCondition{
	@Override
	public List<DateTimeInterval> findTimeIntervals(Call call){
		return Arrays.asList(call.getInterval());
	}
}
```

<img width="698" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f324a8d7-fbf7-4952-a41f-38a0247de5fd">


지속적으로 개선하라
