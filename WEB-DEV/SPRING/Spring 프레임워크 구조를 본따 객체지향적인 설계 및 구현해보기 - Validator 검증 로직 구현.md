# 개요

최근 회사에서 기능 개발을 하던 도중 검증 로직을 구현할 일이 생겼다.

사실 서버개발은 물론, 대부분의 기능개발에 있어서 데이터 검증은 필수다.

 클라이언트에서 건내주는 코드는 항상 의심하고 신뢰하지 않는 것이 오작동과 버그를 막는 가장 확실한 길이기 때문이다.

여기서 문제는 검증(`validate`)로직은 일반적으로 비즈니스 룰에 따라 언제든 추가될수도, 삭제될수도 있는 변경에 취약한 로직이라는 점이다.

유연한 코드와 유연한 구조는 그만큼 복잡한 코드와 복잡한 구조라는 뉘앙스를 내포하기 때문에, 처음부터 변경을 염두해둔 코드를 짜는건 선호하지 않지만,

검증 로직은 애초에 변화가 잦을수밖에 없는 로직이므로, SOLID를 고려해 고도화하는 작업을 진행하였다.

# Reference

## springframework.validation.Validator

우리가 볼 수 있는 가장 교과서에 가까운, 모범적인 코드는 무엇일까?

나는 개인적으로 오픈소스 라이브러리, 프레임워크라고 생각한다. 특히 수백만 개발자가 신뢰하며 20년간 사용하고 있는 스프링 프레임워크의 코드는 객체지향의 정수라고 생각하며

코드를 분석함으로서 정말 많은것을 얻을수 있다고 믿는다. 

또한 개발자로서 성장하고 지향해야할 방향성 역시 궁극적으로 개발자를 위한 개발자가 되고, 개발자를 위한 코드를 작성하는 것이라고 생각한다.

사족이 길었는데, 본론으로 들어가면

이번 validate 로직을 고도화하여 설계하는데 `springframework.validation.Validator` 코드를 많이 참조하였다.

 [Validator Javadocs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/Validator.html)

    public interface Validator {
    	boolean supports(Class<?> clazz);
    	void validate(Object target, Errors errors);
    }
    

`Valildator` 인터페이스는 아주 단순하게 `support()` 메서드와 `validate()` 메서드 두가지만을 퍼블릭 인터페이스로서 메시지를 수신받을수 있으며

실제 구현체는 `support()` 메서드를 통해 검증 지원여부를 판단하고, 지원이 가능할 시`validate()` 로 검증하는 구조로 되어있다.

즉 `SRP`를 준수하여 책임을 잘 분할하기만 하면, 새로운 검증로직이 필요할 때, 구현체만 추가하고 `DI`로 주입하여 `OCP`를 지키는 유연한 확장이 가능해진다.

나는 이번에 회사에서 검증 로직을 구현하고 고도화하면서, 스프링 프레임워크의 `Validate` 구조를 그대로 본따 설계를 진행하였다.

## 스프링 `DI`: 같은 타입 여러 구현체 주입받기

이전에 한번 다룬적이 있는 [Map형태로 주입받기](https://www.jiniaslog.co.kr/article/view?articleId=1103) 를 변형하여

이번 코드에 적용시켜보았다.

# 실제 validate 모듈 구현

>회사 코드이므로 전체 소스코드를 공개할 수는 없으며, 수도코드 및 요약된 코드들로 대체하였다. 하지만 OOP를 학습했다면 충분히 구조를 추론할 수 있으므로 세부적인 구현은 생략하겠다.

## Validator 인터페이스

    public interface Validator {
        boolean isSupportType();
        void validate(검증할 파라미터값);
    }
    
우선 `Spring.validation.validator` 인터페이스와 동일한 역할을 하는 `Validator` 인터페이스를 정의했다.

## ValidatorFactory

    @Component
    @RequiredArgsConstructor
    public class ValidateFactory {
        private final List<Validator> validators;
    
        public Validator getValidatorBy(Type type) {
            return validators.stream()
                    .filter(validator -> validator.isSupportType(type))
                    .findFirst().orElseThrow(TypeNotFoundException::new);
        }
    }
    
그리고 `Validator`의 세부 구현체들을 `DI`로 `List`로 주입받고, 타입에 따라 지원 여부를 판별하여 세부 구현체를 반환해주는 `Factory`를 정의했다.

이처럼 [생성과 사용을 분리하는 설계로](https://www.jiniaslog.co.kr/article/view?articleId=1704) 결합도를 느슨하게 갖게 하고, 변경에 용이한 구조가 되었다!

## 세부 구현체 구현

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/a70b1b94-f012-4dd3-be53-06d4e56710fe.png)

세부 구현체들을 위와같이 구현함으로서, 타입에 따라 검증을 달리하는 `SRP` 를 달성함은 물론, 새로운 타입이 추가되거나 삭제될경우 해당 `validator`만 추가 / 삭제하면 되므로 `OCP`를 만족하는 유연한 객체지향 설계가 되었다.

## Client에서 사용시

    Validator validator = validateFactory.getValidatorBy(type);
    validator.validate(검증할 파라미터);

단 두줄만을 사용해 모든 타입에 대한 검증을 `If else`없이 구현함으로서, Client에 해당하는 `Service` 클래스를 얇게 가져갈수 있게되었다.
