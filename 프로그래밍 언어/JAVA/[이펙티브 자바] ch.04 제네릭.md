## Item 26. raw타입 사용 금지

| 한글 용어 | 영문용어 |예|
|--|--|--|
| 매개변수화 타입 | parameterized type |list\<String>|
| 실제 매개 변수 | actual type parameter |String|
| 제네릭 타입 | generic type |list\<E>|
| 정규 타입 매개변수 | formal type parameter |E|
| 비한정적 와일드카드 타입 | unbounded wildcard type |List<?>|
| 로타입 | rawtype |List|
| 한정적 매개변수 타입 | bounded type parameter |\<E extends Number>|
| 재귀적 타입 한정 | recursive type bound |<T extends Comparable<T>>|
| 한정적 와일드 카드 타입 | bounded wildcard type |List<? extends Number>|
| 제네릭 메소드 | generic method |static \<E> List\<E> asList(E[] a)|
| 타입 토큰 | type token |String.class|



### 지네릭 타입의 상위타입은 매개변수 상위 타입을 따라가는게 아니다?!

- 지네릭타입 List\<Object> 는 List\<Integer> 의 상위 타입이 아니다.
	- 간단한 예시를 들어 보면
					
		  
		  Object a = Integer.valueOf(3);  // 가능
		  
		  List<Object> list = new ArrayList<Intger>(); // // 컴파일 에러
		  // 클래스 관계에서 상위타입은 더 적은 일을 하므로 상관없지만 컬렉션 관계에서 상위 파라미터는
		  // 더 많은걸 담을수 있는 인터페이스이므로 실제 구현 타입이 세부적일경우 add에서 런타임 문제가 생길수 있음
		  // 따라서 원천적으로 컴파일단계에서 막아놓음


### 그렇다면 지네릭의 상위타입 하위타입은 어떻게 나타내야하지?
	
- 와일드 카드로 표현하기

	  List<?> list = new ArrayList<Integer>();
	  List<? extends Object> list = new ArrayList<Integer>();	  

- 와일드 카드로 표현하면 해당 구현 컬렉션을 이용하는데에는 지장이 없지만,  다형성을 생각해서 더 많은 타입을 add할수 있는 거 아닌가 생각할 수 있음
- 따라서 와일드카드 컬렉션은 기본적으로 add를 막아둔다!

- 즉 와일드카드는 컬렉션을 상위타입 인터페이스로 추상화하는 개념으로쓰이지만 add는 막아두어 컬렉션 읽기만 되게 하는 방식 

- 요약 : 와일드카드는 제네릭타입의 상위 타입 표현, add는 불가능


### item으로 돌아가서 로 타입을 쓰면 안되는 이유

- 지네릭이 나오기 이전 문법을 호환하기위한 문법일뿐 로 타입은 명확한 명시가 없으므로 써서는 안된다

- 만약 여러 타입을 담고싶다면 \<Object> 로 명시해서 쓰도록 하자


## Item 27. 컴파일러 경고는 모두 해결하자

- Idle에서 뜨는 노란색 경고는 모두 해결하는것이 좋은 코드!

- 만약 경고를 제거하기 힘들고, 해당 에러가 절대 발생하지 않으리라 확신한다면 @Suppress Warning 애너테이션을 달자
	- 가능하면 좁게
	- 달때는 주석도 함께

- warinig을 무시하는 코드는 결국 기술부채를 만든다...

## Item 28. 배열대신 리스트를 사용하자

- 배열과 리스트의 가장 큰 차이점은 상위타입 인식 문제!

Item 26에서 살펴본것처럼

	// 컴파일 가능, 배열에 요소를 넣는 순간 런타임 에러가 발생'ArraysStoreException'
	 Object[] a = new Integer[3];
		  
	// 컴파일 에러	  
	 List<Object> list = new ArrayList<Intger>(); 

		 
- 컬렉션은 패러미터의 상위타입을 상위타입으로 인식하지 않지만, 배열은 상위타입으로 컴파일시 인식함

- 이경우 역시 add단계에서 에러가 터지므로 결국 런타임 에러가 발생하게 됨

- 성능문제때문에 배열을 쓰는것도 어불성설 그냥 리스트를 쓰는게 대체로 훨씬더 안정적인 코드

## Item 29. 클래스 설계시 제네릭을 쓸수 있다면 쓰자

> 제네릭 타입 컨벤션
> E - Element
> K - Key
> N - Number
> T - Type
> V - Value
> S,U,V etc - 2,3,4 type

- 만약 특정 객체가 특정 패러미터만을 처리하는 객체라면 제네릭을 사용해서 설계해보도록하자
	-  실제로 작성해본 코드가 없어서 아직 와닿지는 않는듯..?
- 만약 형변환 코드가 있다면 리팩토링하도록 하자

## Item 30. 클래스 설계시 메서드도 제네릭 메서드를 권장

- 왠만하면 클라이언트측에서 형변환이 일어나지 않는 설계를 하는것이 바람직

- 그러기 위해서는 제네릭 메서드를 사용하여 타입을 명확히 표시해주는것이 좋음

- 근데 클라이언트가 컨트롤러이고 DTO VS ENTITY 반환의 경우에는?
	-	명확한 정답이 있는가?

## Item 31. 한정적 와일드 카드를 사용해 api유연성을 높이라
- 위에서 살펴본거처럼 매개변수화 타입은 불공변이며 매개변수의 상속 구조를 따라가지 않는다.
	- Object와 Integer는 상속관계지만, List\<Object>와 List\<Integer>는 상속관계 x

- 하지만 자료구조를 다루는데 있어서 때로는 추상화된 컬렉션을 다뤄야하는일이 생기고 이때 한정적 와일드 카드를 사용할 수 있다!
	-  한정적 와일드 카드 <? extend E>	

	- 생산자나 소비자 와일드 카드 사용법
		- **PECS : Producer - Extends, Consumer - Super**


## Item 32. 제네릭과 가변인수를 함께 쓸때는 신중해라
## Item 33. 타입 안전 이종 컨테이너를 고려하라

- 추가 공부 필요
