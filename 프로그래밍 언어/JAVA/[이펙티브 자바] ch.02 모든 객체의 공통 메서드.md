## Item 10 equals는 일반 규약을 지켜 재정의해라

### equals는 꼭 필요할때만 정의하자!

- 각 객체는 본질적으로 고유하다. 값을 표현하는(DTO,VO,ENTITY 등) 객체가 아니라면 재정의할 필요 없음

- 논리적 동치성을 검사할 필요가 있는지? 

### equals 재정의 일반 규약
**1. 반사성 : null이 아닌 모든 참조값 x에 대해 x.equals(x)는 true**
- 객체는 자기 자신과 비교했을때 같아야한다는 뜻
- 보통은 당연히 만족

**2. 대칭성 : null이 아닌 모든 참조값 x,y에 대해 x.equals(y)가 true면 y.equals(x)도 true**
- 서로 다른 클래스간 equals 정의시 한쪽만 정의할경우 발생하기 쉬움 양쪽 equals의 로직이 다르므로 어느쪽에서 equals를 거냐에따라 결과값이 달라짐!

**3. 추이성 : null이 아닌 모든 참조값 x,y,z에 대해 x.equals(y)가 true이고 y.equals(z)가 true이면 x.equals(z)도 true**
- 3단 논법 , 추론을 통해 같아야함!
- 클래스 상속관계에서 equals를 잘못 정의하면 발생하기 쉬움

            
        // Timestamp 클래스가 대표적으로 잘못설계된 클래스
        // TImestamp는 Date를 상속받아 nanosec필드를 추가하고 equals를 재정의해서 Date 클래스와 대칭성을 위반함
        
        Date time1 = new Date(2021-1900, 10, 19, 10, 13, 10);
        Timestamp time2 = new Timestamp(2021-1900, 10, 19, 10, 13, 10,11);
        Timestamp time3 = new Timestamp(2021-1900, 10, 19, 10, 13, 10,16);
        
        System.out.println(time1);
        System.out.println(time2);
        System.out.println(time3);
        
        //Fri Nov 19 10:13:10 KST 2021
        //2021-11-19 10:13:10.000000011
        //2021-11-19 10:13:10.000000016			
        
        
        System.out.println(time1.equals(time2));   // true
        System.out.println(time1.equals(time3));   // true
        
        // 부모클래스쪽에서 equals를 걸면 자식이라 instance연산이 통과되고 nanosec필드를 무시한채 일치			
        
        System.out.println(time2.equals(time1));  // false
        System.out.println(time2.equals(time3));  // false
        
        // 자식쪽에서 걸면 당연 불일치
        
        System.out.println(time3.equals(time1));  // false
        System.out.println(time3.equals(time2));  // false
        
        // time1 == time2, time1 == time3인데 time2 !=time3 이므로 추이성 위반


	
- 그렇다면 상속관계에서 equals 동치 관계를 제대로 성립시키려면 어떻게해야하지?
	- **객체지향적 추상화 관점에서 불가능**

	- 만약 상위 클래스에서 instanceOf 대신 getClass를통해 equals를 정의한다면 가능하지만 이 경우 equals 기반으로 작동하는 다른 기본 메소드들이 정상적으로 동작하지  않고 객체지향적 관점에서 상속관계에 의한 동작들이 제대로 수행 안되므로 사용 x

		  // 인텔리제이 자동 equals 정의에서는 getClass를 사용하지만 책에서는 적절하지 않다고 말함
		  // 추가로 4번의 null 체킹에 대해서도 묵시적 체크로 충분하다는데 인텔리제이에서는 null체킹을 함
			@Override  
			public boolean equals(Object o) {  
			    if (this == o) return true;  
			    if (o == null || getClass() != o.getClass()) return false;  
			    ArticleForm that = (ArticleForm) o;  
			    return Objects.equals(title, that.title) && Objects.equals(content, that.content) && Objects.equals(toc, that.toc) && Objects.equals(memberId, that.memberId) && Objects.equals(thumbnailUrl, that.thumbnailUrl) && Objects.equals(category, that.category) && Objects.equals(tags, that.tags);  
			}
		- [리스코프 치환원칙을 위배](https://github.com/jinia91/TIL/blob/master/Design/SOLID.md#l-%EB%A6%AC%EC%8A%A4%EC%BD%94%ED%94%84-%EC%B9%98%ED%99%98-%EC%9B%90%EC%B9%99lsp-liskov-substitution-principle)
	- 상속 대신 **컴포지션 패턴**을 사용해서 대칭성, 추이성, 리스코프 치환원칙에 위배되지 않는 코드작성 가능!

**4. 일관성 : null이 아닌 모든 참조값 x,y에 대해, x.equals를 반복해서 호출해도 항상 결과가 일정해야한다**	

 - java.net.URL이 대표적으로 잘못설계된 클래스

		URL url1 = new URL("[www.site-name.co.kr](http://www.site-name.co.kr/)");  
		URL url2 = new URL("[www.site-name.co.kr](http://www.site-name.co.kr/)");  
	  
		System.out.println(url1.equals(url2)); //?
	 
- equals가 매핑된 ip주소를 이용해 비교하는데 ip주소가 항상 같으리란 보장이 없으므로 일관성이 깨진다.

**5. null-아님 : 클래스가 null이여서는 안된다**

- 바람직한 코드
				
	  if(!(o instanceof MyClass)) return false

### equals 구현시 컨벤션 로직
1. ==연산자를 사용해 자기 자신 참조면 true 반환
	- equals 로직이 복잡할시 성능을 위해 

2. instanceof 연산자로 입력이 올바른 타입인지 확인하고 false
	- 묵시적 null checking
	- 필요시에는 상위 인터페이스로도 가능
3. 입력을 올바른 타입으로 형변환
	- 2번을 통과했다면 당연 통과
4. 입력 객체와 자기 자신의 대응되는 '핵심'필드들이 모두 일치하는지 하나씩 검사

> float, double 을 비교연산할때는 부동소수값을 생각해서 compare로 비교할것

5. equals를 재정의할때는 hashCode도 재정의할것

6. 오버로딩하지 않도록 인자를 반드시 Object o 로 받자

7. 이후 단위테스트할것
	- AutoValue 프레임워크를 사용하면 간편, 롬복을 사용해도된다


## Item 11 equals를 재정의할때는 hashCode도 재정의하자

- equals 재정의를 했다면 동등성에대해 정의한것이므로 동등성에 맞는 동작을 기대할 것이다.

|비교방법| 설명  |
|--|--|
|  == | value compare, 변수의 값, 주소값 비교, 동일성 |
| equals() | 같은 객체인지 , default는 == 과 동일, 동등성 |
| hashcode() | 논리적으로 같은 객체라면 같은 hashcode() |


- 그러나 hashMap, hashSet같은 메소드들은 해시코드 일반규약으로 작동하므로 equals true인 객체들간에 기대한 동작을 하지 않는다.
- 따라서 equals 재정의시에는 hashCode도 재정의하자!

### hashcode 작성 컨벤션

1. 인텔리제이 기본 예시

		@Override  
		public int hashCode() {  
		    return Objects.hash(title, content, toc, memberId, thumbnailUrl, category, tags);  
		}
- 핵심 필드들을 hash() 함수로 돌려 리턴
- 인수들에 대한 분리가 안되있어서 오토박싱과 배열생성 때문에 성능이 살짝 아쉬움


2. 롬복 예시

		@Override public int hashCode() {
		    final int PRIME = 59;
		    int result = 1;
		    final long temp1 = Double.doubleToLongBits(this.score);
		    result = (result*PRIME) + (this.name == null ? 43 : this.name.hashCode());
		    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
		    result = (result*PRIME) + Arrays.deepHashCode(this.tags);
		    return result;
		  }
	- 타입별로 각각 구분하여 성능최적화를 해서 코드를 작성해준다.
	- 해시코드를 구현할거면 롬복을 쓰자

## Item 12 toString을 항상 재정의해라

- toString 규약상 모든 클래스는 toString을 재정의하게 되있음
- 단순 정보전달뿐만 아니라 로그 메시지에서도 toString은 유용하므로 재정의하는걸 추천

- 실무에서 toString은 그 객체가 가진 주요 정보 모두를 반환하는게 좋다.

- toString의 규약의 필요성에대해서는 협업관점에서 충분한 토의후 결정하자

### 결론
1. 로그찍을일이 있으면 무조건 toString구현
2. 굳이 전부할필요는 실무에선 없음



## Item 13 clone 재정의는 주의해서 하자

- [clone()은 기본적으로 깊은 복사를 위해 사용](https://github.com/jinia91/TIL/blob/master/Java/3.%20%EC%96%95%EC%9D%80%20%EB%B3%B5%EC%82%AC?%20%EA%B9%8A%EC%9D%80%EB%B3%B5%EC%82%AC?.md)

- **유지보수와 클래스 확장에서 고려사항이 너무 많고, 생성자를 사용하지 않는 객체생성방식의 리스크를 생각할 때 clone / clonable은 실무에서 사용하지 말자!**

	- 원시타입 배열 복사라면 쓸수도... 하지만 가변객체를 담으면 재귀적 복사가 필요해 너무 복잡해짐...

- **객체를 복사할 일이 있다면 복사 생성자와 복사 팩터리 패턴으로 대체할것!**
	public ClassName(a, b, c){...}
	- 파라미터 주입으로 동일한 객체를 생성하면 됨
	- 혹은 이를 static 팩토리 메소드로 만들자


## Item 14 compareTo 규약

- 순서가 명확한 값타입을 작성한다면 Comparable 인터페이스를 구현하는것을 추천

> compareTo 일반 규약
> 1. x.compareTo(y) == - y.compareTo(x); 대칭성
> 2. 추이성 보장
> 3. 일관성 보장
> 4. compareTo 와 equals 간의 일관성 보장

### 비교자 생성 메소드
- 일반적으로 comparable의 compareTo를 구현할때는 if와 연산을 사용하거나 double float같은 부동소수점 비교의 경우 compare 메소드를 사용한다

- 자바 8의 경우 비교자 생성 메소드를 사용하여 메소드 체인으로 compareTo를 구현할수 있음

- 하지만 일반적으로 10%의 성능저하 발생

	Comparator.comparingInt(o -> o.hashCode);



		// 실제 구현 예시 - 금은동 갯수로 순위 구하기
		// 전통적 방식으로 compareTo 구현
		@Override
		public int compareTo(rank o) {
			if (this.gold == o.gold) {
				if (this.gold+this.silver == o.gold+o.silver) {
					return o.bronze - this.bronze;
				}
				return o.silver -  this.silver;
			}
			return o.gold-this.gold;
		}
		
		// compare 메소드 사용
			int result = Integer.compare(o.gold, this.gold); // 오토박싱 안일어남
			if (result == 0) {
				result = Integer.compare(o.silver, this.silver);
			}
			if(result == 0){
				result = Integer.compare(o.bronze, this.bronze)
			}
				return result;
		  }
		

		// 람다 + 비교자 생성 메소드 방식
	    Collections.sort(pq, Comparator
	    		.comparingInt((rank o1) -> o1.gold)
	    		.thenComparingInt(o1 -> o1.silver)
	    		.thenComparingInt(o1 -> o1.bronze).reversed());	
