# DI란?

## 의존 관계?(Dependency Relationship)

![image](https://miro.medium.com/max/1400/1*DOKE8eW4eQVZOzVm0Y5hSA.png)

SW공학에서 **의존이란** 의존 대상, 그림에서는 B가 변하면 그것이 A에 영향을 미칠때, 

>A가 B를 의존한다

라고 말한다.

- 쉽게 이해하면 A 코드안에는 B가 존재, B의 코드안에는 A가 존재하지 않는 케이스라고 보면 된다.
- 예시) A가 B를 사용하는경우, 그림처럼 A에서 B에 정의된 메서드를 호출하는 경우

## 그래서 DI는 뭔데?(Dependency Injection)

![image](https://media.vlpt.us/images/gillog/post/41f2eb24-fce2-4b7e-b9ac-d5c3ce97d213/22535642580C4AF12C.jpg)

- `IOC(Inversion of Control)`는 현대 소프트웨어 컨테이너 개념에서 흔히 사용되어진다. 톰캣도 서블릿의 생명주기를 관리하는 서블릿 컨테이너이고 스프링도 빈의 생명주기를 싱글톤으로 관리하는 컨테이너이다.

- 하지만 스프링이 세상에 등장할 당시 기존 프레임워크들과 차별화되었던 기능은 바로 DI였다.

> DI란 컨테이너에 등록된 객체들의 생명주기를 관리하기에 앞서, 객체 생성단계에서 **객체간의 의존관계를 알아서 파악**하고,  특정 객체에 **필요한 객체를 컨테이너가 스스로 판단하여 연결**시켜주는 기능이다.

## DI를 번역하면?

- DI를 보통 의존성 주입이라고 번역하는데 의존성이라는 단어는 직관적이지 못하다. 위의 그림에서 `A에게 B의 의존성을 주입한다` 라는 말이 쉽게 이해가 가는가?

- 이보다는 `A에게 B와의 의존관계를 주입한다` 혹은 조금더 의역해서 `A에게 B와의 의존관계를 설정한다` 라고 말하는것이 보다 직관적이지 않을까 싶다. 

- `토비의 스프링 3.1`에서는 DI를 **의존관계 주입**으로 번역하고 있으며, 본 포스팅도 이를 따르도록 하겠다.

## 의존성을 주입하는 이유

### 결합도(Coupling)를 낮추자!

- 소프트웨어 공학에서 바람직한 설계는 모듈간 결합도를 낮추는데서 시작한다.


|높은 결합도 | |  |  |  |  ||낮은 결합도|
| --- | --- | --- | --- | --- | --- |--|--|
| 내용 | 공통 |  외부|  제어| 스탬프 | 자료 |메시지|결합도 없음|

- 강한 결합 : 만약 A객체가 코드상에서 B객체를 생성하게된다면 이는 A객체가 B객체의 생명주기를 다루고 있기때문에 제어권을 갖고 있는 셈이며 상당히 높은 결합도를 갖게된다.

- 느슨한 결합 : 하지만 DI를 통해 의존관계를 주입받게되면 A객체는 B의 생명주기에 관여하지 않기 때문에 결합도가 낮아지며, 런타임시에 의존관계가 설정되므로 훨씬 유연한 구조가 된다.

- 느슨한 결합으로 SOLID 원칙중 OCP를 지키기 매우 수월해진다.

# 스프링의 DI 방법

## Field Injection
- 변수 선언부에 @Autowired Annotation을 붙인다.
- 이름 그대로 필드에 의존관계를 주입하는 방식

        @Component
        public class SampleController {
          @Autowired
          private final SampleService sampleService;
        }
        
### Field Injection의 문제점

- 코드가 간결해서 유혹되기 쉽지만, 코드가 한번 작성되면 외부에서 변경이 불가능하기때문에 테스트하기 힘들어진다.
- 스프링에 의존적인 코드이기때문에 DI 프레임워크를 사용하지 않으면 동작하지 않는다.
- 테스트코드작성이나 @Configuration 클래스정도가 아닌이상은 사용하지 말자


## Setter Injection
- setter를 통해 선택적인 의존관계를 주입할때 사용
- 상황에따라 지연 주입이 가능!
                
        @Component
        public class SampleController {
          private SampleService sampleService;
 
          @Autowired
          public void setSampleService(SampleService sampleService) {
            this.sampleService = sampleService;
          }
        }
  

- 의존관계가 주입되지 않은 시점에서도 객체가 생성 가능하며, 런타임상에서 의존관계 주입시점을 임의로 조절가능하다.
- 하지만 반대로 이는 객체의 상태가 불변하는 `stateless`를 깨는 방식이므로 객체의 관리와 안정성을 흔들 위험이 있다.
- null 체크를 해야하므로 코드가 굉장히 번거로워진다.
- 정말 특수한 경우에만 사용하도록 하자

## Constructor Injection(권장!)

- 클래스의 생성자에 @Autowired를 붙이는 방법

        @Component
        public class SampleController {
          private final SampleService sampleService;
     
          @Autowired
          public SampleService(SampleDAO sampleDAO) {
              this.sampleDAO = sampleDAO;
          }
        }
   
### Constructior Injection을 사용해야하는 이유!
     
- Spring Framework Reference에서 권장하는 방법
- 필수적으로 필요한 의존성을 객체생성단계에서 강요하므로서 코드의 안전성을 부여
- 단일생성자일경우 어노테이션을 붙이지 않아도 되므로 스프링에 의존적인 코드가 아니어서 외부 사용이 훨씬 용이해짐(테스트 코드 등)
- 컴파일단계에서 의존성 순환을 잡아낼수 있다.

# References
- [이일민, 토비의 스프링 3.1, 에이콘](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788960773431&orderClick=LEa&Kc=)
- [김영한, 스프링 핵심원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)
- [https://velog.io/@gillog/Spring-DIDependency-Injection](https://velog.io/@gillog/Spring-DIDependency-Injection)
