# 4장 주석
## 주석은 최대한 쓰지 말자

> 나쁜 코드에 주석을 달지 마라. 새로 짜라<br>
> \- 브라이언 W. 커니핸, P.J 플라우거


### 주석은 나쁜 코드를 보완하지 못한다.
- 주석은 순수하게 선하지 못하며, 필요악의 존재
- 코드로 의도를 적절하게 표현하지 못해, 즉 **실패를 만회하기 위해 주석을 사용**하는것
- 따라서 최대한 주석이 없이, 이해가 가는 코드를 짜는게 최우선이다!


        if((employee.flags & HOURLY_FLAG) && employee.age > 65) // 코드의 의도를 알 수 없음
        
        if(employee.isEligibleForFullBenefits()) // 메서드명만으로 코드의 의도를 명확히 파악가능
        

### 주석은 방치된다.

- 코드는 유지보수를 통해 계속 변화하지만, 주석을 유지보수하기는 쉬운일이 아니다.
- 결국 시간이 갈수록 주석은 방치되고 낡고 잘못된 정보를 제공해 오히려 혼란을 야기한다.
- 좋은 메서드명을 짓는데 시간을 투자하자


## 좋은 주석은 뭐지?
### 구현에 대한 정보를 제공하는 주석

     //  kk:mm:ss EEE, MMM dd, yyyy 형식
    Pattern timeMatcher = Pattern.complie("\\d*:\\d* \\w*, \\w* \\d*,\\d*") 
    
- 구현의 복잡성으로 정보전달이 난해할때 사용

### 의도와 중요성을 제공하는 주석

     // 많은 쓰레드를 생성하여 시스템에 영향을 끼쳐 테스트를 만들도록함
    for(int = 0; i< 25000; i++){
        SomeThread someThread = ThreadBuilder.builder().build();
    }
      // 유저로부터 입력받을 값을 저장할 때 trim으로 공백제거 필요
    String userName = userNameInput.trim();
    

- 의도를 표현하는 메서드로 별도로 분리하는것이 **클린코드 원칙상** 바람직하긴 하나, 너무 많은 메서드도 가독성에 저해가 되는것은 사실임
- 실무에서는 팀원들간 협의를 거쳐 메서드로 분리하지 않을 작은 단위 코드에 대한 의도를 주석으로 표현하는것은 좋은 주석이다.


###  TODO, FIXME 주석

    // TODO, FIXME
    
- TODO : 앞으로 할일, 지금은 해결하지 않지만 나중에 해야할 일을 미리 적어둘 때 사용
- FIXME: 문제가 있지만, 당장 수정할 필요가 없을 때, 가능하면 빨리 수정하는게 좋다.

- IDE에서 하이라이팅되며, 별도의 윈도우로도 볼수 있기때문에 관리하기 편리, 사용토록 하자




## 현업에서 주석보다 어노테이션을 사용하기!

- @Deprecated : 컴파일러가 경고발생
- @NotThreadSafe: 멀티쓰레드환경에서 안전하지 않음을 나타냄
- @Autor : 작성자를 나타내는 어노테이션, 하지만 소스관리 형상관리 툴이 훨씬 효율적이며, 이를통해 누가 소스코드를 작성했는지 알수 있기때문에 굳이 쓸필요없음, 팀의 컨벤션에 따를것


## JAVA Doc
JAVA 코드에서 API 문서를 HTML 형식으로 생성해주는 도구로 주석을 보다 깔끔하게 작성하고 보는 툴로 이해하면 편하다.

- 작성법
            
        /** <- 2개
        * This is Javadoc
        */
        
- Class Level 예시

        /** 
        * Please see the {@Link com.testexample.javadoc} class for true identity
        * @Author jinia
        */
      
- Field Level 예시

        /** 
        * the Public name of person that is common knowledge(그냥 설명)
        */
        private String name;

- Method Level 예시

        /** 
        * <p>This is a simple description of the method...
        * <a href='~~~~~~'></a>
        * ...
        * @param 
        * @return
        * @see
        * @since 1.0
        */
        private String name;


1. html 태그 사용 가능
2. @param : 파라미터에 대한 설명
3. @return : 결과값에 대한 설명
4. @see : 참고사항
5. @since : 메서드 버전 이력


- javadoc 빌드 명령어를 통해 html문서화된 내용 출력 가능

![image](https://ejiaah.com/assets/images/post/190317-android-javadoc/img-5.png)

- ide에서도 reader 모드를 통해 보다 깔끔한 주석 보기 가능

![image](https://johngrib.github.io/resource/wiki/intellij/104927651-08813380-59e5-11eb-9b20-f48200d45c7c.png)


# 5장 형식 맞추기

## 클린코드에서 제안하는 바람직한 포멧팅

### 적절한 수직 길이는 ~200, Maximum 500 line
- 일반적으로 큰파일보다 작은 파일이 이해하기 쉽다
- 현업에서 실제로 작성할때도 200라인 이상은 `길다`고 느끼는 편
- 코드가 길다는것은 여러가지 일을 하고있다는 신호일수 있다. (SRP 위배)
- 되도록 200라인 이내로 작성하도록 노력하자

### 함수 배치 tip
- 세로 밀집도를 저해하는 주석은 되도록 배제
- 세로 밀집도를 고려해 변수는 최대한 가까운 위치에서 선언
- 행묶음은 하나의 생각을 표현하며 개념적으로 다른 내용은 빈행으로 분리
- 종속 함수는 상위 함수 바로 다음에 위치하도록할것
- 개념적으로 유사한 함수들을 묶어서 배치할것

        public void assertTrue(){}
        public void assertTrue(args){}
        public void assertFalse(){}
        public void assertFalse(args){}
        

### 가로 길이는 최대 120자
- 80자 이하가 바람직
- 들여쓰기원칙 잘지킬것


## [Java Class Declarations - Oracle Convention](https://www.oracle.com/java/technologies/javase/codeconventions-fileorganization.html)

### Class 내부 코드 순서
1. static 변수
    public -> protected -> default -> private 순서
2. instance 변수
    public -> protected -> default -> private
3. 생성자
4. 메서드
    public 메서드에서 호출되는 private 메서드(종속함수)는 바로 그다음 아래에 배치, 가독성 위주로 그룹핑해서 메서드들을 배치할것


## 팀 코딩 컨벤션
- 팀의 코딩 스타일에 대한 약속
- 개발 언어의 컨벤션이 일반적으로 최우선(DB는 스네이크 케이스 네이밍으로, 자바에서 일반변수는 카멜케이스 네이밍으로)
- 애매한 부분은 팀 컨벤션을 정의하고 준수!


## 참고할만한 컨벤션

- [구글 자바 컨벤션](https://google.github.io/styleguide/javaguide.html)
- [네이버 자바 컨벤션](https://naver.github.io/hackday-conventions-java/)



        



