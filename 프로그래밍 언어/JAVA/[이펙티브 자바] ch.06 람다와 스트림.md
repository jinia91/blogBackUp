## Item 42. 익명클래스보다는 람다

- 과거에는 익명클래스를 사용
		
	  Collections.sort(words, new Comparator<String>(){
		  public int compare(String s1, String s2){
			return Integer.compare(s1.length(), s2.length());	
	  }});
- 이제는 람다로 함수형 인터페이스를 사용해 대체!

	  Collections.sort(words, (o1, o2) -> Integer.compare(o1.length(), o2.length()));

- 비교자 생성 메서드도 사용 가능!
	  Collections.sort(words, comparingInt(String::length));

- **코드가 4줄이상되는 람다는 사용하지 말자! 가독성이 너무 떨어짐**


## Item 43. 람다보다는 메서드 참조

- 람다식이 단순히 특정 메서드를 호출하는 경우라면 메서드 참조를 적극고려해볼수 있음

- 메소드 참조 유형

| 참조 유형 | 예 |람다의 경우|
|--|--|--|
| 정적 | Integer::parseInt |str -> Integer.parseInt(str)|
| 한정적(인스턴스) | Instant.now()::isAfter |Instant then = Instant.now(); t->then.isAfter(A);|
| 비한정적(인스턴스) | String::toLowerCase |str -> str.toLowerCase()|
| 클래스 생성자 | TreeMap<>::new |() -> new TreeMap<>()|
| 배열 생성자 | int[]::new |len -> new int[len]|

어차피 인텔리제이가 알려주므로 람다로 작성하고 메소드참조로 리팩토링하도록 하자


## Item 44. 표준 함수형 인터페이스를 사용
- 익명함수를 만들때 표준 함수형 인터페이스를 사용하면 코드작성이 훨씬 쉬워짐!

### 표준 함수형 인터페이스 대표 계열 5가지

1. Consumer 계열
	- 파라미터를 입력받아 그것을 소비하는 함수형 인터페이스
	- Consumer / void accept(T t);
	- BiConsumer / void accept(T t, U u);
	- intConsumer / void accept(int value);
	- Long~
	- Double~
	- ObjIntConsumer / void accept(T t, int value);
	- ObjLong~
	- ObjDouble~
	
2. Supplier 계열
	- 파라미터 없이 값만 반환해주는 함수형 인터페이스
	- Supplier / T get();
	- BooleanSupplier / boolean getAsBoolean();
	- IntSupplier / int getAsInt()
	- Long~
	- Double~
	
3. Function 계열
	- Mapping : 입력 -> 출력
	- Function<T,R> / R apply(T t);
	- BiConsumer<T,U,R> / R apply(T t, U u);
	- PFunction / R apply(P p);
	- PtoQFunction / Q apply(P p);
	- toPFunction / p apply(T t);
	- toPBiFunction / p apply(T t, U u);
	- P,Q는 원시타입

4. Operator 계열
	- 입력받은 타입과 동일한 타입을 출력하는 함수형 인터페이스
	- UnaryOperator / T apply(T t);
	- BinaryOperator / T apply(T t1, T t2);
	
5. Predicate 계열
	- 논리판단을 해주는 함수형 인터페이스
	- Predicate / boolean test(T t);
	- BiPredicate / boolean test(T t, U u);

### 표준 함수형 인터페이스의 주요 메소드
- andThen()
	- 두개이상의 함수형 인터페이스를 연결할 때 사용
	- Consumer종류 함수의 순차적 연결시 사용
- compose()
	- andThen의 반대순서 
- and(), or(), negate(), isEqual()
	- && , ||, !, == 

- 직접 만들일은 아마 없을듯?
- 43개의 표준 인터페이스가 존재하며 기본 유형만 암기하고있다면 나머지는 유추를통해 사용 가능!

## Item 45. 스트림은 주의해서 사용해라

- 스트림을 사용하지 말아야 하는 경우
	1. char 값 처리
	2. 기존 코드를 스트림으로 리팩토링했을때, 유지보수에서 더 가독성이 안좋은경우
	3. continue, break 문이 존재하는 반복문의 경우
	4. 스트림 처리중 외부 지역변수에 접근하는경우
		- 순수함수가 아닌경우

- 스트림을 사용하면 좋은 경우
	1. 원소들의 시퀸스를 일관되게 변환
	2. 원소들의 시퀀스를 필터링
	3. 원소들의 시퀸스를 하나의 연산을 사용해 결합
	4. 원소들의 시퀀스를 컬렉션에 모으기
	5. 원소들의 시퀸스에서 특정 조건을 만족하는 원소 찾기

**스트림을 만능으로 생각하지는 말자!**

[스트림 사용한 코드 예제](https://github.com/jinia91/blog/blob/c62a003d261fe881a39397aa98f0b139d50c1168/src/main/java/myblog/blog/article/controller/ArticleController.java#L54)


## Item 46. 스트림은 부작용 없는 함수를 사용하라

### 함수형 프로그래밍과 순수함수

- 스트림의 핵심은 계산을 **일련의 변환으로 재구성**하는 개념

- 스트림은 종단 반복연산을 위해 사용하는 개념이 아니다!

		// 단어를 수집해서 맵에 반복 횟수를 기록하는 로직
		try (Stream<String> words = new Scanner(file).tokens()){
			words.forEach(word ->{
				map.merge(word.toLowerCase(), 1L, Long::sum);
			})
		}

	- 위와같은 코드는 단순히 단어를 추출해 람다로 반복문을 수행하는것일 뿐, 순수함수라고 할 수 없음
	- merge를 통해 맵 객체에 반복적으로 put을 행동하고 있으므로, 스트림 외부의 변수에 영향을 주고 있기 때문

- 위의 로직을 Stream을 사용하는 순수함수로 바꾸려면

		// 단어들을 소문자로 만든뒤 모아서 카운팅을한다음 이를 map형태로 만들어서 반환
		try (Stream<String> words = new Scanner(file).tokens()){
			map = words
						.collect(groupingBy(String::toLwerCase, counting()));
			}


- forEach 연산은 스트림중 가장 패러다임에 불일치하는 메소드이므로 단순 결과보고 이외에 연산용으로는 사용하지 않는것을 권장

- 부작용없는 순수함수로의 Stream 구현을 위해서는 Stream Api를 제대로 정리할 필요성!!! 언제 한번 날 잡아서 제대로 다시 정리해보도록 하자

	- [toMap을 사용한 Stream 예시](https://github.com/jinia91/blog/blob/c62a003d261fe881a39397aa98f0b139d50c1168/src/main/java/myblog/blog/member/auth/UserInfoFactory.java#L34)

	- groupBy, joining, toList 등등도 정확히 알아둘 것

 ## Item 47. 반환타입으로는 스트림보다 컬렉션이 낫다!

- 클라이언트 측에서 자료구조를 받을때 Stream을 받게되면 Iterable 을 사용한 처리가 불가능한 단점이 존재
- 따라서 왠만하면 Iterable을 확장한 컬렉션으로 반환하는것이 보편적

- 전용 컬렉션을 구현해서 반환하라고 하는데 과연 이정도까지....?

## Item 48. 스트림 병렬화는 주의해서 적용

- 스트림 파이프라인 병렬화는 정확한 지식없이는 시도하지말것
- 잘못된 병렬화는 성능을 크게 떨어뜨리고 프로그램이 오작동나도록 한다.
	- task를 fork 하는 과정에서 비용발생
	- fork된 자식 쓰레드들을 join하는 과정에서 추가 비용발생

- 강도높은 테스트가 필요

- 병렬화 개념과 병렬 프로그래밍을 잘 모름... 나중에 다시 보도록하자