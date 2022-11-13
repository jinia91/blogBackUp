# 개요

kotlin 1.5에서 추가된 `value class` 를 실험적으로 product 레벨에 적용해보면서 겪은 삽질들을 기록하고자한다.

# Value Class?

## Value Object?
`value class`를 이해하기 위해선 우선 `value object(이하 VO)`에 대해 알아야한다.

VO는 [이전에 포스팅에서 간단히 정리](https://www.jiniaslog.co.kr/article/view?articleId=1852) 한적이 있기때문에 해당 글을 읽어보길 바란다.

간략히 요약하자면 애플리케이션의 도메인 영역에서 관심사를 다루기 위한 수단으로서 특정 개념을 객체로 만들어 관리하는 컨셉이다.

이해하기 쉬운 예제로는 켄트백의 tdd나 조용호님의 오브젝트 등의 저서에서 나오는 `화폐 class` 같은개념일것이다.

##  실제 코드에서는 VO를 어떻게 구현할까?

VO를 코드레벨로 구현하기위해서는 다음의 몇가지 원칙을 가져야한다.

- 고유 식별자가 필요없는 순수값
- 생성된 값은 수정 불가능(`setter등 으로 불가능, final field!`)
- String, int, long 등 primitive한 타입으로 표현가능하나, 비즈니스 로직의 원할한 표현을 위해 타입을 정의할 필요가 있는 값
- 생성 방식

이를 준수하여 코드화하면 다음과 같은 클래스를 만들어 볼 수 있다.

    // 핸드폰 번호라는 값을 타입으로 다루어보자
    data class Hp private constructor(
      val number : String
    ) {
    companion object Utils{
        private val prefixNumbers = listOf("010", "011", "016", "017")

        fun getHp(phoneNumber: String) : Hp {
            require(checkPhoneNumber(phoneNumber)) { log.warn { "$phoneNumber is wrong" } }
            return Hp(phoneNumber.replace("-", ""))
        }

        private fun checkPhoneNumber(phoneNumber: String) = (phoneNumber.length == 11 || phoneNumber.length == 10) && phoneNumber.slice(0..2) in prefixNumbers
    }
    }



## Value class?

여기서 코틀린 개발자들은 

> 어차피 primitive 타입을 wrapping하는거라면 인스턴스 생성없이 컴파일러 차원에서 value object를 다룰수 있지 않을까

라는 아이디어로 만든 기능이 바로 `value class`이다.

    @JvmInline
    value class Hp private constructor(
    val number : String
    ) {
    companion object Utils{
        private val prefixNumbers = listOf("010", "011", "016", "017")

        fun getHp(phoneNumber: String) : Hp {
            require(checkPhoneNumber(phoneNumber)) { log.warn { "$phoneNumber is wrong" } }
            return Hp(phoneNumber.replace("-", ""))
        }

        private fun checkPhoneNumber(phoneNumber: String) = (phoneNumber.length == 11 || phoneNumber.length == 10) && phoneNumber.slice(0..2) in prefixNumbers
    }
    }
    
아까의 예시를 value class로 변환하면 위와같이 

- @JvmInline
- value class

로 선언하면 된다.

뭐가 다르지? 라고 생각될텐데

 Hp 클래스가 데이터 클래스일때와 value 클래스일때 객체를 생성하는 코드를 바이트 코드로 컴파일한뒤, 자바클래스로 디컴파일해보자

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/ad93898b-b60f-4809-845b-443db0f423ec.png)


(1) data class의 경우

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/6561a9a9-c873-4195-aa77-d171cac71158.png)

당연히 Hp 타입의 객체를 생성한다.

(2) value class의 경우
![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/70879eb4-4a7a-400f-933a-bf0591b18c23.png)

`Utils.getHp()` 메소드가 이상하게 바뀌고 반환된 값이 `String` 이 되었다!

 코틀린 컴파일러에 의해 value class가 실제로 바이트코드로 컴파일될때 내부의 primitive 타입을 보고 알아서 바꿔주므로 위와같은 결과물이 나오게된다.

이는 런타임시점에 실제 인스턴스를 만들지 않으므로 래퍼객체 생성의 오버헤드를 막아 보다 나은 퍼포먼스를 보여줄 수 있다.

### 메서드명이 이상한 이유?

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/70879eb4-4a7a-400f-933a-bf0591b18c23.png)

여기서 디컴파일된 함수명을 자세히보면 본래 메서드인 `getHp()`가 아니라 `getHp-BSJ4Wc8()` 로 되어있음을 볼 수 있다.

이는 `네임 맹글링`이라는 컴파일러 기법으로 

    // .kt
    getHp(input : String) : Hp
    getHp(input : String) : String
    
    // decompiled
    getHp(input : String) : String
    getHp(input : String) : String // 충돌!
    

위와같이 컴파일후 생길수 있는 충돌문제를 해결하기위해, value class를 인자로받거나 반환하는 메서드들에 대해 그 함수명에 해시값을 덧붙여준다.


# value class with JPA

위에서는 코틀린의 value class에대해 간단히 알아보았다. 

하지만 서버사이드 실무에서 사용하기 위해서는 다양한 라이브러리들과 병행되어야 하고 그중에서도 데이터 영속화 프레임워크인 JPA와의 궁합이 가장 중요할 것이다.

## Issue : mangling - method query
value class를 적용하면서 겪은 첫번째 문제는 JPA의 메서드 쿼리 생성 실패였다.

런타임 시점에 다음과같은 에러가 발생하는데
![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/332bf55e-c412-4699-9f1c-fd874625b07f.png)

1. 작성된 `JPA repository` 인터페이스에 value class가 포함된 메서드가 존재하는경우(eg.`findByHpAndBuyerCode(~~)`) 
2. 이 메서드를 코틀린 컴파일러가 맹글링하게되고(`findByHpAndBuyerCode-fApeRkE(~~)`)
3. 맹글링된 메서드 쿼리 바이트코드를 가지고 런타임시점에 실제 JPA 구현체로 만드는과정에서
4. 필드명에 맹글링이 포함되어버려(`Hp 그리고 BuyerCode-fApeRk`)
5. 이를 찾으면서 참조 불가 예외를 터뜨리게된다.(`BuyerCode-fApeRk 라는 필드명은 없따!`)

### solution

이슈 핸들링을 위해서는 맹글링을 막아야하는데 컴파일러 옵션에서 기본전략으로 맹글링을 끄는 옵션은 찾지 못했고

다만 메서드 명 위에

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/cbeb7fa2-2e42-429f-b491-7a3d605eccb0.png)

맹글링을 수동으로 메서드명과 동일하게 덧씌우고, warnning을 막아서 해결하였다.

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/671976f1-6932-47ec-98ae-7037a73bd27e.png)
[`PropertyPath` 객체를 만들때, 프로퍼티명이 맹글링 없이 들어가는게 디버깅으로 확인가능하다]

 
## Issue : Generic 
엔티티의 Id를 `value class`로 사용하면서 발견한 문제로 `JpaRepository`를 사용하기 위해 우리는 보통

>interface CustomRepository : JpaRepository<CustomEntity, CustomId> { ...}

와 같은 인터페이스를 선언한다.

이때 바이트코트를 까보면

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/39173c55-08c5-4486-ab6d-fe04feb098d9.png)

제네릭으로 들어있는 엔티티와 Id의 클래스 path를 메타데이터로 그대로 갖고, 이를  참조하여 기본 Jpa 구현체를 만들게 된다.

즉 Jpa Repository 구현체는 해당 엔티티의 Id를 순수하게 `CustomId` 타입으로 인식하고 있으며,

기본으로 제공되는 `findById`나 코틀린Crud 메서드인 `findIdOrNull`등을 사용하면 내부 primitive 값인 `Int 나 Long`으로 날라가면서 

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/a370db5b-4eaa-470c-a424-aed56511875b.png)

에러를 런타임때 뱉게된다.

위의 맹글링 이슈는 그나마 런타임시점에 앱을 띄우면서 터지기때문에 감지라도 되지만, 해당 이슈는 실제 레포지토리 메서드를 호출하기전까진 감지조차 안되므로 상당히 치명적이다..

### solution

위의 메서드 쿼리 재정의랑 동일하게 

    @Suppress("INAPPLICABLE_JVM_NAME")
    @JvmName("findById")
    override fun findById(id: CustomId): Optional<CustomEntity>

id로 찾는 메서드쿼리를 재정의하면 정상 동작됨을 확인할 수 있었다.


# 결론

여러 삽질을하면서 내린 결론은 `value class` 는 서버사이드에서 현재 사용 **불가능!**(코틀린 ver.1.7.20 기준)
코틀린컴파일러와 자바 구현체들간의 간극, JPA 자체의 동작 호환성문제 등 극복해야할 문제도 많고 내가 찾아내지 못한 이슈가 더 많을것으로 생각된다. 

당장 2번째 이슈도 런타임시점에서 발생한다는점에서 너무 치명적이고 테스트코드로 버그들을 전부 잡아낸다쳐도 안정성을 보장할수 없는 불안한 코드를 프로덕트레벨로 릴리즈하기엔...

사실 data class를 사용해 인스턴스를 만들더라도 그 오버헤드가 딱히 크다고 느껴지지도 않고, 굳이 이런 위험성을 갖고 value class를 쓸필요는 없을거 같다.

여담으로 근래 이직후 바쁘다는핑계로 나 자신의 공부를 소홀히 한거같다. 깃헙 1일1커밋도 깨졌고, 블로그도 한동안 방치되었고 스터디도 멈추고...

오늘부터 다시 맘잡고 열심히 달려보자!

# References
- [value class concept docs](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md#name-based-construction-of-classes)
- [value class mangling](https://kotlinlang.org/docs/inline-classes.html#mangling)
- [https://slack-chats.kotlinlang.org/t/554879/is-there-any-way-to-disable-mangling-for-functions-that-cons](https://slack-chats.kotlinlang.org/t/554879/is-there-any-way-to-disable-mangling-for-functions-that-cons)
