## 페이지네이션(Pagination)

- 한정된 네트워크 자원을 효율적으로 활용하기 위하여 특정 정렬 기준에 따라 데이터를 분할하여 가져오는 방식

- 데이터 베이스에서 조회하려는 데이터의 양이 일정 이상일 때 이를 쪼개어 부분만 조회함으로써 네트워크의 부담을 줄이고, 빠른 응답을 기대할 수 있다.

- 페이지네이션의 방식으로는 크게 오프셋 방식과 커서방식 두가지로 나뉜다.

## 오프셋(Offset-based Pagination)

### 개 요

전통적인 페이징 방식. 

조회할 전체 데이터를 정렬한 후 LIMIT으로 내가 조회하고 싶은 만큼의 데이터 양을 정하고 OFFSET으로 SKIP할 데이터 양을 정한다.

    // MY-SQL 오프셋 페이징 쿼리 예시 1(OFFSET , LIMIT 순서)
    
    SELECT * 
    FROM BOARD 
    ORDER BY id DESC 
    LIMIT 40, 10 
    
    // MY-SQL 오프셋 페이징 쿼리 예시 2(LIMIT OFFSET 순서)
    
    SELECT * 
    FROM BOARD 
    ORDER BY id DESC 
    LIMIT 10
    OFFSET 40
    
    // ORACLE 오프셋 페이징 쿼리 예시(12버전 이상)
    SELECT *
    FROM BOARD
    ORDER BY id DESC
    OFFSET 40 ROWS FETCH NEXT 10 ROWS ONLY;


만약 실제로 마이바티스나 JDBC Template을 사용해 페이징을 구현한다고 생각해보자.

우선은 우리가 원하는 페이지를 **페이지 번호**로 클라이언트 단에서 요청하게 될 것이므로, 한번에 화면에 보여줄 LIMIT 기준과 **요청된 페이지에 따라 동적으로 바뀌게 될 OFFSET을 계산**해줄 클래스가 필요하다.

[요청된 페이지에 따라 오프셋을 계산 해주는 페이징 핸들러 클래스 토이프로젝트 예제](https://github.com/jinia91/SpringbootMvcBoard/blob/master/board/src/main/java/com/project/board/board/handler/PagingHandler.java)


여기에 추가로 페이징처리된 데이터들을 클라이언트단에서  우리가 흔히 볼 수 있는 페이지 박스로 렌더링하려는 경우 위의 페이징 핸들러 클래스를 확장하거나, 데코레이터 패턴으로 감싸 렌더링 기능을 추가하는 경우도 있다.

![페이지네이션](https://miro.medium.com/max/1200/1*7_wE6ZSicZBTGh5qXh_QkQ.png)

[우리가 흔히 보는 페이징 박스를 그리기 위해 추가 로직이 필요]

[페이징 박스 렌더링을 위해 데코레이터 패턴으로 만든 페이징박스 핸들러 클래스 토이프로젝트 예제](https://github.com/jinia91/SpringbootMvcBoard/blob/master/board/src/main/java/com/project/board/board/handler/PagingBoxHandler.java)


### SPRING-DATA-JPA Page, Slice 인터페이스

사실 페이징 박스를 렌더링하는건 뷰에서 어떻게 보여질지 정책에 따라 로직이 각양각색으로 다양할 수 있고, 이를 위한 프론트 라이브러리(제이쿼리 등)도 존재한다.

이와 마찬가지로 페이징 처리도 SPRING DATA에선 `org.springframework.data.domain.Page` 와 `org.springframework.data.domain.Slice` 라는 인터페이스로 추상화 시켜 놓았다.

Slice는 Iterable을 구현하고 List와 페이지처리에 쓸만한 다양한 멤버변수, 메서드들을 명시한 인터페이스이고, 

Page는 Slice 를 확장한 인터페이스로 사용시에는 JPA 내부에서 페이징 처리를 하려하는 데이터들의 총 갯수(total count) 쿼리를 하나 더 날리게되며, 이를 활용하는 getTotalPages(), getTotalElements() 메서드 등을 사용할 수 있다.

따라서 페이징 박스를 렌더링하는 정책에 따라 Page나 Slice중 하나를 골라 사용하면 된다.


[Spring Data JPA 공식 레퍼런스](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)


Spring Data JPA에서 제공하는 `org.springframework.data.domain.Pageable` 인터페이스나 `org.springframework.data.domain.Sort` 클래스를 패러미터로 사용하면 JPA상에서 굉장히 쉽게 페이징처리가 가능해진다.
 
  
      // JPA의 메서드 쿼리, Slice와 Pageable을 사용하면 아래 한줄로 기본적인 페이징이 완료된다. 페이징 핸들러 클래스를 작성할 필요도 없다!
      
      Slice<Board> findByOrderByIdDesc(Pageable pageable);


>Page와 Slice는 내부에서 **오프셋 페이징방식**으로 구현되며 위의 메서드는 앞의 쿼리와 완전히 동일한 limit offset 쿼리로 변환되어 DB로 날라가고 Slice로 바인딩하여 반환된다.


## 오프셋 페이징의 단점
오프셋 페이징은 스프링 DATA JPA에서도 공식적으로 인터페이스화하여 사용할만큼 페이징 처리의 정석으로 여겨지지만 몇가지 단점이 존재한다.

#### OFFSET 의 구동방식과 성능문제
OFFSET은 DB가 정렬된 데이터들을 SKIP하는 쿼리인데 문제는 **DB가 SKIP하기 위해서 OFFSET 개수만큼** 수를 센다는 것이다.

즉 `LIMIT 40, 10 `는 '데이터들에서 40개를 SKIP하고 그다음 10개를 조회해~' 이므로 40개를 DB가 세는 연산이 들어간다. 

40, 80. 100, 1000정도라면 큰 체감이 들지 않겠지만 만약 `LIMIT 2000000, 10 ` 200만건이라면? 2000만건 이라면? 페이지가 더욱 더 뒤로 갈수록 숫자를 세는 연산은 비례해서 증가하게 된다.

![정비례](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSoXDxpBycTu10W6KytvF0QNpt-8iZ_tLpc2Ke1PZrmKkCyd67ojSJUhpLKGtRO9e2jyv0&usqp=CAU)
[SKIP하는 페이지수만큼 쿼리 처리 시간도 증가한다]

실제로 토이프로젝트로 더미데이터 200만건을 넣고 로컬환경에서 테스트를 해보니 첫페이지를 조회할때는 0.016초의 처리시간이 걸렸지만 최종페이지를 조회할때는 0.83초의 처리시간이 걸렸다.

#### 데이터 변경에 따른 누락 / 중복
오프셋 페이징은 사용자 경험에도 문제가 있다. 

만약 특정 사용자가 4페이지를 보고 있는 도중 이전 페이지에 해당하는 데이터가 세개 삭제되었다고 가정해보자.

사용자는 자연스럽게 5페이지를 요청하게 될 것이고 내가 보고있던 4페이지 내용 바로 직후의 데이터 LIMIT개수를 보길 기대하지만

이전에 보았던 데이터중 3개가 삭제된 까닭에 OFFSET 범위에서 내가 보지않은 데이터 3개가 포함되어 SKIP되게 된다.

결국 다시 이전 4페이지로 돌아가지 않는이상 사용자는 누락된 3개의 데이터를 볼수 없게되는 것이다.

반대 케이스도 마찬가지다.

 데이터가 추가된다면 OFFSET 범위에서 추가된 데이터양만큼 SKIP 범위가 밀려 다음페이지에 중복된 데이터들을 보게될 것이다.

## 커서페이징
커서 페이징은 오프셋 페이징의 단점을 깔끔하게 해결해준다.

커서페이징이란 데이터를 조회할때 내가 현재 조회한 위치를 쿼리로 가리키는 커서 구문을 집어넣는 방식으로 쿼리로 표현하면 다음과같다.

    SELECT * 
    FROM BOARDS 
    WHERE ID < {이전에 조회한 마지막 id}  //<- 커서
    ORDER BY ID DESC LIMIT 10;          // <- OFFSET이 없음


단순하지 않은가?

커서페이징의 강점은 DB가 **SKIP할 데이터를 세는 연산을 하지 않고** WHERE 조건절의 기준으로 바로 탐색한다는 점이다.

따라서 아무리 뒤의 데이터를 조회하더라도 처리시간이 크게 늘어나지 않는다.
![커서 VS 오프셋](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbb1mTe%2FbtqMiZ1GkDU%2FKi6tMRhe7DrSwkwLs23KVK%2Fimg.png)

[O(1)시간복잡도를 갖는 커서 페이징]

또한 내가 마지막으로 조회한 마지막 데이터를 기준으로 다음 데이터를 조회하므로 데이터가 새로 추가되거나 삭제되더라도 다음페이지 조회시 데이터가 삭제되거나 누락되는 일이 일어나지 않는다.

### 만능은 아니다

물론 커서페이징도 만능은 아니다.

일단 커서를 사용하기 위해서 정렬된 기준이 필요한데 만약 정렬 기준이 여러개거나 컬럼의 값이 UNIQUE하지 않은 경우 정상적인 작동을 기대하기 어렵다.

또한 조회된 데이터를 기준으로 전후 조회만 가능하기 때문에 페이지 넘버링 구현이 힘들고 여러페이지를 넘기기도 쉽지않다.

즉 **더보기 혹은 무한스크롤** 방식의 페이징 처리에 최적화되어있는 셈이다.

## 프로젝트를 커서페이징으로 리팩토링

기존에 블로그 메인화면에 있는 최신게시물은 SPRING DATA JPA의 Slice 인터페이스를 사용해 오프셋방식의 무한스크롤로 구현하고 있었다.

    
     // 기존 OFFSET 페이징 메서드 쿼리
    Slice<Article> findByOrderByIdDesc(Pageable pageable);

    // 레포지토리 메서드 호출후 Dto로 매핑하는 로직
    public Slice<ArticleDtoForMain> getRecentArticles(int page) {
        return articleRepository
                .findByOrderByIdDesc(PageRequest.of(page, 5))
                .map(article -> modelMapper.map(article,ArticleDtoForMain.class));


이를 무한 스크롤방식으로 리팩토링 하였다.

    /*
       - 커서페이징으로 최신 게시물 가져오기
           - 첫번째 페이지용 쿼리
     */
    @Query("select a " +
            "from Article a " +
            "order by a.id desc")
    List<Article> findByOrderByIdDescWithList(Pageable pageable);

    /*
       - 커서페이징으로 최신 게시물 가져오기
           - 커서 적용
     */
    @Query("select a " +
            "from Article a " +
            "where a.id < :articleId " +
            "order by a.id desc")
    List<Article> findByOrderByIdDesc(@Param("articleId") Long articleId,Pageable pageable);
    
최초에는 커서 없이 limit만 적용해서 쿼리를 날리고 이후부터 마지막 조회 id를 패러미터로 넘겨 쿼리를 날리도록 했다.

QDSL을 사용해 동적쿼리로 만들어 두 쿼리를 하나로 줄일수도 있지만 아직 QDSL 숙련도가 부족하여 JPQL로 작성하였다.

    // 레포지토리를 호출하는 서비스 계층 메서드
    public List<Article> getRecentArticles(Long lastArticleId) {
        return lastArticleId.equals(0L)?
                articleRepository
                        .findByOrderByIdDescWithList(PageRequest.of(0, 5))
                :
                articleRepository
                        .findByOrderByIdDesc(lastArticleId, PageRequest.of(0, 5));

    }
 

전체 코드 변경사항은 아래 링크로 확인할 수 있다.
[깃허브 리팩토링 커밋 history](https://github.com/jinia91/blog/commit/9c3ba4cdbbdf38629566dab4d057f8ad54b0aaaa#diff-25a6634263c1b1f6fc4697a04e2b9904ea4b042a89af59dc93ec1f5d44848a26)


## References
- [https://bbbicb.tistory.com/40](https://bbbicb.tistory.com/40)
- [https://alwayspr.tistory.com/45](https://alwayspr.tistory.com/45)
- [Spring Data JPA DOC](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)

