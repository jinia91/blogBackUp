# Composite 란?
## 컴포지트 패턴의 정의, Intend
> 컴포지트 패턴은 객체들의 관계를 트리구조로 표현하는 패턴으로, 사용자가 단일 객체와 복합 객체를 모두 동일하게 다룰수 있도록 구조화한 패턴이다.

![image](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Composite_UML_class_diagram_%28fixed%29.svg/480px-Composite_UML_class_diagram_%28fixed%29.svg.png)

### Composition?

컴포지션은 패턴이라기보다는 설계 개념, 컨셉으로서 하나의 객체가 다른 객체를 품고있는 경우를 일반적으로 말한다.

여기서 조금 더 나아가, 컴포지션의 `가장 밖에 위치한 객체` = Wrapper 객체는 내부에 품고있는 객체와 생명주기가 강결합되어있는편이다.

### What is `Composition over Inheritance`

자바나 OOP에 대해 공부하다보면 어느순간 상속보다는 결합(`composition`)을 사용하라는 말을 듣게된다. 나 역시  이펙티브 자바, Object 등을 공부하면서 해당 구문을 읽었고, 이번에 컴포지트 패턴을 공부하면서도 듣게 되었다.

해당 내용은 다형성의 구현에 있어서 상속은 부모클래스가 구현하는 내용을 자식 클래스가 모두 물려받다보니, 하위클래스에서 정작 쓰이지 않는 메서드도 갖게되고, 부모클래스의 변화에 따라 자식클래스도 크게 영향을 받게되므로

캡슐화가 깨지게 된다는 주장이였다.

따라서 `is-a`관계인경우에만 상속을 사용하고, 보통은 객체간 결합을 사용해야하며 많은 공감을 받아 현재 OOP 진형의 대표적인 설계기법으로 자리 잡고 있다.

### Composition vs Composite pattern?

인터넷의 여러 글을 읽어보고 의문점이 들었는데 컴포지트 패턴은 명확하게 의도가 

> 복잡하게 얽혀있는 데이터 구조를 트리 구조로 하이라이키하게 계층화시키고, 이러한 구조의 노드와 리프들을 모두 같은 인터페이스로 접근하도록 하려는 패턴

인거지, 상속의 한계와 그 해결책으로 나온 패턴이 아니라는 점이다.

이펙티브 자바나 Object책에서 소개된 `Composition`으로서의 패턴도 `Decorator`패턴이지, 컴포지트 패턴이랑은 거리가 멀었다.

몇몇 블로그글도 그렇고 나 역시도 컴포지트 패턴이 `컴포지션-상속 논쟁`의 컴포지션으로 생각했었는데

컴포지트 패턴이 컴포지션의 구조인건 맞지만 그 의도가 위의 내용이랑은 전혀 관계가 없다는걸 깨달았다.

아마도 명칭의 유사성때문에 헤깔리지 않았나 싶은데 이참에 개념을 다시 잡았다.

> 요약하자면 컴포지트 패턴은 `Compositon over Inheritance` 와는 전혀 관련이 없다!

혹시 이견이 있다면 꼭 댓글로 달아주시길

## 왜 필요한가? Pros

- 데이터를 다루다보면 계층형 트리 자료구조로 저장되고 이를 다루게되는 경우가 종종 생긴다.
- 이때 composite패턴을 사용하면 클라이언트측에서 모든 데이터를 모르더라도 복잡한 트리구조를 쉽게 다룰수 있다.
- 새로운 `leaf`로서의 클래스를 추가하더라도 클라이언트는 상위 추상화된 인터페이스만을 바라보기 때문에, `OCP`를 준수할 수 있다.

## 문제점은? Cons

- 계층형 구조에서 `leaf`에 해당하는 객체와 `Node`에 해당하는 객체들 모두를 동일한 인터페이스로 다루어야하는데, 이 인터페이스 설계가 어려울 수 있다. 이럴때는 디자인패턴에 억지로 끼워맞추려는것은 아닌지 다시 생각해볼필요가 있다.

# 구현 코드

    public interface Document {
        void delete();
    }
    
    public class Folder implements Document {
    
        private List<Document> contents = new ArrayList<>();
    
        @Override
        public void delete() {
            for (Document content : contents) {
                content.delete();
            }
        }
    
        public void addContents(Document content){
            contents.add(content);
        }
    }
    
    public class File implements Document {
        private String contents;
    
        public File(String contents) {
            this.contents = contents;
        }
    
        @Override
        public void delete() {
            System.out.println("delete contents : " + contents);
        }
    }
    

## 설명

최상위 Document 인터페이스가 있고 이를 상속받는 폴더와 파일이 있는 단순한 자료구조를 구현했다.

폴더에는 파일이나 폴더가 담길수 있기때문에 트리구조로 자료가 담기게 되는데, 이때 최상위 root 폴더에 `delete()`메서드만 호출하면 재귀적으로 모든 파일들이 다 `delete()` 메서드를 호출하게되는 구조다.



       private static Document buildDocu() {

        Folder root = new Folder();
        Folder subNode1 = new Folder();
        Folder subNode2 = new Folder();
        root.addContents(subNode1);
        root.addContents(subNode2);
        subNode1.addContents(new File("컨텐츠1"));
        subNode1.addContents(new File("컨텐츠2"));
        subNode1.addContents(new File("컨텐츠3"));
        subNode2.addContents(new File("컨텐츠4"));

        Folder subNode3 = new Folder();
        subNode2.addContents(subNode3);
        subNode3.addContents(new File("컨텐츠5"));

        return root;
    }


위와같은 파일이 주어졌을때 클라이언트 측에서 아래처럼 호출하면

       public static void main(String[] args) {
            Document document = buildDocu();
            document.delete();
        }
    

    //delete contents : 컨텐츠1
    //delete contents : 컨텐츠2
    //delete contents : 컨텐츠3
    //delete contents : 컨텐츠4
    //delete contents : 컨텐츠5
 
트리구조를 손쉽게 다룰수 있게된다.

# 다른패턴과의 관계

- 재귀적인 구조를 만들때 Builder패턴을 사용하면 훨씬 가독성이 좋아진다
- iterator 패턴이나 visitor 패턴을 composite 패턴 트리에 작동되도록 할수도 있다
- leaf노드에 경량패턴을 적용하여 램사용을 줄일수있다.
- decorator 패턴과 유사한 구조를 보이는데, decorator 패턴은 래퍼 객체가 추가적인 책임을 갖는다는 점에서 큰 차이가 있다.


# [학습예제 코드는 깃허브에](https://github.com/jinia91/DesignPattern/tree/main/src/composite)
   
   # References
- [https://ryulib.tistory.com/270](https://ryulib.tistory.com/270)
- [https://greenmon.dev/2019/06/29/inheritance-composition.html](https://greenmon.dev/2019/06/29/inheritance-composition.html)
- [https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8F%AC%EC%A7%80%ED%8A%B8_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8F%AC%EC%A7%80%ED%8A%B8_%ED%8C%A8%ED%84%B4)
- [https://stackoverflow.com/questions/9071067/design-patterns-composite-vs-composition](https://stackoverflow.com/questions/9071067/design-patterns-composite-vs-composition)
- [https://refactoring.guru/design-patterns/composite](https://refactoring.guru/design-patterns/composite)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
