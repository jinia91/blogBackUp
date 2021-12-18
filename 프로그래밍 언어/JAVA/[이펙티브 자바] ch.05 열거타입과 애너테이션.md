## Item 34. int  상수대신 열거타입사용!

- Int 상수 열거패턴은 매우 부실한 방식이므로 사용하지 말자
	- [열거타입을 DB에 Ordinal 또는 String 타입으로 저장하는 방법](https://github.com/jinia91/blog/blob/6f75c1d9c9085427b3bca131856f74e66c577b97/src/main/java/myblog/blog/member/doamin/Member.java#L37)
	- 단점
		- 타입 안전을 보장할 방법이 없음
		- 표현력도 안좋음
		- 중간에 다른 값을 집어넣으면 순서가 뒤틀려서 문제가 발생

- 열거타입(Enumeration)을 사용하면 해당 문제 손쉽게 해결 가능
	- [열거타입에 특정 데이터를 연결하고 생성자에서 데이터를 받아 인스턴스 필드에 저장하며 getter를 통해 그 특정 데이터를 얻는 방식의 예시](https://github.com/jinia91/blog/blob/main/src/main/java/myblog/blog/member/doamin/Role.java)

### 실무에서 Enum은 언제 사용해야할까?

- 타입으로 묶을수 있는 2개이상의 값이 있다면 왠만하면 Enum을 사용하자

- 특정 서비스에 종속되어있고 아직 불필요하다고 판단하면 안만들수도 있지만 그래도 만드는걸 권장

### 추상 메소드를 활용하여 상수별 메서드 구현을 분기하는 패턴
	public enum Operation {
	  PLUS("+")		{public double apply(double x, double y){return x + y;}}
	  MINUS("-")	{public double apply(double x, double y){return x - y;}}
	  TIMES("*")	{public double apply(double x, double y){return x * y;}}
	  DIVIDE("/")	{public double apply(double x, double y){return x / y;}}

	  private fianl String symbol;
	  public abstract double apply(double x, double y);
	  Operation(String symbol){this.symbol = symbol;}
	}

- enum 의 값으로 메소드를 넘겨주는것도 가능하다!


### 열거타입을 재귀적으로 사용하는 전략 열거 타입 패턴
	enum PayrollDay{
	 MONDAY(WEEKDAY)
	 TUESDAY(WEEKDAY)
	 WEDNESDAY(WEEKDAY)
	 THURSDAY(WEEKDAY)
	 FRIDAY(WEEKDAY)
	 SATURDAY(WEEKEND)
	 SUNDAY(WEEKEND)
	
	...
	
		int pay(int minutesWorked, int payRate){
			return payType.pay(minutesWorked, payRate)
		}
	}
	
	// 전략 열거 타입
	enum PayType{
		WEEKDAY{overtimePay 메소드 ~~}
		WEEKEND{overtimePay 메소드 ~~}

		abstract int overtimePay(int min, int payRate);
		int pay(int minsWorked, int payRate){
		~~~...}
	}

- 추상 메소드를 활용하여 열거타입으로 메서드 구현을 분기할때 중복되는 코드를 재차 열거타입으로 묶어 코드의 중복을 없애는 패턴
	
	

### ~~ToEnum 메소드 구현
- 파라미터가 enum으로 넘어오지 않을때, Enum으로 매핑시켜 구현하는 방법
	 - [실제 사용 코드 예제](https://github.com/jinia91/blog/blob/c62a003d261fe881a39397aa98f0b139d50c1168/src/main/java/myblog/blog/member/auth/UserInfoFactory.java#L29)


## Item 35. ordinal 메서드 대신 인스턴스 필드를 사용!

- 위의 아이템 34에서 말한거처럼 int 방식으로 매칭하는 열거는 안좋은 표현법
- 순서를 부여하는경우도 유지보수하기 매우 좋지 않음!

**ordinal 메소드는 그냥 쓰지말자**

## Item 36. 비트 필드 대신 EnumSet 사용하자

- 특정한 상태값 저장시 비트필드를 사용하는 코드 스타일이 존재함
	- "폰트가 굵으면서 이탤릭체이고, 언더라인이나 취소선이 없이 쓰자"
	-  (1, 1, 0 ,0) 이와같이 상태값을 가지면 해당 상태를 인지하기 편리해짐

- 하지만 EnumSet을 사용하면 성능의 손실은 거의 없으면서 보다 가독성 좋은 상태 저장이 가능해진다!

	  public class Text{
			public enum Style{BOLD, ITALIC, UNDERLINE, STRKEDTHROUGH}

			public void applyStyles(Set<Style> styles){...}
		} 
		//클라이언트에서는
		text.applyStyles(EnumSet.of(Style.BOLD,Style.ITALIC));
		// 이것이 (1,1,0,0);  이와같이 인자를 넘기는것보다 훨씬 가독성이 좋음

## Item 37. EnumMap vs HashMap

- EnumMap은 맵의 키값으로 Enum을 받는 Map을 뜻함
- 이경우 키값이 Enum임을 인지하므로 단일객체임을 보장하고 해싱작업없이 내부에서 Ordinal로 작동하게 되므로 일반 HashMap에 비해 성능이 훨씬 좋고 정렬도 가능해짐!

[HashMap을 EnumMap으로 리팩토링](https://github.com/jinia91/blog/blob/c62a003d261fe881a39397aa98f0b139d50c1168/src/main/java/myblog/blog/member/auth/UserInfoFactory.java#L22)

[Ref; HashMap vs EnumMap 성능 비교](https://siyoon210.tistory.com/142)



## Item 38. 확장할 수 있는(상속) 열거타입이 필요하면 인터페이스를 사용하라

- 열거타입 분기 메소드를 작성하는 로직에서 열거타입이 확장될경우(상속)를 생각해 인터페이스를 부착하는 설계를 고려해볼 수 있다.

- 실무에서 enum을 상속할일은 아마 없을듯?


## Item 39. 명명패턴보다 애너테이션을 사용해라

- 명명 패턴의 문제점
	- 오타에 취약
	- 올바른 프로그램요소에서만 사용되리라는 법이 없음(원치 않는 scan으로 인한 에러)
	- 프로그램 요소를 매개변수로 전달할 방법이 없음

- 명명 패턴을 구현하거나 애너테이션을 구현할 일은 최소  라이브러리를 만드는레벨이 아닌이상 쓰진 않을듯
- 나중에 다시한번 공부해보자

## Item 40. @Override어노테이션을 무조건 사용!

- 실수를 잡아주므로 당연
- 오버라이딩을 하는 메소드라면 무조건 달아주도록하자

## Item 41. 마커 인터페이스 / 마커 애너테이션

- 마커인터페이스 ? 마커 애너테이션?
	요소가 아무것도 정의되지않은 인터페이스, 애너테이션을 뜻함

	- 스프링 시큐리티에서 @AuthenticationPrincipal 인터페이스의 유저 엔티티를 뽑아올때 사용하는 에너테이션 사용자 정의 코드 예시

			@Retention(RetentionPolicy.RUNTIME)  
			@Target(ElementType.PARAMETER)  	
			@AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")  
			public @interface CurrentAccount {  
			}

- 만약 타입정의만을 목적으로 할경우 인터페이스가 애너테이션보다 우월함
	- 클래스의 인스턴스들을 구분하는 타입으로 사용가능
	- 적용 대상을 더 정밀히 지정 가능
	
- 반면 위의 마커애너테이션처럼 프레임워크 일부로 편입하거나 프로그램요소에 마킹하는경우는 애너테이션이 더 나은 선택