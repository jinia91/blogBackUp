# 1장 깨끗한 코드
## 나쁜 코드란?

![image](https://media.vlpt.us/images/seob/post/9917d59c-3497-4129-aee4-f7e08c3020c2/r_1205476_UFofK.jpg)

- **성능이 나쁜코드**

    불필요한 연산이 들어가서 개선의 여지가 있는 코드

- **의미가 모호한 코드**
    이해하기 어려운 코드
    네이밍과 그 내용이 다른코드

- **중복된 코드**    
    비슷한 내용인데 중복된 코드들은 버그를 낳는다.

### 나쁜코드가 나쁜 이유 

- 나쁜 코드의 존재는 마치 깨진 유리창 이론처럼 그 옆에 또 다른 나쁜 코드를 만든다.

>깨진 유리창 이론( - 琉璃窓 理論, 영어: broken windows theory)은 미국의 범죄학자인 제임스 윌슨과 조지 켈링이 1982년 3월에 공동 발표한 깨진 유리창(영어: Fixing Broken Windows: you suck Restoring Order and Reducing Crime in Our Communities)이라는 글에 처음으로 소개된 사회 무질서에 관한 이론이다.<br>
깨진 유리창 하나를 방치해 두면, 그 지점을 중심으로 범죄가 확산되기 시작한다는 이론으로, 사소한 무질서를 방치하면 큰 문제로 이어질 가능성이 높다는 의미를 담고 있다.

- 나쁜코드들이 쌓이면 기술부채로 이어지고 이는 생산성의 저하로 연결되며, 기존 코드의 수정을 더욱더 어렵게한다.

![image](https://ichi.pro/assets/images/max/724/0*pR30vSVsZUP0J0kG)

[기술부채와 생산성]

- 나쁜 코드는 종국에 새로운 시스템을 요구한다.
    나쁜코드가 쌓여 기술부채가 한계에 도달하면 새로운 요구사항을 반영하는 유지보수는 불가능해진다. 결국 기존 시스템 기능을 그대로 반영하는 깨끗한 코드의 새로운 시스템을 만들어야하며 이는 현실적으로 매우 어렵다. 

### 나쁜 코드를 짜는 이유

- 일정이 촉박해서
    **하지만** 나쁜코드는 기술부채로 이어지며 장기적으로 생산성을 저하하기때문에 나중에는 오히려 나쁜코드로 인한 일정 낭비가 더 크다!
- 영향 범위가 넓어서
    **하지만** 해당 기능을 건드렸을때 부작용이 크다면 애당초 해당 기능의 책임이 과도한 것은 아닌지? 메서드가 제대로 분리된것은 맞는지? 의존성을 최소로 설계한것은 맞는지? 다시 한번 생각해보자

## 클린 코드란?

**비야네 스트롭스트룹(C++ 창시자)**
>깨끗한 코드는 한 가지를 제대로 한다.


**그래디 부치(객체지향의 대가)**
>깨끗한 코드는 잘 쓴 문장처럼 읽힌다


- **성능이 좋은 코드**
<br>
- **의미가 명확한 코드 = 가독성이 좋은 코드**
<br>
- **중복이 제거된 코드**


# 2장 의미있는 이름

## 의미가 분명한 이름 짓기

만약 판매 상품을 코드로 표현한다고 생각해보자.

Before:

    int a;
    String b;
    
    System.out.println("Item Name : "+b+"\n"+"Item count : " + a);
    
여기서 `int a`와 `String b`가 무슨 의미인지 파악할 수 있는가?

물론 아래 콘솔 출력을 통해 a는 갯수, b는 상품이름이란걸 추론할 수  있지만 `a`, `b`를 통해서는 아무런 정보도 얻을 수 없다.

이처럼 아무런 의미도 드러나지 않는 변수 이름은 쓰레기다!

변수 이름에는 함축성이 있어서는 안되며 정보를 충분히 제공해야 한다.

자동완성과 Idle이 보편화된 현대 개발 환경에서 긴 변수 명은 아무런 문제가 안된다. 

After:

    class SalesItem{
    long itemId;
    int count;
    String name;
    } 
    
    //  사용예시
    SalesItem SelectedItem = salesItemRepository.getItemById(purchaseRequest.getItemId()); 
    System.out.printf("User Requested %s, count = %d",selectedItem.getName(), selectedItem.getCount());
    

 ## 루프 속에 i.j.k 사용하지 않기

- 향상된 for문을 사용할수 있는 상황에서는 최대한 향상된 for문으로 사용하기
   [- 이펙티브 자바 Item 58](https://www.jiniaslog.co.kr/article/view?articleId=108)
- 단순 조회라면 스트림의 forEach를 사용하기 
    - 하지만 요소의 수정이나 삭제등이 동반될 경우 forEach는 바람직하지 않음
    [- 이펙티브 자바 Item 46](https://www.jiniaslog.co.kr/article/view?articleId=106)

- i,j,k 대신 최대한 맥락에 맞는 이름으로 부여
    행열이라면 (row, column), 좌표라면 (x, y) 등등

## 통일성있는 단어 사용

> - Member / Customer / User <br>
> - Service / Manager <br>
> - Repository / Dao

팀에서 네이밍에 대한 협의를 해서 일관된 단어로 작성하자

## 변수명에 타입 넣지 않기
### 헝가리안식 표기법은 이제 그만
- 헝가리안식 표기법
    프로그래밍 언어에서 변수 및 함수의 인자 이름 앞에 데이터 타입을 명시하는 코딩 규칙
     
| 접두 | 의미 |
| --- | --- |
| a |배열  |
| b|  boolean|
| c | 카운터로 사용되는 변수 |
| d| 날짜형 변수 |

80년대 당시에는 IDE가 부실했고 컴파일러가 타입을 점검하지 않았기에 프로그래머에게 타입을 기억할 단서가 필요했다.

하지만 현대에와서 타입을 이름에 인코딩할 이유는 전혀 없다. 파스칼 표기법과 카멜케이스 표기법을 준수해서 변수명을 짓도록하자

### 인터페이스 앞에 I?

- 과거에는 자주 쓰던 표기법
- 하지만 접두어 I는 주의를 흐트리고 과도한 정보를 제공하며 해당 타입이 인터페이스인지 클래스인지 굳이 알려줄 필요가 없음   
- 팀과 협의로 정하도록 하자


## 구글 자바 네이밍 가이드

[https://google.github.io/styleguide/javaguide.html](https://google.github.io/styleguide/javaguide.html)

### Pakage Naming Guide

- 소문자로만 작성
- 언더바 사용 금지

### Class Naming Guide

- 파스칼 표기법으로 작성
- 클래스는 명사, 명사구
- 인터페이스는 명사, 명사구, 형용사
- 테스트는 접미사로 Test

### Method Naming Guide

- 카멜 케이스 표기법으로 작성
- 메서드는 동사, 동사구
- junit 테스트시에는 스네이크 표기법 가능