## Item 15 클래스와 멤버의 접근권한을 최소화하라 / Item 16 public 필드를 사용하지 말며, getter 사용	


### 정보 은닉의 장점
1. 여러 컴포넌트 병렬개발로 시스템 개발 속도 증가
2. 컴포넌트 단위이므로 시스템 관리 비용이 낮아짐
3. 성능최적화에 용이
4. 재사용성 증가

### 캡슐화를 하지 않을 경우

- 불변식을 보장하지 못하므로 작동의 일관성을 보장하지 못함

- Thread safe 하지 못한 문제


- 만약 정말 필요하다면 public static final 필드로 불변객체를 참조하도록하자
	- 멤버 변수가 배열의 경우라면 static final이라도 안전하지 못함
		-  이경우는 private으로 묶고 접근 메소드를 두되, 반환을 asList나 clone()으로 반환하는걸 고려해보자

### 클래스를 설계할때 일반적으로 private 멤버만 갖도록 하자

- private 클래스 혹은 default 클래스라면 getter없이 필드에 직접접근해서 내부처리를 해도 무방하긴 함

## Item 17 변경 가능성 최소화, setter 사용을 최소화

### 불변 클래스일수록 안전하다

- 확장성은 떨어지지만 설계의 안전성을 얻을 수 있음

- 불변 클래스 만들기
	1. 객체의 상태를 변경하는 메서드 제공 x(setter x)
	2. 클래스 확장 불가(final)
	3. 모든 필드도 final
	4. 모든 필드 private
	5. 자신 외에 내부의 가변 컴포넌트 접근 x
		- 멤버로 다른 객체를 참조하고있을때 반환을 객체로 하지 않고 변환해서 반환하는 것
			

- 하지만 모든 클래스를 불변클래스로 만들 수는 없으므로 변경부분을 최소화하는 설계방향이 바람직함

	- [세터를 열지 않는 설계](https://github.com/jinia91/blog/blob/9ab902808b650c6c084f0cf643f8d6e751cc0857/src/main/java/myblog/blog/article/domain/Article.java#L75)

- 실무에서 불변클래스는 일반적으로 사용하진 않는듯? 명확히 VO 클래스를 사용한다면 만들만 하다.

## Item 18 구체클래스 상속보다 컴포지션을 사용해라

- 상속이 안전한 경우
	- 상위 클래스와 하위 클래스 모두 같은 프로그래머가 통제하는 패키지 안에 존재
	- 확장할 목적으로 설계되어있으며, 문서화도 잘 되어 있는경우

- 상속이 안전하지 않은 경우
	- 다른 패키지의 구체 클래스를 상속하는 경우
		- 캡슐화를 깨뜨리기때문
		- 내부 구현의 불필요한 노출은 api가 내부 구현에 묶이게 되고 클라이언트의 접근 리스크가 생기므로 유지보수가 매우 힘들어진다!

### 구체 클래스를 상속하는 클래스를 설계하기보다는 다른 방식을 생각해보자

- 컴포지션이란?
![enter image description here](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https://blog.kakaocdn.net/dn/cW7HqG/btqyV5IV2PM/VW9kw0xyX36XdeEksfD6o1/img.png)

	 - 기존 클래스(leaf)가 새로운 클래스의 구성요소(compoiste)로 쓰이는 디자인 패턴
	 - 기존 클래스를 다시 확장하기 보다, 새로운 클래스를 만들고 private 필드로 기존 클래스들을 참조하게 만드는 방식
 
	  - 새로운 클래스(composite)는 기존 클래스(leaf)와 공통의 부모를 가지면서 leaf를 private 멤버로 add remove 처리할수 있고, get을 통해 참조가능하여 leaf의 캡슐화된 설계를 전혀 손대지 않고도 모든 메소드를 사용할수 있고, 거기에 더해 composite만의 operation도 추가로 가능하게 되는 설계방식

- 데코레이터 패턴?
 ![enter image description here](https://gmlwjd9405.github.io/images/design-pattern-decorator/decorator-pattern.png)

	- 래퍼 + 추가 메소드를 더하는 데코레이터 패턴과 동일하나 래핑 객체가 많다면 컴포지트, 단일이라면 데코레이터 패턴이라 이해하면 편함

	- 데코레이터 패턴 예시 코드
		- [페이징 핸들러 클래스](https://github.com/jinia91/SpringbootMvcBoard/blob/master/board/src/main/java/com/project/board/board/handler/PagingHandler.java) 
		- [페이징 핸들러를 래핑한 페이징 박스 핸들러 클래스](https://github.com/jinia91/SpringbootMvcBoard/blob/master/board/src/main/java/com/project/board/board/handler/PagingBoxHandler.java)


## Item 19 왠만하면 상속을 금지하기

 - Item 18 처럼 구체클래스 상속의 문제성을 고려할때, 실무에서 무분별한 상속 클래스 만들기는 자제해야한다.

- 따라서 유지보수차원에서 상속을 원천봉쇄시키는 방어적 코드 , 설계가 보다 바람직

### 상속 금지하는 방법
1. 모든 생성자를 private, default로 선언하고 스태틱 팩토리 메소드로 생성 대체하기
	- super() 를 못가져오므로 상속이 불가능해짐
2. 클래스에 final을 명시하여 상속 금지

### 상속이 가능하게 열어두려면 주석을 잘 써야됨
- 실무에서 이걸 수행가능할까...?
- @implSpec 어노테이션사용
- 그냥 상속을 쓰지말고 인터페이스만 사용하는걸 권장...

### 정리
> 상속의 본질은 확장이다(= 클래스의 확장extend)
> 
> 클래스를 무분별하게 확장하고 오버라이딩을 하면 예상치 못한 오작동의 리스크가 커짐
> 
> 그렇다고 모든 클래스에 final을 달거나 static factory method를 사용하여 상속을 막고, 일부는 주석을 통해 상속룰을 정해 상속을 열어둔다? 현실적으로 불가능함
> 
> 차라리 내부적으로 상속 금지 컨벤션을 정하고, interface로 구현을 분리하여 설계하고, 필요시엔 컴포지션 패턴이나 데코레이터 패턴으로 구현하도록 하자



## Item 20 추상클래스보다는 인터페이스!

- 추상 클래스와 인터페이스의 가장 큰 차이는 다중상속(구현, 확장) 여부 

- 인터페이스는 다중 상속을 통해 선택적 기능을 추가(부착) 하는 느낌으로 구현이 가능하므로 고계층 구조를 불필요하게 만들필요 없이 믹스인으로 구현할수 있는 편의성이 존재한다.

- Java8 부터는 인터페이스에 디폴트 메소드도 사용가능해졌으므로 추상클래스를 쓸 이유는 더욱 없음

### 디폴트 메소드 사용시 다이아몬드 현상 주의

- 다중상속시 디폴트 메소드가 중복된다면 어느쪽 메소드를 사용해야할지 판단할 수 없기 때문에 에러 발생

### Skeleton implementation (추상 골격 구현) - 추상 클래스 사용법

1. Interface 로 뼈대를 만들고
2. Abstract class 로 필요한 구현을 마친뒤
3. Subclass로 마무리하는 방식

- Abstract class에서 실제 사용될 핵심 골격을 추가하는식으로 만들어 코드의 중복을 줄이는 설계

- 템플릿 메소드 패턴과 동일![enter image description here](https://gmlwjd9405.github.io/images/design-pattern-template-method/template-method-solution3.png)



## Item 21 인터페이스 설계시엔 구현을 항상 고려하자

- 인터페이스를 수정할경우 모든 구현체들을 다 수정해야하므로 처음 인터페이스를 설계할때 잘 설계하는건 당연
- 물론 디폴트 메서드를 사용할 수 있긴 하지만, 디폴트 메서드를 삽입하는것은 구현체들의 합의 없이 메서드를 추가하는 셈이므로 장기적인 관점에서 좋은 개발방식이라고 볼 수 없음!

### 구현을 고려하는 설계 방법 = TDD!

- Test 코드를 반드시 짜도록하자

- 좋은 코드는 오해를 발생시키지 않는 코드다! 

## Item 22 인터페이스는 타입을 정하는 용도로만 설계

- 인터페이스와 구현체를 분리하는 가장 큰 이유는 추상화를 통해 유사한 구현체들을 상위 타입으로 묶어 리스코프 치환원칙, 인터페이스 분리원칙, 의존 역전 원칙을 지키고 보다 객체지향적인 설계를 하기 위함이다.

- 위의 의도가 아닌 이유로 인터페이스를 정의하는것은 잘못된 설계!

### 상수 인터페이스가 대표적인 anti pattern

- 차라리 utill class나 해당 클래스에 static final로 추가하는것이 바람직


## Item 23 태그달린 클래스 보단 계층구조를 사용해라

- 태그 달린 클래스?
	- 클래스가 여러가지 역할을 하고 특정 필드의 값으로 그 역할을 구분짓는 설계방식

- 안티패턴인 이유
	- 역할에 따라 안쓰이는 메소드, 필드가 생김
		-  객체의 책임에 비해 불필요한 코드를 갖고 있으므로 메모리 낭비
		- 동일 메소드 다른동작으로 쓸데없는 분기처리가 일어나 성능 손해를 보기도함

### 실제 내 프로젝트에서 사용된 태그달린 클래스

- User 엔티티를 Type으로 구분하는 방식

- 타입에 의해 객체의 역할이 정해지면 그 역할에 불필요한 필드들이 생김

		@Getter

		public class Member extends BasicEntity {

		..... 
			// enum을 통해 역할 구분
			@Enumerated(EnumType.STRING)
			private Role role;

			// 관리자만 글을 쓸 권한이 있으므로 일반 유저는 필요없는 필드가 생성
			@OneToMany(mappedBy = "member")
			private List<Article> articles = new ArrayList<>();
		.......
		
		}
		

- ADMIN만 실제로 articleList를 갖는데 일반 유저들한테도 불필요한 필드가 생기게됨
- 

### 계층구조로 사용하자

- User 라는 추상 클래스를 두고 서브클래스들을 정의해서 설계하는 계층구조로 리팩토링하도록하자

- **Enum을 쓰지말라는게 아니라 특정 필드로 인해 내부 메소드나 필드의 쓰임이 결정되는 상황을 말하는거임!**

## Item 24. 멤버 클래스는 되도록 static으로 만들기

### 클래스 안에 클래스 정의하기 nested class, inner class

- 자신을 감싼 외부 클래스에서만 사용될때 nested class를 만들것!

1. 일반적인 Inner class
	- 특징
		- 여러 클래스 중첩가능
		- Outer  클래스 멤버를 Inner 클래스에서 접근가능
		- Outer 클래스에서 Inner 클래스 접근은 불가능(객체 생성켜서 접근은 가능)
		- Inner 클래스는 static 선언 불가능
		- **Inner 클래스 객체 생성시 반드시 Outer 클래스 객체를 만들고 생성해야함**
		- inner 클래스의 객체는 묵시적으로 outer 클래스의 객체랑 **연결되어있으므로(의존하므로)** inner 클래스의 메소드들은 this를 통해서도 outer 클래스 멤버변수로 접근 가능


	- 어댑터 패턴에서 주로 사용
![enter image description here](https://t1.daumcdn.net/cfile/tistory/9989BE395AFA70961C)

- 서로 다른 인터페이스(혹은 클래스)를 모두 호환하기 위한 패턴
- inner class는 여기서 어떻게 쓰지...? 좀더 공부해보자 

2. Static inner class, Static member class

	- 내부에서 static 선언이 가능한 클래스
	- 특징
		- **static이므로 외부에서 해당 클래스로 접근해 생성하는것이 가능**
		- static 선언이 자유로움
	
	- 일반 inner class와 결정적 차이
		- outer class를 참조할일이 있다면 일반 inner class 그게 아니라면 static으로 쓰도록하자
		- 일반적으로 static을 쓰는걸 권장


3. 익명 클래스 Anonymous class
	- 인터페이스의 구현체를 다른 클래스 안에서 구현하는 방식

	- 사실상 람다와 동일

4. local class
	- 특정 메소드에 한정적으로 사용하는 클래스
	
	- 특징
		- 접근 제한자, 예약어 사용 불가
		- 객체 생성 외부에서 불가능


## Item 25. Top level Class 는 파일당 하나만 쓰자

- 코딩 문제풀때만 씀...
- 컴파일러 일관성을 생각해서라도 당연히 지켜야할 룰