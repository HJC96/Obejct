# CH.12 다형성 
### 상속을 사용 하기 전 질문 
단순히 코드를 재사용 하기 위해서? ❌
클라이언트 관점에서 인스턴스들을 동일하게 행동하는 그룹으로 묶기 위해서 ⭕
→ 상속의 목적은 코드 재사용이 아니다. **상속은 타입 계층을 구조화**하기 위해 사용해야 한다.

이번 장에서 상속의 관점에서 다형성이 구현되는 기술적인 메커니즘을 살펴보기로 한다. 이번 장을 읽고 나면 다형성이 런타임에 메세지를 처리하기에 적합한 메서드를 동적으로 탐색하는 과정을 통해 구현되며, 상속이 이런 메서드를 찾기 위한 일종의 탐색 경로를 클래스 계층의 형태로 구현하기 위한 방법이라는 사실을 이해하게 될 것이다. 

이번 장에 목표는 포함 다형성의 관점에서 런타임에 상속 계층 안에서 적절한 메서드를 선택하는 방법을 이해하는 것 

### 다형성 (Polymorphism)
하나의 추상 인터페이스에 대해 코드를 작성하고 이 추상 인터페이스에 대해 서로 다른 구현을 연결할 수 있는 능력

<img width="629" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/746d2e9c-f0cd-4c3b-a8a2-990e78f2535d">

### 다형성의 네가지
다형성
- 유니버셜
  - 매개변수
  - 포함
- 임시
  - 오버로딩
  - 강제

- **오버로딩**
  하나의 클래스 안에 동일한 이름의 메서드가 존재하는 경우
  ```scala
  public Money plus(Money aumout){...}
  public Money plus(long aumout){...}
  public Money plus(BigDecimal aumout){...}
  ```
- **강제**
  언어가 지원하는 자동적인 타입 변환이나 사용자가 직접 구현한 타입 변환을 이용해 동일한 연산자를 다양한 타입에 사용할 수 있는 방식
    
  정수 + 정수 = 정수
    
  (강제로 문자로)정수 + 문자열 = 문자열
- **매개변수**
  [제너릭 프로그래밍](https://tlatmsrud.tistory.com/59)
    
  클래스의 인스턴스 변수나 메서드의 매개변수 타입을 임의의 타입으로 선언한 후 사용하는 시점에 구체적인 타입으로 지정하는 방식    
- **포함** -> 우리가 일반적으로 말하는 다형성 업캐스팅
  메세지가 동일하더라도 수신한 객체의 타입에 따라 실제로 수행되는 행동이 달라지는 것 능력 서브 타입 다형성이라고도 함.
    
  구현하는 일반적인 방법은 상속인데 (그래서 서브타입 다형성이라고..) 상속 외에 다른 방법으로도 구현 가능함 이거에 대해 자세히 알아 보자!
    
### 상속의 양면성

강의를 이수한 학생의 수와 낙제한 학생의 수와
등급별로 학생들의 분포 현황을 나타내는 
수강생들의 성적을 계산하는 프로그램 만들기~!! 

```java
Pass: 3 Fail: 2, A:1 B:1 C:1 D:0 F:2
```

다행히도 프로젝트에는 Pass: 3 Fail: 2 같은 형식으로 통계를 출력하는 Lecture 클래스가 존재했음

```java
public class Lecture{
	private int pass;
	private String title;
	private List<Integer> scores = new ArrayList<>();

	public Lecture(String title, int pass, List<Integer> scores)
	{...생성자}
	
	public double average(){
		return scores.stream()
									.mapToInt(Integer::intValue)
									.average().ofElse(0);
	}

	public List<Integer> getScores(){
		return Collections.unmodifiableList(scores);
	}
	
	public String evaluate(){
		return String.format("Pass %d Fail: %d", passCount(), failCount());
	}

	private long passCount(){
		return scores.stream().filter(score->score>=pass).count();
	}

	private long failCount(){
		return scores.size() - passCount();
	}
}

```

```java
//이수 기준이 70점인 객체지향 프로그래밍 과목의 
//수강생 5명에 대한 성적 통계를 구하는 코드
Lecture lecture = new Leture("객체지향 프로그래밍", 70, Arrays.asList(81,95,75,50,45));
String evaluration = lecture.evalute(); // 결과 Pass: 3 Fail: 2
```

등급별 통계

```java
public class GradeLecture extends Lecture{
	private List<Grade> grades;
	
	public GradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores){
		super(name, pass, scores);
		this.grades = grades;
	}
	
 @Override
	public String evaluate(){
		return super.evaluate() + ", " + gradeStatistics();
	}
	
	private String gradeStatistics(){
		return grades.stream()
								.map(grade=>format(grade))
								.collect(joining(" "));
	}

	private String format(Grade grade){
		return String.format("%s:%d", grad.getName(), gradeCount());
	}

	private long gradeCount(Grade grade){
		return getScore().stream()
											.filter(grade::include)
											.count();
	}

}
```

```java
public class Grade{
	private String name;
	private int upper, lower;
	
	private Grade(String name, int upper, int lower){
		this.name = name;
		this.upper = upper;
		this.lower = lower;
	}

	public String getName() => name;
	public boolean isName(String name) => this.name.equals(name);
	public boolean include(int score) => score>=lower && score <=upper;
}
```

```java

Lecture lecture = new GradeLecture ("객체지향 프로그래밍",
															70,
															Arrays.asList(new Grade("A", 100, 95),
																						new Grade("B", 94, 80),
																						new Grade("C", 79, 70),
																						new Grade("D", 69, 50),
																						new Grade("E", 49, 0))
															Arrays.asList(81,95,75,50,45));
lecture.evalute(); // Pass: 3 Fail: 2, A:1 B:1 C:1 D:0 F:2
```

상속을 설명하는데 필요한 예제 준비 완료! 이 예제를 이용해 데이터와 행동이라는 두가지 관점에서 상속이 가지는 특성을 살펴보자

- **데이터 관점의 상속**
  메모리상에 생성된 객체의 모습들
  <img width="472" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/6e923fb2-1617-4541-9fcd-2df3c3ffea08">

  인스턴스를 참조하는 lecture는 GradeLecture의 인스턴스를 가리키기 떄문에 특별한 방법을 사용하지 않으면 GradeLecture 안에 포함된 Lecture의 인스턴스에 직접 접근할 수 없다. 
    
  요약하면 데이터 관점에서 상속은 자식 클래스의 인스턴스 안에 부모 클래스의 인스턴스를 포함하는 것으로 볼 수 있다. 따라서 자식 클래스의 인스턴스는 자동으로 부모 클래스에서 정의한 모든 인스턴스 변수를 내부에 포함하게 되는 것이다. 
  <img width="684" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/c656b879-e890-41c5-b72b-a1ff5c3303c8">

    

- **행동 관점의 상속**
  객체의 경우 서로 다른 상태를 저장할 수 있도록 인스턴스 별로 독립적인 메모리를 할당 받아야 한다. 하지만 메서드의 경우에는 동일한 클래스의 인스턴스끼리 공유가 가능하기 때문에 클래스는 한 번만 메모리에 로드하고 각 인스턴스별로 클래스를 가르키는 포인터를 갖게 하는 것이 경제적이다. 
    
      <img width="691" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f69bc1e8-e0f5-4527-a42a-44ec29bc4fbc">

      <img width="691" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/66b1804e-8951-4549-b66d-78198b4cc914">
    
  이 구조들을 바탕으로 객체가 실행 시점에 메서드를 탐색하는 과정을 자세히 보자
    

  메서드는 코드이기 때문에 **같은 클래스의 모든 인스턴스가 공유**한다.
  클래스 정보는 메모리(메서드 영역)에 한 번만 올라가고, 각 인스턴스는 이 정보를 참조해서 사용한다.

## 업캐스팅과 동적 바인딩
교수별로 강의에 대한 성적 통계를 계산하는 기능 추가

```java
public class Professer{
	private String name;
	private Lecture lecture;
	
	public Professer(String name, Lecture lecture){
		this.name = name;
		this.lecture = lecture;
	}

	public String complieStatics(){
		return String.format(lecture.evluate(), lecture.average();
	}

}
```

```java
Professor professer = new Professor("다익스트라", new Lecture("알고리즘", 70, Arrays.asList(81,95,75,50,45)));
professer.complieStatics() 
// [다익스트라] Pass: 3 Fail: 2 -Avg:69.2

Professor professer = new Professor("다익스트라", new GradeLecture ("알고리즘", 70, Arrays.asList(new Grade("A", 100, 95), new Grade("B", 94, 80), new Grade("C", 79, 70), new Grade("D", 69, 50), new Grade("E", 49, 0)), Arrays.asList(81,95,75,50,45)));
professer.complieStatics() 
// [다익스트라] Pass: 3 Fail: 2, A:1 B:1 C:1 D:0 F:2 -Avg:69.2
```

이처럼 코드 안에서 선언된 참조 타입과 무관하게 실제로 메세지를 수신하는 객체의 타입에 따라 실행되는 메서드가 달라질 수 있는 것은 업캐스팅과 동적 바인딩이라는 메커니즘이 작용하기 때문이다. 

- 부모 클래스(Lecture) 타입으로 선언된 변수에 자식 클래스(GradeLecture)의 인스턴스를 할당하는 것이 가능하다. 이를 **업캐스팅** 이라고 부른다.
- **선언된 변수의 타입이 아니라 메서드를 수신하는 객체의 타입에 따라 실행되는 메서드가 결정**된다. 이것은 객체지향 시스템이 메세지를 처리할 적절한 메서드를 컴파일 시점이 아니라 실행 시점에 결정하기 때문에 가능하다. 이를 **동적 바인딩**이라고 부른다.

**업캐스팅**
<img width="649" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/f5d9f020-879b-48de-be7e-42a79ec4624d">

**동적 바인딩**
동적 바인딩 = 지연 바인딩 : 실행될 메서드를 런타임에 결정하는 방식
정적 바인딩 = 초기 바인딩 = 컴파일 타임 바인딩 : 컴파일 타임에 호출할 함수를 결정하는 방식
어떤 규칙에 따라 메시지 전송과 메서드 호출을 바인딩하는 것일까?

## 동적 메서드 탐색과 다형성
객체지향 시스템은 다음 규칙에 따라 실행할 메서드를 선택한다.

1. 메서드를 수신한 객체는 먼저 자신을 생성한 클래스에 적합한 메서드가 존재하는지 검사한다. 존재하면 메서드를 실행하고 탐색을 종료한다.
2. 메서드를 찾지 못했다면 부모 클래스에서 메서드 탐색을 계속 한다. 이 과정은 적합한 메서드를 찾을 때까지 상속 계층을 따라 올라가며 계속된다.
3. 상속 계층의 가장 최상위 클래스에 이르렀지만 메서드를 발견하지 못한 경우 예외를 발생시키며 탐색을 중단한다. 

**self(this) 참조**
객체가 메시지를 수신하면 컴파일러는 self 참조라는 임시 변수를 자동으로 생성한 후 메시지를 수신한 객체를 가리키도록 설정

**동적 메서드 탐색의 두 가지 원리**
1. 자동적인 메시지 위임
   1. 자식 클래스는 자신이 이해할 수 없는 메시지를 전송받은 경우 상속 계층을 따라 부모클래스에게 처리를 위임
2. 동적인 문맥 사용
   1. 메시지 수신시 실제 어떤 메서드를 실행할지를 결정하는 것은 컴파일 시점이 아닌 실행 시점에 이뤄지며 메서드를 탐색하는 경로는 self 참조를 이용해서 결정

<img width="754" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/532131b3-5a7f-45a4-b7f6-d86cd01e01b4">

self 참조에서 상송 계층을 따라 이뤄지는 동적 메서드 탐색

**메서드 오버라이딩 (예제 1)**
Lecture 클래스의 인스턴스에 evaluate 메시지를 전송하는 코드

```java
Lecture lecture = new Lecture(...);
lecture.evaluate();
```

<img width="724" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/aa64d6af-b05d-4ccd-bdcc-4a40b18e29b5">


Lecture의 인스턴스를 가리키는 self 참조
<img width="679" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/cc949604-1472-4bf2-a17a-2bc34e4b0316">


데이터 측면을 생략한 간략한 표현
<img width="714" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/8670a629-9ddb-481c-ada2-da588aa26809">
→ Lecture 클래스 안에 evaluate 메서드가 존재하기 때문에 시스템은 메서드를 실행한 후 탐색 종료

**메서드 오버라이딩 (예제 2)**
```java
Lecture lecture = new GradeLecture(...);
lecture.evaluate();
```

<img width="679" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/fca67693-8b70-4db4-ae6a-ec9e4bbb1ff8">
→ 실제 객체를 생성한 클래스인 GradeLecture에 정의된 메서드가 실행

**결론**
자식 클래스의 메서드가 부모 클래스의 메서드를 감춘다.

**메서드 오버로딩 (예제1)**
GradeLecture 인스턴스에 average 메시지를 전송하는 경우

```java
GradeLecture lecture = new GradeLecture(...);
lecture.average("A");
```

<img width="703" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/d788cb9f-2565-44f0-a48c-0eefe6930597">


**메서드 오버로딩 (예제2)**
GradeLecture 클래스의 인스턴스에 이름은 동일하지만 파라미터를 갖지 않는 average() 메시지를 전송
```java
GradeLecture lecture = new GradeLecture(...);
lecture.average();
```

<img width="729" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/914c4988-f48c-410c-a2ed-702ff57795c7">


자식 클래스에서 응답가능한 적절한 메시지를 발견하지 못한다면 부모 클래스에서 탐색
→ **하나의 클래스** 안에서 같은 이름을 가진 메서드들을 정의하는 것만 메서드 오버로딩이 아니다.
→ 하지만 일부 언어 사이에서는 상속 계층 사이 메서드 오버로딩을 지원하지 않는 경우도 있다.
→ 부모 클래스에 선언된 이름이 동일한 메서드 전체를 숨기는 것을 **이름 숨기기**라고 한다.
-> **메서드 오버라이딩은 메서드를 감추지만 메서드 오버로딩은 사이좋게 공존한다.**
### **동적인 문맥**

**self 전송 (예제1)**

```java
public class Lecture{
 public String stats(){
		return String.format("Title: %s, Evaluation Method: %s", title, getEvaluationMethod());
	}

 public String getEvaluationMethod(){
		return "Pass or Fail";
	}
}
```

<img width="710" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/1830394d-55f9-4464-9490-ca5870f5614c">


→ self 참조가 가리키는 Lecture 클래스에서부터 다시 메서드 탐색이 시작되고 Lecture의 getEvaluationMethod 메서드를 실행한 후에 메서드 탐색을 종료한다.

**중요한 것**
getEvaluationMethod() 문장이 Lecture 클래스의 getEvaluationMethod 메서드를 실행시키라는 의미가 아니라 self가 참조하는 현재 객체에 getEvaluationMethod 메시지를 전송하라는 의미

**self 전송 (예제2)**
```java
public class GradeLecture extends Lecture{
 @Override
 public String getEvaluationMethod(){
	return "Grade";
 }
}
```


**Lecture 클래스의 stats 메서드를 실행하는 중에 self 참조가 가리키는 객체에게 getEvaluationMethod 메시지를 전송하는 구문과 마주치게 된다. 이제 메서드 탐색은 self 참조가 가리키는 객체에서 시작된다. 여기서 self 참조가 가리키는 객체는 바로 GradeLecture의 인스턴스다. 따라서 메시지 탐색은 Lecture 클래스를 벗어나 self 차조가 가리키는 GradeLecture에서부터 다시 시작된다.**
-> stats 메서드 내에서 self 참조가 GradeLecture 인스턴스를 가키리며 getEvaluationMethod 메시지 탐색이 GradeLecture부터 다시 시작됨

<img width="722" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/bb7a9fc7-cb62-42dd-8040-d8479a07584f">


### **이해할 수 없는 메시지**
상속 계층의 정상에 오고 나서 자신이 메시지를 처리할 수 없다는 사실을 알게된다면?

- 정적 타입 언어
  - 컴파일 에러를 발생시킨다
    - 유연성이 부족하지만 안정적이다
      - ex) 자바
- 동적 타입 언어
  - 예외를 던진다
  - 예외 메시지에 응답할 수 있는 메서드를 구현
    - 좀 더 객체지향에 가깝지만 이러한 특성은 코드를 이해하고 수정하기 어렵게 만들뿐만 아니라 디버깅 과정을 복잡하게 만든다
      - ex) 루비

<img width="743" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/d66c5bce-7942-45e6-9224-2390e0baa729">


### self vs super

|  | 시작 위치 |
| --- | --- |
| self | 동적 |
| super | 컴파일 시점(부모 클래스부터 탐색) |

```java
    public GradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores) {
        super(name, pass, scores);
        this.grades = grades;
    }

    @Override
    public String evaluate() {
        return super.evaluate() + ", " + gradesStatistics();
    }
```

```java
public class FormattedGradeLecture extends GradeLecture {
    public FormattedGradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores) {
        super(name, pass, grades, scores);
    }

    public String formatAverage() {
        return String.format("Avg: %1.1f", super.average());
    }
}
```

<img width="697" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/9bb03b3a-2369-4311-bb2a-9a5f5810a77d">

사실 super 참조의 용도는 부모 클래스에 정의된 메서드를 실행하기 위한 것이 아니라, ‘지금 이 클래스의 부모 클래스에서부터 메서드 탐색을 시작하세요.’ 다.

**부모 클래스의 메서드를 호출하는 것과 부모 클래스에서 메서드 탐색을 시작하는 것은 의미가 매우 다르다.**
-> 부모 클래스의 메서드를 호출한다는 것은 그 메서드가 반드시 부모 클래스 안에 정의돼 있어야 한다는 것을 의미한다.

## 상속 대 위임

### **위임과 self 참조**
<img width="666" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/01a0c35a-2d00-4e2d-bcfb-6681a28bfcfa">

위 그림에서 GradeLecture 인스턴스 입장에서 self 참조는? GradeLecture 인스턴스
GradeLecture 인스턴스에 포함된 Lecture 인스턴스의 입장에서 self 참조는? GradeLecture 인스턴스
→ 상속 계층을 구성하는 객체들 사이에 self 참조를 공유한다

<img width="702" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/14bf2bd3-ee5c-4e3a-9aeb-7416f4b5abd7">

**루비코드와 눈여겨볼점 네 가지**
```ruby
class Lecture
  def initialize(name, scores)
    @name = name
    @scores = scores
  end

  def stats(this)
    "Name: #{@name}, Evaluation Method: #{this.evaluationMethod(this)}"
  end

  def getEvaluationMethod()
    "Pass or Fail"
  end
end

lecture = Lecture.new("OOP", [1,2,3])
puts lecture.stats(lecture) #Lecture의 stats 메서드가 실행

```
Lecture를 상속받는 GradeLecture
```ruby
class GradeLecture
  def initialize(name, canceled, scores)
    @parent = Lecture.new(name, scores) # 1.@parent에 부모 클래스인 Lecture의 인스턴스 할당
    @canceled = canceled
  end

  def stats(this)
    @parent.stats(this) # 2, 4 @parent에게 요청을 그대로 전달
  end

  def getEvaluationMethod() 
    "Grade" #3. 요청을 @parent에 전달하지 않고 자신만의 방법으로 메서드를 구현 
  end
end

grade_lecture = GradeLecture.new("OOP", false, [1,2,3])
puts grade_lecture.stats(grade_lecture)
```
1. GradeLecture는 인스턴스 변수인 @parent에 Lecture의 인스턴스를 생성해서 저장.
   1. GradeLecture의 인스턴스에서 Lecture의 인스턴스로 이동할 수 있는 명시적인 링크가 추가된 것.
   2. 컴파일러가 제공해주던 동적 메서드 탐색 메커니즘을 구현한 것
2. GradeLecture의 stats 메서드는 추가적인 작업 없이 @parent에게 요청을 그대로 전달한다.
   1. 부모 클래스에서 메서드 탐색을 계속하는 동적 메서드 탐색 과정
3. GradeLecture의 getEvaluationMethod는 stats 메서드처럼 요청을 @parent에 전달하지 않고 자신만의 방법으로 메서드를 구현
   1. 부모 클래스의 메서드와 동일한 메서드를 구현하고 부모클래스와는 다른 방식으로 메서드를 구현하는 것은 상속에서의 메서드 오버라이딩과 동일
4. GradeLecture의 stats 메서드는 메시지를 직접 처리하지 않고 Lecture의 stats 메서드에게 요청을 전달
   1. Lecture의 메서드가 아닌 GradeLecture의 메서드가 실행되어 self 전송에 의한 동적 메서드 탐색 과정과 동일

**위임 vs 포워딩**
| 위임 | 메시지를 직접 처리하지 않고 요청을 전달하는 것을 위임이라고 한다 |
| --- | --- |
| 포워딩 | 객체가 다른 객체에게 요청을 처리할때 단순히 코드를 재사용하고 싶은 경우 self 참조를 전달하지 않는데 이를 포워딩이라고 한다 |

### 위임
~~~java
class Window {
    private Rectangle rectangle;

    public int area() {
        // area 계산을 rectangle에게 위임
        return rectangle.area(this); // this(=Window)를 함께 넘김
    }
}
~~~
### 포워딩
~~~java
class Printer {
    public void print(String text) {
        System.out.println(text);
    }
}

class PrinterProxy {
    private Printer printer = new Printer();

    public void print(String text) {
        // self 참조 없이 그냥 전달만 함
        printer.print(text);
    }
}
~~~

![](image.png)

**위임과 상속의 관계**
- 위임이 객체 사이의 동적인 연결 관계를 이용해 상속을 구현하는 방법
- 상속 관계로 연결된 클래스 사이에는 자동적인 메시지 위임이 일어난다

### 프로토타입 기반의 객체지향 언어: 객체 간 상속을 통해

- 자바스크립트의 모든 객체들은 다른 객체를 가리키는 용도로 사용되는 prototype이라는 이름의 링크를 가진다.
- 인스턴스 메시지를 수신하면 먼저 메시지를 수신한 객체의 prototype안에서 메시지에 응답할 적절한 메서드가 존재하는지 검사한다.
- 만약 메시지가 존재하지 않는다면 prototype이 가리키는 객체를 따라 메시지 처리를 자동적으로 위임한다.
```java
function Lecture(name, scores) {
    this.name = name;
    this.scores = scores;
}

Lecture.prototype.stats = function() {
    return "Name: "+ this.name + ", Evaluation Method: "+ this.getEvaluationMethod();
}

Lecture.prototype.getEvaluationMethod = function() { 
    return "Pass or Fail"
}
// 메서드를 Lecture의 prototype이 참조하는 객체에 정의했다는 점에 주목. Lecture를 이용해서
// 생성된 모든 객체들은 prototype 객체에 정의된 메서드를 상속받는다.
// prototype에 할당되는 객체는 특별한 작업을 하지 않는 한 자바스크립트의 최상위 객체 타입인 objects
 

var lecture = new Lecture("OOP", [1, 2, 3]);

```
GradeLecture가 Lecture를 상속받게 하고 메서드를 오버라이딩
```java

function GradeLecture(name, canceled, scores) {
    Lecture.call(this, name, scores);
    this.canceled = canceled;
}

GradeLecture.prototype = new Lecture();

GradeLecture.prototype.constructor = GradeLecture;

GradeLecture.prototype.getEvaluationMethod = function() {
    return "Grade" 
}
// GradeLecture의 prototype에 객체를 할당하여 GradeLecture를 이용해 생성된 모든 객체들이
// prototype을 통해 Lecture에 정의된 모든 속성과 함수에 접근할 수 있게 된다.
// 결과적으로 GradeLecture의 모든 인스턴스들은 Lecture의 특성을 자동으로 상속받게 된다.
```
```java
var grade_lecture = new GradeLecture("OOP", false, [1, 2, 3]);
console.log(grade_lecture.stats());

```
<img width="726" alt="image" src="https://github.com/HJC96/Obejct/assets/87226129/aec32dba-565c-4190-989f-1b0dd03d6427">
 
