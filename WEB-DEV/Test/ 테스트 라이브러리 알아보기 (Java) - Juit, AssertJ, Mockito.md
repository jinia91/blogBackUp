# Goal
- 현재 재직중인 회사에선, 구현 코드 작성시 최소한의 테스트코드가 없으면 풀리퀘가 거부되는 방식으로 개발이 진행되고 있다.
- 또한 코틀린으로 테스트 코드를 작성하기 떄문에, 코틀린에 맞는 라이브러리들을 추가로 학습해야 한다.
- 따라서 자바 / 코틀린의 다양한 테스트 라이브러리들을 정리하고, 사용법을 익혀보고자 포스팅한다
# 자바 테스트 라이브러리
## Junit

![image](https://nesoy.github.io/assets/logo/JUnit.png)

>java의 단위테스트를 수행해주는 대표적인 Testing Framework

## Junit4 와 Junit5

Junit의 현재 최신버전은 5이며, 스프링부트 start.io등을 통해 기본적인 라이브러리들을 당겨오면 Junit5가 디폴트로 적용된다.

java언어에 큰 변화가 있었던 java8과, 이에 맞춰 2017년 출시한 Junit5는 junit4와 많은 차이를 갖는데 

그중 가장 큰 차이는 역시 jupiter라는 새로운 테스팅 엔진을 사용한다는 점이다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FqVRFa%2FbtqQIbDz6wo%2FG5KZcaH0VZ6ZeQUxSpScLk%2Fimg.png)

기존 junit4에 작성되었던 코드들은 호환성을 위해 vintage엔진으로 작동되고, 새롭게 추가된 jupiter엔진을 통해 junit5의 새로운 테스팅 api를 사용할 수 있다.

## AssertJ의 `AssertThat`

하지만 junit이 기본으로 제공하는 `검증 api ` 는 가독성이 좋지 않다는 치명적인 단점이 존재한다.
   
        assertEquals(A,B);
        
다음과 같은 검증 구문에서 파라미터 A와 B중 어떤것이 expect고 어떤것이 actualResult인지 확인이 가능한가?

클린코드에서는 실제 코드보다도 테스트코드의 가독성이 더 중요하다고 주장하기도 했는데, 위의 코드는 그런 관점에서 많은 아쉬움이 남는다.

또 다른 예시를 하나 더 보면 

       assertFalse(A.equals(B));
       
A is not equal B 검증을 위해서는 위와 같이 A객체의 equal메서드를 호출하는 등, 직관적이지 못한 구문들이 존재한다.


이를 보완하기 위해 등장한 라이브러리가 **AssertJ**다.

AssertJ란 단언문(assertion)을 작성하기 위한 풍부한 인터페이스를 제공하는 자바 라이브러리로,

테스트 코드의 가독성을 향상시키고 테스트 유지 관리를 더 쉽게 만드는 것을 주요 목적으로 한다.

또한 Junit5와 마찬가지로 스프링부트 기본 테스팅 라이브러리로 가져오기때문에, 스프링부트 프로젝트를 빌드한다면 별도의 의존성 추가를 해줄 필요가 없다.

AssertJ의 `assertThat` 구문을 살펴보면

    assertThat(A).isEqualsTo(B);
    assertThat(A).isNotEqualsTo(B);
    
위와같이 Expect를 AssertThat으로 담고, 이후 검증할 로직을 메서드 체이닝 방식으로 작성하여 가독성이 훨씬 좋다.

## AssertJ의 필수 api 학습하기

다음은 간단한 api 예제들이며, AssertJ엔 수많은 api가 존재하므로 자세한 내용은 공식 doc를 보고 학습하자.

[AssertJ 공식 Doc 참조](http://joel-costigliola.github.io/assertj/core-8/api/index.html)

### Object 검증

    assertThat(Expect).isEqualTo(Actual);

`isEqualTo()`의 동작방식은 이름 그대로 `equals()`로 검사를 하기 때문에 클래스에 `equals`가 잘 정의되어 있다면 문제 없이 작동한다.

만약 equals가 잘 정의되지 않은 클래스를 검증할 경우엔

    assertThat(Expect).isEqualToComparingFieldByFieldRecursively(Actual);

위와 같은 방식도 존재한다.
    
### boolean 검증

    assertThat("".isEmpty()).isTrue();
    assertThat("not Empty".isEmpty()).isFalse();

###  Iterable/Array 검증

    //given
    List<String> list = Arrays.asList("1", "2", "3");
    //then
    assertThat(list).contains("1");
    assertThat(list).isNotEmpty();
    assertThat(list).startsWith("1");
    //또는 체이닝을 사용하여
    assertThat(list)
      .isNotEmpty()
      .contains("1")
      .doesNotContainNull()
      .containsSequence("2", "3");
    
### 문자 검증

    assertThat(someCharacter)
          .isNotEqualTo('a')
          .inUnicode()
          .isGreaterThanOrEqualTo('b')
          .isLowerCase();
          
### Map 검증

        assertThat(map)
          .isNotEmpty()
          .containsKey(2)
          .doesNotContainKeys(10)
          .contains(entry(2, "a"));
          

### description 추가

        assertThat(person.getAge())
          .as("%s's age should be equal to 100", person.getName())
          .isEqualTo(100);
          
        // 콘솔창에 에러시 다음과같이 출력
        [Alex's age should be equal to 100] expected:<100> but was:<34>
        

### 에러를 던지는 메서드 검증시

    assertThatThrownBy(() -> { throw new Exception("boom!"); }).isInstanceOf(Exception.class) .hasMessageContaining("boom");
    
   
## 테스트 대역이 필요할때는 Mockito

유닛테스트에서 빼먹을 수 없는 또하나의 라이브러리는 바로 목킹 라이브러리다.

유닛 테스트의 핵심은 의존성을 제거하고 해당 모듈, 해당 클래스만을 테스트하는게 목적이기 때문에,

테스트하려는 타겟 클래스의 객체가 의존하는 다른 객체들을 최소한의 동작만 하는 테스트 대역(`Double`)으로 교체해주어야 한다.

이때 이 테스트대역이 하는 일에 따라 구체적으로 `Stub`과 `Mock`으로 나뉜다.


|  |  |  |
| --- | --- | --- |
| stub | 상태검증시 사용 | 인스턴스화하여 구현한 가짜 객체, 실제로 동작하는것처럼 보이게 만든 객체 |
| mock |  행위검증시 사용| 호출에 대한 기대 명세에 따라 실제로 동작하는 객체 |

>- 상태 검증(state verification)<br>
테스트 할 기능이 실행 된 후에, 테스트 대상 객체와 협력 객체의 상태가 기대되는 상태인지 검증한다.
>- 행위 검증(behavior verification)<br>
테스트 할 기능이 실행된 후에, 테스트 대상 객체와 협력 객체가 기대하는 순서대로 메시지를 주고 받았는지(호출이 일어났는지) 검증한다.

위의 내용만 보면 와닿지 않을수 있는데 행위검증과 상태검증 테스트 코드를 통해 보면 명확하게 차이를 확인할 수 있을것이다.

### 상태 검증 테스트 코드

    class Test{

      @Mock
      Stub stub
      @InjectMocks
      Target target
    
      @Test
      void test(){
        // given
        when(stub.action()).thenReturn("do Action");       
        
        // when
        Stirng result = target.callStub();
        
        // then
        assertThat(result).isEqualTo("do Action");
      }    

    }


target의 테스트 메서드 `callStub()`를 호출할때, 의존하는 Stub의 `action()` 이 어떤 동작을 할지, 정확히 지정해주고(`when()`)

해당 값을 검증하는 방식으로 결과의 상태를 검증하는 방식이다.

### 행위 검증 메서드


    class Test{

      @Mock
      Stub stub
      @InjectMocks
      Target target
    
      @Test
      void test(){
        // given        
        // when
        target.callStub();
        
        // then
        verify(stub).action();
      }    
    }

해당 목킹 객체가 `action()`을 호출했는지 행위를 검증하는 방식으로, 상태의 결과값은 확인할 수 없지만 내부에서 목킹객체를 호출했다는 행위 자체는 검증가능하다.

따라서 stub작성과 같은 수고를 덜어도 되므로 훨씬 테스팅이 수월해진다.

하지만 정확한 상태값을 확인할 수 는 없으므로, 테스트의 목적에 따라 방법을 잘 선택해서 사용해야할 것이다.

## mokito 주요 api
### 목킹 주요 어노테이션

- @Mock: Mock 객체를 만들어 반환해주는 어노테이션
- @Spy: Stub하지 않은 메소드들은 원본 메소드 그대로 사용하는 어노테이션
- @InjectMocks: @Mock 또는 @Spy로 생성된 가짜 객체를 자동으로 주입시켜주는 어노테이션

### stub으로서 결과 처리
- doReturn(): Mock 객체가 특정한 값을 반환해야 하는 경우/ 메서드 실제 호출 x
- doNothing(): Mock 객체가 아무 것도 반환하지 않는 경우(void)
- doThrow(): Mock 객체가 예외를 발생시키는 경우
- thenReturn(): 메소드를 실제 호출하지만 리턴 값은 임의로 정의 할 수 있다. 실제 메소드를 호출하기 때문에 대상 메소드에 문제점이 있을 경우 발견 할 수 있다.


# References
- [https://github.com/team-tancheon/book-lounge/issues/16](https://github.com/team-tancheon/book-lounge/issues/16)
- [AssertJ 공식 Doc 참조](http://joel-costigliola.github.io/assertj/core-8/api/index.html)
- [https://pjh3749.tistory.com/241](https://pjh3749.tistory.com/241)
- [https://www.baeldung.com/introduction-to-assertj](https://www.baeldung.com/introduction-to-assertj)
- [https://medium.com/@SlackBeck/mock-object%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-85159754b2ac](https://medium.com/@SlackBeck/mock-object%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-85159754b2ac)