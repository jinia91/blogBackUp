# Chain Of Responsibility 란?

>  책임 연쇄 패턴(Chain Of Responsibility pattern)이란 클라이언트의 요청을 처리하는 객체의 집합(`chain`)을 만드는 패턴을 의미한다. 요청이 시작되면, 연쇄연결된 객체들은 자신의 순서일때 요청을 다루거나, 혹은 다음으로 연결된 객체에게 요청을 전달하는 일을 한다.

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/ad7dbe82-465a-4880-ac2c-891f238f04f2.png)

## 왜 필요한가? Pros
- 요청을 처리할 큰 책임을 작은 책임으로 분할하여 객체를 만드는 만큼 SRP를 준수하게된다.
- 작은 객체들의 순서를 정할 수 있다.
- 다른 책임을 가진 객체를 추가하더라도 순서를 컨트롤하는 부분만 수정하면 되므로, OCP를 준수할 수 있다.
- 객체를 책임에 따라 분할함에 따라 결합도를 낮출 수 있으며 보다 유연한 확장이 가능해진다


## 문제점은? Cons

- 체인을 연결하는 코드의 가독성이 문제될 수 있으며, 체인이 잘못연결되면 순환참조, 사이클이 발생할 수 있다.
- 디버깅이 더 어려워진다.

# 구현 코드
> 게시물을 등록하는 수도코드를 통해 간단히 책임 연쇄패턴을 살펴보자

## ver 0. 절차적 프로그래밍으로 구현

### ArticleController
    public class ArticleController {
        public ArticleController() {
        }
    
        public void postArticle(ArticlePostRequest request){
            System.out.println(request.getTitle() + "글을 업로드했습니다.");
        }
    }


### Client

    public class Client {
        public static void main(String[] args) {
            ArticleController controller = new ArticleController();
            ArticlePostRequest request = new ArticlePostRequest(1L, "access", "title", "content");
            if(request.getAccessToken() == null){
                throw new IllegalArgumentException("인증 통과 실패");
            }
            System.out.println("request.getMemberId() = " + request.getMemberId());
            System.out.println("request.getTitle() = " + request.getTitle());
            System.out.println("request.getContent() = " + request.getContent());
            controller.postArticle(request);
        }
    }
    
### 설명

요청이 들어오면 인증, 로깅(logging), 그리고 게시물을 등록하는 수도코드 크게 3가지 책임이 존재할때, 이를 클라이언트에서 순차적으로 처리하는 절차적 프로그래밍 코드이다.

위와같은 코드는 `client`가 너무 많은 책임을 지고 있으며, 구체적인 구현을 모두 한곳에 몰아넣었기때문에 `SRP`에 위배되고

수정사항이있을때, 코드 전체를 다 파악하고 고쳐야하는만큼 유지관리가 힘들며 확장에 닫혀있다.(`OCP`)
    
## ver 01. 1차적인 책임 분할 

### AuethFilter

    public class AuthFilter {
    
        public void filtering(ArticlePostRequest request){
            if(request.getAccessToken() == null){
                throw new IllegalArgumentException("인증 통과 실패");
            }
        }
    }
    
### LogFilter
    public class LogFilter {
    
        public void filtering(ArticlePostRequest request){
            System.out.println("request.getMemberId() = " + request.getMemberId());
            System.out.println("request.getTitle() = " + request.getTitle());
            System.out.println("request.getContent() = " + request.getContent());
        }
    }
    
### ArticleController

    public class ArticleController {
        public ArticleController() {
        }
    
        public void postArticle(ArticlePostRequest request){
            System.out.println(request.getTitle() + "글을 업로드했습니다.");
        }
    }

### Client

    public class Client {
        public static void main(String[] args) {
            // build App
            LogFilter logFilter = new LogFilter();
            AuthFilter authFilter = new AuthFilter();
            ArticleController controller = new ArticleController();
    
            // set Request
            ArticlePostRequest request = new ArticlePostRequest(1L, "access", "title", "content");
    
            // do
            authFilter.filtering(request);
            logFilter.filtering(request);
            controller.postArticle(request);
        }
    } 
    
### 설명

클라이언트단에 존재하던 로깅과 인증 책임을 각각의 필터로 분할한 구조다.

하지만 여전히 클라이언트에게 여러 책임이 몰려있으므로 더 고도화를 해보자

## ver 2. 책임연쇄패턴 적용

### Filter(ChanHandler)

    public abstract class Filter {
    
        private Filter nextFilter;
    
        public Filter(Filter nextFilter) {
            this.nextFilter = nextFilter;
        }
    
        void filtering(ArticlePostRequest request){
            if (nextFilter != null){
                nextFilter.filtering(request);
            }
        }
    
    }
    
### Filter 구현체들

    public class StartFilter extends Filter {
    
        public StartFilter(Filter nextFilter) {
            super(nextFilter);
        }
    
        @Override
        public void filtering(ArticlePostRequest request){
             super.filtering(request);
        }
    }
    
    public class LogFilter extends Filter {
    
        public LogFilter(Filter nextFilter) {
            super(nextFilter);
        }
    
        @Override
        public void filtering(ArticlePostRequest request){
            System.out.println("request.getMemberId() = " + request.getMemberId());
            System.out.println("request.getTitle() = " + request.getTitle());
            System.out.println("request.getContent() = " + request.getContent());
            super.filtering(request);
        }
    }
    
    public class AuthFilter extends Filter {
    
        public AuthFilter(Filter nextFilter) {
            super(nextFilter);
        }
    
        @Override
        public void filtering(ArticlePostRequest request){
            if(request.getAccessToken() == null){
                throw new IllegalArgumentException("인증 통과 실패");
            }
            System.out.println("검증 통과");
            super.filtering(request);
        }
    }
 
### Client

    public class Client {
        public static void main(String[] args) {
            // build App
            StartFilter filterChain = new StartFilter(new LogFilter(new AuthFilter(null)));
            ArticleController controller = new ArticleController();
    
            // set Request
            ArticlePostRequest request = new ArticlePostRequest(1L, "access", "title", "content");
    
            // do
            filterChain.filtering(request);
            controller.postArticle(request);
        }
    }
    
### 설명

각각의 필터들을 추상화하고 체인을 연결시키는 메서드와 참조를 추가하였다. 이제 더이상 `Client`는 필터들간의 순서를 알필요가 없어지므로 결합도가 낮아지고 SRP와 OCP에 보다 적합한 코드가 되었다.

여기서 더 고도화를 한다면, 객체를 생성하는 `Build App` 코드를 `Abastract Factory` 패턴으로 분리하고 요쳥역시 `Factory method` 패턴으로 분리할 수 있을것이다.


# 다른 패턴과의 관계

- 책임연쇄패턴, 커맨드패턴, 중재자패턴, 옵저버 패턴은 모두 요청에 대한 수신자와 송신자간 다양한 결합에 대한 패턴들이다.
    - 책임연쇄패턴은 동적으로 연결된 체인에 요청을 순차적으로 전달하는 패턴이다.
    - 커맨드 패턴은 수신자와 송신자를 오직 단방향으로만 연결하는 패턴이다.
    - 중재자 패턴은 송신자와 수신자간 방향성을 제거하고 중재자 객체가 그 사이에서 소통을 관장한다.
    - 옵저버 패턴은 수신자가 동적으로 구독/구독해제를 통해 메시지 수신을 결정할 수 있게한다.

- 책임연쇄패턴은 종종 Composite 패턴과 함께 사용될 수 있다.
- 책임연쇄패턴을 커맨드 패턴으로도 구현가능하다. 
- 책임연쇄패턴은 데코레이터 패턴과 그 구조가 매우 유사하지만, 요청에 대한 흐름을 끊을수 있는 책임연쇄패턴에 비해, 데코레이터 패턴은 모든 책임을 다 실행한다는점에서 차이를 보인다.



# [학습예제 코드는 깃헙에](https://github.com/jinia91/DesignPattern/tree/main/src/chain_of_responsibility)

# References
- [https://refactoring.guru/design-patterns/chain-of-responsibility](https://refactoring.guru/design-patterns/chain-of-responsibility)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)