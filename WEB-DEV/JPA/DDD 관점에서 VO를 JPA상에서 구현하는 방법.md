# DDD의 VO와 엔티티
## Value Object -DDD
DDD 관점에서 애플리케이션이 소프트웨어적으로 해결하려는 모든 영역은 도메인 영역이라고 볼 수 있다.

하지만 도메인 영역안의 모든 관심사를 도메인 모델링하여 엔티티화시킨다면, 그 복잡도는 지수적으로 증가하게되고 결국 비즈니스로직의 복잡도에 짓눌려 변경에 취약한 설계가 되버리기 쉽다.

따라서 에릭에반스의 DDD 책에서는 도메인 모델링시 주요관심사를 Entity와 VO로 구분하여 바라볼것을 제안하는데,  VO의 개념과 요소는 다음과 같다.

- 고유 식별자(Id)가 필요없는 순수 값 개체
- String, int, long 등 primitive한 타입으로 표현가능하나, 비즈니스 로직의 원할한 표현을 위해 타입을 정의할 필요가 있는 값
- `되도록` 변경되지 않는 불변객체
- `개념적 식별성을 갖지 않으면서 도메인의 서술적 측면을 나타내는 객체 - DDD`

> 실무에서는 도메인 객체를 캡슐화하기위해 레이어드 아키텍처 기준 프리젠테이션영역, 혹은 헥사고날 아키텍처 기준으로 포트영역으로 내보낼때 DTO로서 VO라는 컨벤션으로 객체를 새로 만들어 사용하는 경우도 있는데, 여기서의 VO와 DDD에서 언급하는 VO는 다른 개념으로 봐야할거같다. 이 케이스에서의 VO는 view단을 위한 DTO로서 개념에 더 가깝다고 생각된다.



# JPA상에서 VO 구현 방법
## 1. 엔티티의 단일컬럼에 VO 담기

Member가 여러가지 Role을 가질수 있는 시나리오를 상상해보자.

DB관점에서 `member` 엔티티의 테이블과 `member_role` 테이블을 연상하고 두 테이블간 1:N관계를 생각해볼수도 있지만, 비정규화를 통해 `member_role`을 `member` 테이블의 특정 컬럼안에 json형태로든, 특정 포멧으로든 전부 집어넣는 방식도 생각해볼 수 있다.

값 객체를 굳이 별도의 테이블을 만들지 않고, 테이블의 단독 컬럼에 넣는 방식을 JPA에서 지원하고 있는데

### Entity

    @Entity
    public class Member {
    ....
    @Column(columnDefinition = "VARCHAR(100)", nullable = false)
    @Convert(converter = MemberRoleCollectionConverter.class)
    private Set<Role> roles = new HashSet<>();
    
    ....
    }
    
위와같이 코드를 작성하고 `MemberRoleCollectionConverter` 를 작성해주면 된다.

### Converter

    public class MemberRoleCollectionConverter implements             AttributeConverter<Set<Member.Role>, String> {
    
    @Override
    public String convertToDatabaseColumn(Set<Member.Role> attribute) {
       
     ....

      
    }

    @Override
    public Set<Member.Role> convertToEntityAttribute(String dbData) {
       
    ....
   
    }
    }




### 실무적으로 QDSL을 사용한다면 지양하자

값 객체를 엔티티의 한 컬럼안에 전부 담는 방식으로 DB관점에서 join 없이 비정규화된 하나의 테이블만 조회하면 되므로 성능상으론 나쁘지않다.

또한 JPA를 통해 애플리케이션단에서 엔티티의 참조를 통해 값 객체를 조회할때는 1대 N의 데이터들이 잘 바인딩되므로 이론상으론 정말 괜찮은 방법이다.

하지만 QDSL을 사용한다면 문제가 생기는데, QDSL의 버그인지 최신 버전 라이브러리기준으로 `Converter`를 제대로 인식하지 못하여 값객체를 조회하지 못하는 문제가 존재한다.

만약 QDSL을 사용하는 환경이라면 `Converter` 를 통한 비정규화 모델링은 지양하도록 하자.



## 2. @ElementCollection, @CollectionTable

JPA에서는 DDD의 관점에서 엔티티 Root에 종속되는 값타입을 컬렉션으로 매핑할수 있는 추상화된 어노테이션과 기능을 직접 제공하는데 바로 `@ElementCollection 과 @CollectionTable`이다.

javadoc을 통해 해당 어노테이션 사용법을 확인 할 수 있으며,


        // 엔티티
      @Entity 
      public class Person {
         @Id protected String ssn;
         protected String name;
         protected Address home;
         ...
         // 엔티티안에 primitive 타입 String으로 값타입 저장하기
         @ElementCollection  // use default table (PERSON_NICKNAMES)
         @Column(name="name", length=50)
         protected Set<String> nickNames = new HashSet();
         ...
      }

      // 값타입 VO 정의
      @Embeddable 
      public class Address {
         protected String street;
         protected String city;
         protected String state;
         ... 
       }
  
        // 값타입 VO를 엔티티에 매핑하기  
      @Entity 
      public class WealthyPerson extends Person {
         @ElementCollection
         @CollectionTable(name="HOMES") // join 되는 테이블명
         @AttributeOverrides({
            @AttributeOverride(name="street", 
                               column=@Column(name="HOME_STREET")),
            @AttributeOverride(name="city", 
                               column=@Column(name="HOME_CITY")),
            @AttributeOverride(name="state", 
                               column=@Column(name="HOME_STATE"))
          })
         protected Set<Address> vacationHomes = new HashSet();
         ...
      }


위와같은 방식으로 `Address` 라는 객체는 `HOMES`라는 테이블에 매핑되면서도 고유 식별자는 갖지않는 값타입으로 정의가 가능하다.

JPA에서 정의한 값타입 컬렉션의 명세와 특징으론 다음과같다.

- 영속성 전이(Cascade.ALL) 이 기본이며, 부모엔티티(Root)와 생명주기를 반드시 같이한다.
- default로 지연로딩이며, 필요에 따라 즉시 로딩도 가능하다.
- 엔티티와 달리 식별자 개념이 없다.
- 변경시 추적이 어렵다
- 변경사항이 발생시 모든 값을 다 지우고 새로 저장하므로 성능상 단점이 존재한다.
- 값 타입 테이블은 모든컬럼을 결합해 기본키로 사용한다.

DDD의 값타입(VO)를 그대로 구현한 어노테이션이며 상당히 유용하긴 하지만, 

값 객체 Collection에 새로운 값이 추가되거나 기존 값이 변경될 시 `All Delete and Re-insert` 하는 성능상 이슈, 값 객체는 반드시 root 엔티티에 종속되어야한다는 점, 정의된 명세대로만 따라야한다는 점 등 때문에

유연성이 떨어지는 단점도 존재한다.

## 3. @ManyToOne, @OneToMany 로 매핑해서 관리하기

마지막 방법으로는 JPA에서 제공하는 연관관계 매핑을 그대로 이용하되, @ManyToOne으로 연결되는 값객체 대상 엔티티를 애플리케이션단에서 값 객체로 여기고 다루도록 설정을 추가하는 방식이다.


    // OneToMany Root
    @Entity 
    public class WealthyPerson extends Person {
             @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true) // Aggregate Root 를 위한 일관성 설정
    @JoinColumn(name = "PERSON_ADDRESS")
             protected Set<Address> vacationHomes = new HashSet();
             ...
          }
          
    @Entity
    @Table(name = "PERSON_ADDRESS")
    @Value
    @EqualsAndHashCode(callSuper = false)
        public class Address extends IdentifiedValueObject{
             protected String street;
             protected String city;
             protected String state;
             ... 
           }
           

    // 값객체의 식별자를 관리하는 상위 클래스 별도 정의
    @MappedSuperclass
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public abstract class IdentifiedValueObject {
        @Id
        @GeneratedValue
        @Getter(AccessLevel.PACKAGE)
        private Long id;    // 패키지 접근제어를 통해서 식별자 은닉. 단, hibernate는 접근 가능
    }
    

이와같이 low 레벨에서 엔티티간 연관관계를 맺되, 애플리케이션단에서 설정을 통해 aggregate root에 종속된 엔티티를 값객체로 강등시키는 방법은, 해당 값객체가 엔티티로 독립되는 변경에도 유연하게 대응 가능하며,

DB영역에서는 식별자가 존재하고, 마이바티스 등 직접적인 쿼리를 다룰때에 보다 제약없이 접근이 가능하므로 실무적으로 가장 좋은 방법으로 권장된다.