# 8장 경계
## SW공학에서 경계란?
> 우리 코드와 외부 코드 사이에 있는 영역, 인터페이스 사용자와 제공자와의 간격

- 오픈 소스, 라이브러리를 안쓰는 프로젝트는 없다.
- 우리가 만든 코드는 외부에서 만들어진 코드와 병합되어야 한다.
- 외부코드란, 외부에서 만든 코드 또는 외부 시스템을 호출하는 코드들을 모두 의미한다.
- 외부코드의 변화(버전에 따른 deprecated, 혹은 spec 변화 등)에 내부코드는 영향을 최소로 받아야된다.
- 이를 위해서는 외부코드와 내부코드의 경계를 잘 짓고 적절히 격리해놨을때, 좋은 통합이 이루어질것이다.


## 경계를 잘 만드는 방법

### 캡슐화(Encapsulation)
> 객체의 실제 구현을 외부로부터 감추는 방식

   객체지향을 처음 배우게 될 때, 캡슐화를 보통 정보 은닉, 보안을 위한 기법으로 배우고 필드 private 접근제한자 설정을 대표적인 예시로 접하게 된다. 

하지만 진정한 캡슐화 의도는 `객체의 실제 구현중 노출이 불필요한 부분을 외부로부터 감추는` 것과 `객체가 자기 스스로 자신의 상태변화를 일으키게 하는 자율적인 객체`로 만들기 위함이라고 생각한다.

즉 좋은 캡슐화는 1. 구현이 분리된 좋은 인터페이스를 만들며, 2. 외부로부터 자율적인(낮은 결합도의) 클래스작성이 가능케한다.

### 방법 1. API Spec 클래스 정의하기 - `Map`과 `VO`

예를들어 레포지토리를 거쳐 서비스단에서 특정 데이터를 담는 Map 객체가 만들어졌다고 생각해보자.

`Map` 을 그대로 반환하는 메서드로 마무리할경우, 협업과정에서 다른 개발자가 컨트롤러를 구현하고 해당 서비스의 메서드를 호출하는 과정에서 `Map`을 받아올 수 있다.

이때, 다른 개발자는 해당 메서드의 코드를 까보기전까지는 `Map`에 정확히 어떤데이터가 담겼고, 또 그 데이터를 `clear()` 하거나, `add()` 하는등 조작을 해도 되는지 안되는지 확신이 서지 않는다.

`Map`의 모든 api가 `public`하게 열려있으므로, 다 허용되겠거니 생각하며 사용하게 되고 이는 잠재적 장애 유발의 가능성을 내포할 수 밖에 없다.

여기서 해당 `Map`을 감싸는 `VO`클래스를 재정의하고 메서드가 이를 반환하게 바꾸면 경계가 훨씬 명확해지고, 안전해진다.

    public class Vo{
        private Map<Long,Data> dataBase = new HashMap();
        
        public Data getById(Long id){
            return dataBase.get(id);
        }
    }

### 방법 2. 외부코드와 호환하는 어댑터 패턴

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/4f21d901-a124-42d5-8a88-168252df698e.png?raw=true)

> 접근하고자(의존하고자) 하는 클래스의 인터페이스를 사용자가 원하는 인터페이스로 변환하는 패턴으로 호환성이 없는 인터페이스를 조작할수 있게해준다.

만약 우리가 외부 API를 사용할 일이 생겼을때, API를 직접 사용하여 코드를 작성한다고 가정해보자.

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/14f62120-bfa0-439a-ba65-d38a65ae29a6.png?raw=true)

이와 같이 외부코드 사용자(우리)가 직접 외부 API에 의존관계를 맺게되면, 외부 API의 스펙이 변화할경우 우리 코드 전체도 직접 그 변화에 영향을 받게 되기 때문에 변경에 취약하게 된다.(OCP 위반)

이를 어댑터 패턴을 통해 결합도를 느슨하게 만들면, 우리 코드는 어댑터의 인터페이스만 바라보므로 아무리 외부 코드가 변화할지라도 내부 코드는 병경에 닫혀있게된다.(solid 준수)


#### 어댑터 패턴 실제 코드로 보기

**어댑터 인터페이스 / 전략패턴 인터페이스**

     /**
      * 파일 업로드 전략패턴 추상화 인터페이스
      */
     public interface ImgUploadStrategy {
         String uploadFile(MultipartFile file, String storeFileName);
     }
    

**어댑터 구현체 / 전략 구현체**

     /**
     * S3 이미지 업로드 전략 어댑터
     */
    @RequiredArgsConstructor
    @Service
    public class AwsS3ImgUploadStrategy implements ImgUploadStrategy {
    
        private final AmazonS3 amazonS3; // 외부 API
        @Value("${cloud.aws.s3.bucket}")
        private String bucket;
    
        // 외부 API를 사용하는 어댑터이자 전략
        @Override
        public String uploadFile(MultipartFile file, String storeFileName) {
            ObjectMetadata metadata = createObjectMetadata(file);
            try(InputStream inputStream = file.getInputStream()) {
                amazonS3.putObject(new PutObjectRequest(bucket, storeFileName, inputStream, metadata)
                        .withCannedAcl(CannedAccessControlList.PublicRead));
            } catch (IOException e) {
                throw new IllegalArgumentException("파일 업로드에 실패했습니다.");
            }
            return amazonS3.getUrl(bucket, storeFileName).toString();
        }
    

위의 코드는 최근 토이프로젝트에서 사용한 이미지 업로드 서비스 로직으로, 전략패턴과 어댑터 패턴이 혼용된 사례로 볼 수 있다.

현재는 전략이 하나이므로 어댑터 패턴으로서의 기능만 수행하나, 차후 여러 전략이 존재하게되면 이를 연결하는 인터페이스 덕분에 전략 패턴으로서도 작동 가능하다.

만약 전략패턴을 고려하지 않고 어댑터로서의 의도만 생각했다면 `ImgUploadStrategy` 인터페이스는 작성하지 않아도 무방할 것이다.

### 방법 3. 러닝 테스트 작성

외부 라이브러리를 우리코드에 적용할때 가장 좋은 방법은 외부라이브러리를 우리가 사용할 의도와, 코드 스타일에 맞게 단위테스트를 작성하는 것이다.

외부 라이브러리 러닝만의 테스트를 작성하면 해당 테스트코드를 돌림으로써 변경에 대한 작동 여부 체크가 가능하고 추적이 가능하므로 호환성 체크가 가능하다.

또한 해당 테스트 작성을 통해 외부 라이브러리 이해도를 높일수 있는 부가적인 장점도 존재한다.


# 9장. 유닛 테스트

## 테스트 코드의 중요성
### 테스트 코드는 런타임 전에 실수를 바로잡아준다.

테스트 코드가 없다면 해당 로직에 잘못된점이 있는지 없는지 런타임시점에 실제로 조작을 통해 발견해야하므로 더 많은 시간과 노력이 들어간다.

### 테스트 코드는 변경을 쉽게 해준다.

리팩토링이나 새로운 기능 추가시, 해당 로직때문에 다른 로직이 정상 작동하지 않을수도 있다. 테스트코드는 이런 문제를 사전에 방지하여, 장기적인 생산성 저하를 막아준다.

>단위 테스트는 코드의 유연성, 재사용성, 유지보수성을 제공하는 버팀목, 테스트 케이스가 없다면 모든 변경은 잠정적 버그다!

### 하지만 지저분한 테스트 코드는 없는것만 못하다...
테스트 코드의 가독성이 떨어지고, 제대로 격리가 되지 않으며, 의존성이 복잡하게 얽힌 테스트코드는 오히려 생산성 저하를 가져올 위험이 크다.

제대로된 테스트코드 작성법을 알아야한다!

[이펙티브 유닛 테스트를 나중에 꼭 읽어보자](http://www.yes24.com/Product/Goods/11361087)


### 테스트는 자동화되어야 한다
CI/CD 빌드시 자동화 테스트를 통해 무조건 코드 검증을 해야한다.


## 테스트의 종류

### Test Pyramid 

![image](https://media.vlpt.us/images/ahnjh/post/760717e9-b1b4-4a2c-8566-283019fcf8e2/%E1%84%80%E1%85%AE%E1%84%80%E1%85%B3%E1%86%AF%E1%84%90%E1%85%A6%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%E1%84%91%E1%85%B5%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%83%E1%85%B3.png?w=768)

**구글 권장 비율**
unitTest(70%)
Integration Test(20%)
E2E Test(10%)

### Unit Test
프로그램 내부의 개별 컴포넌트의 동작을 테스트한다. 배포하기 전에 자동으로 실행되도록 많이 사용
### Integration Test
프로그램 내부의 개별 컴포넌트를 합쳐서 동작을 테스트한다.
### E2E
실제 유저 시나리오대로 네트워크를 통해 서버의 EndPoint를 호출해서 테스트한다.


## 유닛테스트시 준비물
### 테스트 라이브러리 / 프레임워크 (Java 기준)

- Junit
- Mockito
### Test Double
테스트에서 원본 객체를 대신하는 객체를 총칭

1. Stub : 원래의 구현을 최대한 단순한 것으로 대체, 테스트를 위해 프로그래밍된 항목만 응답
2. Spy : Stub +@. 호출에 대한 정보 기록, 추적, 몇번 호출됬는지 체크 등등
3. Mock : 행위 검증을 위해 가짜 객체를 만드는것 Stub은 상태검증, Mock은 행위 검증으로 구분되나 실무에서는 그냥 Mock이라 부르는편

### given - when - then 패턴

        // given
        Todo todo1 = Todo.builder()
                .name("단위 테스트합시다")
                .completed(null)
                .build();
        Mockito
                .when(todoRepository.save(todo1))
                .then(invocation -> mockRepositorySave(todo1));
        // when
        Long todoId = todoService.createNewTodo(todo1);
        // then
        assertThat(todoId).isEqualTo(todo1.getId());
        

테스트 코드 작성 흐름을 크게 3가지로 나누어 작성하면 가독성이 더 좋아진다.

- given : pre-condition. 주어진 조건
- when : 테스트하고 싶은 동작
- then : 테스트 결과 확인

여기서도 보다 깨끗한 테스트를 위해선 각 행동의 로직들을 별도의 메서드로 분리해보는것도 생각해 볼 수 있다.

### [토이프로젝트 테스트코드를 통해 실사용 예시 보기](https://github.com/jinia91/ToDoList_Rest_Api/tree/main/src/test/java/jinia/todoapp)


## F.I.R.S.T. 원칙

### Fast
테스트는 자주, 많이 돌릴수록 좋기때문에 빠르게 돌아가야한다. 너무 느린 테스트코드는 그 자체로 생산성 저하를 만든다.

### Independent
각 테스트는 독립되어야 한다. 서로에게 의존하면 실패한 원인을 찾기 어려워진다.

### Repeatable
테스트는 어떤 환경에서도 반복 가능해야한다. 실제환경, QA 환경, 모든 환경에서 돌아가야한다.

### Self-Validating
테스트는 그 자체로 bool값으로 결과를 나타내야한다. 추적없이도 뭐가 테스트 실패다라고 직관적으로 인식해야한다.

### Timely
테스트하려는 실제 코드를 구현하기 전에(TDD!) 테스트 코드를 구현한다.


## 그외에 알아두기

### 테스트 하나에 Assert 하나?

테스트 하나에 검증 하나만 하면 해당 테스트가 실패할때 어떤 점이 문제인지 바로 파악하기 쉽고 가독성이 좋아지지만 그만큼 테스트 코드가 많아지고, 중복되는부분도 많아지게된다.

권장하지는 않지만, 생산성과 필요에 따라 얼마든지 테스트하나에 여러 Assert 작성도 가능하다.

### Junit5에 대해 제대로 공부하자

자바 개발자로서 가장 많이 사용하는 테스트 프레임워크이다. `@ParameterizedTest`, `@CsvSource` 등 다양한 테스트 조작법에대해 공부해보자