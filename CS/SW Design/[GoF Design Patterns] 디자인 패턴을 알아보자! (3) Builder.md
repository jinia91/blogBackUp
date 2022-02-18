# Builder란?
## 빌더 패턴 정의, Intend
![image](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Builder_UML_class_diagram.svg/700px-Builder_UML_class_diagram.svg.png)

> 복잡한 객체의 생성 과정과 표현 방법을 분리하여, 같은 생성 과정을 거쳐도 서로 다른 표현 결과를 만들수 있게 해주는 패턴이다.

## 왜 필요한가?  Pros
- 복잡한 객체생성시 구체적인 과정을 캡슐화가 가능하다.(못생긴 생성자 감추기 가능)
- 동일한 프로세스로 각기 다르게 구성된 객체 생성 가능하다.
- **클라이언트가 불완전한 객체를 사용하는 것을 방지할 수 있다.**
- 구조에 따라선 복잡한 객체를 만드는 순서를 강제할 수도 있다.

## 문제점은? Cons
- 생성자로 객체 생성하는것에 비해 구조가 훨씬 복잡해진다.
- Builder 객체, Director 객체등을 생성하는 비용이 든다.

# 구현방법

## Simple Builder 패턴

### product
      // 인스턴스를 생성하고자 하는 클래스, 생성자를 private으로 막아뒀다.
     public class Article {
    
        private Long id;
        private String title;
        private String content;
        private Long hit;
    
        private Article() {
        }
    
        private Article(Long id, String title, String content, Long hit) {
            this.id = id;
            this.title = title;
            this.content = content;
            this.hit = hit;
        }
        
       // 내부 static Builder 클래스 생성 static 팩터리 메서드 
       public static ArticleBuilder builder() {
            return new ArticleBuilder();
        }

### static inner Builder    
        // 내부 static Builder 클래스
        public static class ArticleBuilder {
            private Long id;
            private String title;
            private String content;
            private Long hit;
    
            private ArticleBuilder() {
            }
    
            public ArticleBuilder id(Long id) {
                this.id = id;
                return this;
            }
    
            public ArticleBuilder title(String title) {
                this.title = title;
                return this;
            }
    
            public ArticleBuilder content(String content) {
                this.content = content;
                return this;
            }
    
            public ArticleBuilder hit(Long hit) {
                this.hit = hit;
                return this;
            }
    
            // 조립된 Article 최종 반환 메서드
            public Article build(){
                return new Article(id,title, content, hit);
            }
        }


### 설명
생성코자 하는 클래스의 내부에 static inner class로 Builder를 정의한 패턴.

1. static 팩토리 메서드로 Builder를 만들고 
2. Builder에서 필요로 하는 필드를 주입한다음 
3. 최종적으로 목표하는 클래스의 인스턴스를 반환한다.

일반적으로 자주보는 빌더 패턴이며, 우리가 흔히 사용하는 롬복 라이브러리의 `@Builder`도 위와같은 바이트코드가 작성되는것으로 알고있다.



**클라이언트 코드**

       //  private으로 막아놓았으므로 생성 불가
            //  Article article = new Article();
            //  article.setId(1L);
            //  article.setContent("빌더패턴");
            //  article.setHit(1L);
            //  article.setTitle("빌더패턴제목");
    
            Article article = Article
                    .builder()
                    .id(1L)
                    .content("빌더패턴")
                    .hit(1L)
                    .title("빌더패턴제목")
                    .build();

   ## Builder - Director 패턴
   
![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/50a5797d-2c4d-4e28-b64b-494d660f8f66.png?raw=true)

  
### Builder  
    // 빌더 인터페이스
    public interface ArticleBuilder {
    
        ArticleBuilder id(Long id);
    
        ArticleBuilder title(String title);
    
        ArticleBuilder content(String content);
    
        ArticleBuilder hit(Long hit);
    
        Article build();
    }
    
    // 빌더 구현체
    public class DefaultArticleBuilder implements ArticleBuilder {
    
        private Long id;
        private String title;
        private String content;
        private Long hit;
    
        public ArticleBuilder id(Long id) {
            this.id = id;
            return this;
        }
    
        public ArticleBuilder title(String title) {
            this.title = title;
            return this;
        }
    
        public ArticleBuilder content(String content) {
            this.content = content;
            return this;
        }
    
        public ArticleBuilder hit(Long hit) {
            this.hit = hit;
            return this;
        }
    
        public Article build(){
            return new Article(id,title, content, hit);
        }
    }
    

### Director
    //Director 클래스
    public class ArticleDirector {
    
        private ArticleBuilder articleBuilder;
    
        public ArticleDirector(ArticleBuilder articleBuilder) {
            this.articleBuilder = articleBuilder;
        }
    
        public Article sampleArticle() {
            return  articleBuilder
                            .id(1L)
                            .content("빌더패턴")
                            .hit(1L)
                            .title("빌더패턴제목")
                            .build();
        }
    }
    



    
### 설명

> 빌더 클래스가 외부로 노출되거나, 인터페이스로 분리한것은 설계의 방식일뿐 위의 static inner 클래스와 차이 없음!

동일한 프로세스를 거쳐 반복적으로 같은 객체를 만드는 경우 빌더 클래스를 한번더 감싸고 객체를 만드는 로직을 캡슐화하는 `Director` 패턴을 고려해볼 수 있다.

`Director` 객체 하나를 더 생성해야하는 비용이 존재하지만, 유지 보수하기엔 더 좋은 코드 결과물을 얻을 수 있다.

  
**클라이언트 코드**

    ArticleDirector articleDirector = new ArticleDirector(new DefaultArticleBuilder());
        Article article = articleDirector.sampleArticle();
  

# 다른 패턴과의 관계
- 빌더 패턴은 복잡한 객체를 순서대로 만드는 과정을 추가한다는데 의도가 있지만 , 추상 팩토리는 연관된 객체 집합을 생성하는데 특화되어있다. 또한 추상팩토리는 한번에 객체를 반환하지만, 빌더는 최종적으로 빌드하기전까지 요소들을 하나하나 조립하는차이점이 존재한다.
- `Director` 클래스를 사용하는 구현 패턴은 `Bridge` 패턴으로도 해석할 수 있다.
- 빌더는 싱글턴으로 구현 가능하다.

# 실무에서 어떻게 사용되는지? 실제 사용된 사례

- java.lang.StringBuilder
- java.lang.StringBuffer
- [롬복 @Builder](https://projectlombok.org/features/Builder)
- 스프링, MockMvcWebClientBuilder
    
# References

- [https://refactoring.guru/design-patterns/builder](https://refactoring.guru/design-patterns/builder)
- [위키백과 - 빌더 패턴](https://ko.wikipedia.org/wiki/%EB%B9%8C%EB%8D%94_%ED%8C%A8%ED%84%B4)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
