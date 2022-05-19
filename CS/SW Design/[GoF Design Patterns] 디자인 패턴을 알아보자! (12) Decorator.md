# Decorator 란?

>  데코레이터 패턴(Decorator pattern)이란 주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴으로, 기능 확장이 필요할 때 서브클래싱 대신 쓸 수 있는 유연한 대안이 될 수 있다. -wiki


![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/904db566-609f-4258-8ac8-5df9efcf7f7b.png)

## Composition Over Inheritance

Composition은 [composite 패턴](https://www.jiniaslog.co.kr/article/view?articleId=1102)을 설명하면서 다뤘던 주제로, 하나의 객체가 다른 객체를 래핑하고 있는 `has - a` 관계를 의미한다.

객체의 책임(기능)을 확장할 때, 상속을 사용하면 자식 클래스(Sub)는 부모클래스(Super)를 모두 알게되는 `White Box reuse` 가 되는데, 이 경우 높은 결합도로 인해 `OCP`가 위배되기 쉽다.

`Sub - Super` 클래스간 논리적 관계가 정말 완벽하게 `is-a`로 일치한다면 상관없지만, 대부분의 상황에서는 `has-a`에 가까운 경우가 많다.

이럴때, 객체를 감싸는 객체를 만들어 책임을 덧붙이는 `composition`을 사용하면 `Black Box reuse` 가 되어 두 객체간 결합도가 느슨하게 연결되고 SOLID를 준수하는 훨씬 유연한 설계가 될 수 있다.

그리고 `Composition Over Inheritnace (상속 보다는 합성을 사용해라)` 를 대표하는 디자인 패턴이 바로 데코레이터 패턴이다.

## 왜 필요한가? Pros
- 상속을 사용하지 않고도 객체의 기능(책임)을 추가할 수 있다.
- 런타임시점에서 객체의 책임 추가 삭제가 가능해진다.
- 여러 데코레이터를 조합하면 새로운 서브클래스 작성없이도 새로운 기능을 추가할 수 있다.
- 하나의 데코레이터에 하나의 책임만을 부여하며 여러 데코레이터를 만들면 SRP를 준수하는 좋은 설계가 가능해진다.

## 문제점은? Cons
- 데코레이터 패턴은 스택 구조로 책임을 덧붙이게 되는데, 이때 추가될 책임이 순차적인 특성을 지니지 않는다면 구현이 까다로워진다.
- 데코레이터를 사용하는 `Client` 측에서 최초 초기화시 데코레이터들을 조합하는 설정코드가 가독성이 떨어진다.
- 같은 이유로 데코레이터를 조합하는 설정코드 변경이 필요할때, 코드 분석이 까다롭다.

# 구현 코드

## 시나리오
- 간단한 댓글 기능 수도 코드를 통해 데코레이터 패턴을 알아보자
## Ver.01
 
### Controller   
   
    public class CommentController {
    
        private CommentService commentService;
    
        public CommentController(CommentService commentService) {
            this.commentService = commentService;
        }
    
        public void doComment(String comment){
            commentService.doService(comment);
        }
    }

### CommentService
    
    public class CommentService {
    
        public void doService(String comment){
            System.out.println("comment = " + comment);
        }
    }
    

### Client


    public class Client {
    
        public static void main(String[] args) {
            CommentController commentController = new CommentController(new CommentService());
            commentController.doComment("HELLO WOLRD");
        }
    }
   
### 설명

현재는 단순히 입력한 댓글을 그대로 반환하는 기능이므로 별다른 고도화가 필요없다.

## Ver.02 - Inheritance

### 시나리오

- 요구사항 1.
    A유저 그룹 댓글에는 소문자로만 댓글이 나오게 해달라
- 요구사항 2.
   B유저 그룹 댓글에는 마지막에 `*****`을 붙여달라
- 요구사항 3.
   C유저 그룹 댓글에는 소문자로 나오고 `*****`도 붙여달라  

만약 위 시나리오를 상속을 이용해 해결한다면 다음과같은 코드가 나올것이다.
 

### CommentService의 Sub Class


    public class CommentLowerCaseService extends CommentService {
        @Override    
        public void doService(String comment){
            System.out.println("comment = " + comment.toLowerCase());
        }
    }



    public class CommentEditStarService extends CommentService {
        @Override    
        public void doService(String comment){
            System.out.println("comment = " + comment + "******");
        }
    }
    

    public class CommentComplexService extends CommentService{
        @Override
        public void doService(String comment) {
            comment = comment.toLowerCase() + "***";
            super.doService(comment);
        }
    }



### Client
    
    public class Client {
        public static void main(String[] args) {
            CommentController commentController = new CommentController(new CommentLowerCaseService());
            commentController.doComment("HELLO WORLD");
    
            CommentController commentController2 = new CommentController(new CommentEditStarService());
            commentController2.doComment("HELLO WORLD");
    
            // 만약 두가지 기능을 다 섞고 싶거나, 동적으로 두가지 기능중 하나를 선택하는등 유연하게 변경하고싶다면 ? 상속받은 새로운 객체 만들기
            CommentController commentController3 = new CommentController(new CommentComplexService());
            commentController3.doComment("HELLO WORLD");
        }
    }
    
### 설명

A 그룹 유저를 위한 `CommentLowerCaseService()`
B 그룹 유저를 위한 `CommentEditStarService()`
C 그룹 유저를 위한 `CommentComplexService()`

각각의 상황에 맞는 `Sub Class`를 작성해 대응할 수 있다.

하지만 만약 위와같은 요구사항이 하나 더 증가하여 댓글색깔을 빨강색으로 바꾸는 서비스가 추가된다고 생각해보자.

그럼 소문자, 별달기, 색깔 바꾸기 3가지 서비스를 조합한 총 4가지 경우의 수가 더 추가되므로

`Sub Class`를 총 7개 작성해야한다.

여기서 요구사항이 더 늘어난다면? 그때마다 모든 상황에 대처할 새로운 클래스를 작성할것인가?

`OOP`의 시각에서 `OCP`와 `SRP` 를 준수하여 상속을 통해 다형성을 사용하는 설계를 했지만, 오히려 유연한 확장과는 거리가 멀어진거 같다.

이때 Decorator 패턴을 사용하면 훨씬 유연한 확장이 가능해진다.

## Ver.03 - Composition

### 최상위 인터페이스 정의

    public interface CommentUseCase {
        void doService(String comment);
    }
    
### default 구현체
 
    public class CommentService implements CommentUseCase {
    
        @Override
        public void doService(String comment){
            System.out.println("comment = " + comment);
        }
    }
    
### Abstract Decorator

    public abstract class CommentDecorator implements CommentUseCase{
        private CommentUseCase commentUseCase;
    
        public CommentDecorator(CommentUseCase commentUseCase) {
            this.commentUseCase = commentUseCase;
        }
    
        @Override
        public void doService(String comment) {
            commentUseCase.doService(comment);
        }
    }
    
### Decorator Impls

    public class CommentEditStarDecorator extends CommentDecorator {
    
        public CommentEditStarDecorator(CommentUseCase commentUseCase) {
            super(commentUseCase);
        }
    
        @Override
        public void doService(String comment) {
            comment = comment + "***";
            super.doService(comment);
        }
    }
    

    public class CommentLowerCaseDecorator extends CommentDecorator {
    
        public CommentLowerCaseDecorator(CommentUseCase commentUseCase) {
            super(commentUseCase);
        }
    
        @Override
        public void doService(String comment) {
            comment = comment.toLowerCase();
            super.doService(comment);
        }
    }
    
### Client

    public class Client {
        public static void main(String[] args) {
    
            boolean youWantLowerCase = true;
            boolean youWantEditStar = true;
    
            CommentUseCase commentService = new CommentService();
            if(youWantLowerCase) {
                commentService = new CommentLowerCaseDecorator(commentService);
            }
            if (youWantEditStar) {
                commentService = new CommentEditStarDecorator(commentService);
            }
    
            commentService.doService("HELLO WORLD");
    
        }
    }
    
### 설명

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/904db566-609f-4258-8ac8-5df9efcf7f7b.png)

그림과 같이 최상단 `UseCase` 인터페이스와 기본 구현체, 그리고 Decorator 인터페이스와 그 구현체들을 구현했고

`Client`에서 데코레이터들을 조합하여 책임을 덧붙였다.

당장에 지금상황에는 보일러플레이트 코드가 더 증가했지만, 

요구사항이 증가할 경우 데코레이터 구현체 하나만 추가하면 데코레이터를 조합하는 경우의 수는 

Client단에서 if/else 분기를 통해 대응가능해지므로, 추가되는 코드양이 훨씬 줄어들고 빠른 개발이 가능해진다.

ver.02에서 예시를 든 시나리오대로라면, 하나의 요구사항이 추가만되도 총 7개의 구현체가 필요했던때와는 달리

composition을 사용한다면 decorator 구현체 3개만으로 대응 가능하다.

# 다른 패턴과의 관계
- Adapter 패턴은 대상 객체의 인터페이스를 바꾸지만, 데코레이터 패턴은 인터페이스는 그대로 유지하면서 책임을 덧붙인다.
- 책임 연쇄 패턴과 데코레이터 패턴은 그 구조가 매우 비슷하지만, 책임을 수행하는 특정 시점에서 체인을 끊어 의도적으로 멈추는게 가능하다는 점에서 데코레이터 패턴과 차이를 갖는다.
- 컴포지트 패턴이나 데코레이터패턴으로 생성된 객체를 재생성할 경우 프로토타입 패턴을 사용하면 코드가 훨씬 간결해질수 있다.
- 데코레이터 패턴과 전략 패턴 모두 `Composition`을  사용하지만, 데코레이터패턴은 외부를 감싸는 래핑객체를 늘려나가고, 전략패턴은 내부의 전략을 변경하는점에서 차이를 보인다.
- 프록시 패턴과 데코레이터 패턴은 그 구조가 매우 유사하며, 둘다 `Compositon`을 사용한다. 책임을 덧붙이는경우 엄밀히 말하면 데코레이터로 분류하는것이 맞으나, 일반적으로 프록시패턴에서도 혼용되어 사용되는만큼 둘의 차이를 구분짓기는 쉽지않다. 둘의 가장 큰 차이점은, 프록시패턴은 그 객체의 생명주기를 스스로 책임지나, 데코레이터 패턴은 생명주기를 `Client`가 제어한다는 점이다.

# [학습예제 코드는 깃헙에](https://github.com/jinia91/DesignPattern/tree/main/src/decorator)

# References
- [https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0_%ED%8C%A8%ED%84%B4)
- [https://devjino.tistory.com/19](https://devjino.tistory.com/19)
- [https://refactoring.guru/design-patterns/flyweight](https://refactoring.guru/design-patterns/decorator)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)