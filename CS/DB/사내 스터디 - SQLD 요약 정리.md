# 데이터 모델링의 이해
## 모델링의 정의

- 데이터 모델은 현실세계의 정보를 인간과 컴퓨터가 이해할 수 있도록 추상화하여 표현한 모델
- 현실세계를 데이터베이스에 표현하는 중간과정, 데이터의 구조(스키마)를 논리적으로 표현하기 위한 도구
- 현실세계를 추상화한 반영

## 데이터 모델링의 중요성 / 유의점

### 중요성

- 파급 효과
    데이터 주도 설계 관점에서 애플리케이션의 모든 의존성을 데이터를 향하게되므로, 데이터 모델링이 변화하면 애플리케이션 설계 자체도 크게 변화하게된다! 파급효과가 매우 큼
- 복잡한 정보 요구사항의 간결한 표현
    데이터 모델은 구축할 시스템의 정보 요구사항과 한계를 가장 명확하고 간결하게 표현할 수 있는 도구
- 데이터의 가치
   비지니스 데이터는 그 자체로 기업의 중요 자산이므로, 해당 데이터의 품질관리 측면에서 데이터 모델링은 중요

### 유의점

- 중복
    데이터가 중복저장되면 일관성 유지가 힘들어짐
- 비유연성
    데이터 모델이 수시로 변경되면 유지보수가 어려워짐
- 비일관성
    데이터 중복이 없더라도 비일관성이 발생할 수 있음(예를 들어 주문거래를 한적이 없는데, 입출금 내역이 존재한다던가하는 데이터 모순점)

## 데이터 모델링 3단계(개.논.물)

### 개념적 데이터 모델링
- 추상화 수준이 높고 업무 중심적이고 포괄적인 수준의 모델링
- 핵심 엔티티 도출과 관계설정이 가장 중요 

### 논리적 데이터 모델링
- 시스템으로 구축하고자 하는 업무에 대해 key, 속성, 관계등을 정확하게 표현하는 모델링
- 비즈니스 정보의 논리적인 구조와 규칙을 명확하게 표현하는 과정
- 정규화 시점
- 데이터 모델링 완료시점

### 물리적 데이터 모델링
- 실제로 데이터베이스에 이식할 수 있도록 성능, 저장 등 물리적인 성격을 고려하여 설계
- 실제로 물리적으로 어떻게 저장할것인지를 고려하는 단계

## 데이터 독립성 모델 스키마 3단계
> 애플리케이션과 영속성 계층 분리를 위한 DB 설계시점 스키마 정의라고 이해

### 내부 스키마
- DB가 물리적으로 저장된 형식을 정의
- 물리적 장치에서 데이터가 실제적으로 저장되는 방법을 표현하는 스키마

### 개념 스키마
- 구성 모든 사용자 관점을 통합한 조직 전체 DB를 기술하는것
- 모든 응용시스템들이나 사용자들이 필요로 하는 데이터를 통합한 조직 전체의 DB기술, DB에 저장되는 데이터와 그들간의 관계를 표현하는 스키마

### 외부 스키마
- 뷰 단계 여러 개의 사용자 관점으로 구성, 앱 단위에서 접근하는 DB 정의

### 데이터 모델링과 데이터 독립성의 관계

- 개념, 논리데이터 모델링은 일반적으로 개념 스키마에 대해 정의하는것!
- 물리적 모델링은 내부 스키마정의로 이해
- 애플리케이션의 변화에도 외부 스키마는 변할지언정, 개념스키마는 변하지 않는것이 튼튼한 설계이자 좋은 스키마 정의다.

## 데이터 모델링 3요소와 ERD
### 3요소
- 엔티티
- 관계
- 속성

### ERD
> <p>개념적 데이터 모델의 가장 대표적인 형태<br>
- 개체, 관계, 속성의 삼요소로 데이터를 묘사<br>
- 특정 DMBS를 고려X<br>
<img src="https://player.slidesplayer.org/88/15839223/slides/slide_28.jpg" alt="enter image description here"></p>
- <p>사각형 =&gt; 개체<br>
- 마름모 =&gt; 관계<br>
- 타원 =&gt; 속성<br>
- 이중타원 =&gt; 복합속성<br>
- 밑줄타원 =&gt; 기본키 속성<br>
- 복수타원 =&gt; 복합속성<br>
- 선 - 개체 타입과 속성을 연결</p>
<p>개체와 개체간의 관계는 대응수로 표현함</p>
<h4 id="관계의-대응수">관계의 대응수</h4>
<p>관계집합에서 각 개체들이 참여할수 있는 대응의 개수</p>
<ul>
<li>일 대 일</li>
<li>일 대 다</li>
<li>다 대 일</li>
<li>다 대 다</li>
</ul>

[추가 참조](https://github.com/jinia91/TIL/blob/master/DB%20%EC%84%A4%EA%B3%84%20%EB%AA%A8%EB%8D%B8%EB%A7%81/CH.02%20ERD.md)

# 엔티티

## 정의

- 변별할 수 있는 사물
- 데이터베이스 내에서 변별 가능한 객체
- 정보가 저장될수 있는 어떤 것
- 명사, 비지니스적 관심사, 어떤 것(Things)

## 특징

- 비즈니스 업무에 필요하고 관리하고자 하는 정보
- 유일한 식별자 존재
- 인스턴스의 집합이여야함(인스턴스가 두개이상)
- 반드시 속성이 두개이상 존재해야 하고 속성은 하나의 속성값을 가져야함
- 다른 엔티티와 관계가 존재해야함

## 엔티티 학술적 분류
### 유.무형
- 유형 엔티티
- 개념 엔티티
- 사건 엔티티

### 발생 시점
- 기본 엔티티
    다른 엔티티로부터 주 식별자를 상속받지 않고, 자신의 고유한 주식별자를 가진경우
- 중심 엔티티
    기본 엔티티로부터 발생되고, 그 업무에서 중심적인 역할을 하는 경우
- 행위 엔티티
    두개 이상의 부모엔터티로부터 발생하고, 자주 내용이 바뀌거나 데이터양이 증가하는 엔티티


# 속성

## 개념
> 사물의 성질, 특징, 또는 본질적인 성질, 업무에서 필요로하는 인스턴스로 관리하고자 하는 의미상 더이상 분리할 수 없는 최소 데이터 단위

## 특징
- 해당업무에서 필요하고 관리하고자 하는 정보
- 정규화 이론에 근거, 정해진 주식별자에 함수적 종속성을 가져야함
- 하나의 속성은 한개의 값만 가져야함, 만약 다중속성이라면 1차 정규화를 통해 별도의 엔티티를 만드는것이 바람직하다.

## 속성 학술적 분류
### 특성에 따라
- 기본 속성
    비즈니스상 필요하고 실제로 존재하는 기본적인 데이터들
- 설계속성
    비즈니스 규칙화를 위해 변형, 가공한 속성들로 코드성 속성, 일련번호등에 해당
- 파생속성
     비즈니스상 필요하여 연산된 데이터들로, 다른 속성들을 통해 연산할 수 있으므로 가급적 적게 만드는것이 바람직하다. 데이터 정합성에 문제가 생길 수 있음
### 엔티티 구성방식에 따라
- PK
- FK
- 일반

# 관계
## 개념
> 상호 연관성이 있는 상태, 엔티티의 인스턴스 사이의 논리적인 연관성으로서 존재의 형태로서나 행위로서 서로에게 연관성이 부여된 상태

## 관계 학술적 분류
### 존재에 의한 관계
> 팀 - 구성원과 같이 구성원이 팀에 존재함으로서 맺어지는 관계
### 행위에 의한 관계
> 회원 - 주문 처럼 회원이 주문을 함으로서 맺어지는 관계

### UML 클래스 다이어그램에서 연관관계 VS 의존관계
- 연관관계는 존재에 의한 관계에 해당, 실선으로 펴현
- 의존과계는 행위에 의한 관계에 해당, 점선으로 표현
- ERD를 그릴때는 위의 구분없이 모두 실선으로 관계 표현

## 관계 표기법
- 관계 명 : 관계 이름
- 관계 차수 : 몇대 몇 관계인지
- 관계 선택사양 : 필수인지, 선택인지

## 엔티티간 관계 도출시 체크사항
1. 두개의 엔티티 사시에 관심있는 연관규칙이 존재하는가?
2. 두개의 엔티티 사이에 정보의 조합이 발생되는가?
3. 업무 기술서, 장표에 관계연결에 대한 규칙이 서술되어 있는가?
4. 업무 기술서, 장표에 관계연결을 가능하게 하는 동사가 있는가?


# 식별자
## 개념
- 엔티티는 인스턴스의 집합이며, 인스턴스를 구분하는 기준이 바로 식별자
- Primary Key는 정확히는 물리 데이터 모델링 단계에서의 용어이며, 식별자는 논리 데이터 모델링 단계의 용어

## 특징
- 유일성 : 주 식별자에 의해 엔티티내에 모든 인스턴스들이 유일하게 구분되어야 한다.
- 최소성 : 주 식별자를 구성하는 속성의 수는 유일성을 마족하는 최소의 수가 되어야한다.
- 불변성 : 지정된 주식별자의 값은 자주 변하지 않는 것이어야 한다
- 존재성 : 주식별자가 지정이 되면 반드시 값이 들어와야 한다

## 식별자 학술적 분류
### 대표성 여부
- 주식별자
- 보조 식별자
   식별자로서 역할은 가능하나, 대표성이 없으므로 참조 관계 연결 하면 안됨(회원 엔티티에서 id가 존재하고, 주민번호같은것)
### 스스로 생성 여부
- 내부 식별자
- 외부식별자(FK)
### 속성수
- 단일 식별자
- 복합 식별자
### 대체 여부
- 본질 식별자
- 인조 식별자

## 자식 엔티티에서 FK를 PK로 사용할지에 대해
### 식별자 관계인경우(PK로 사용) - 본질 식별자
- 부모엔티티가 반드시 생성된후 자기 엔티티가 생성된느 상황
- 필수관계 - 1대1 혹은 1대 다

### 비식별자 관계인경우(독자적 PK사용) - 인조식별자
- 부모 없이 자식이 생성될 수 있는상황
- 엔티티별 데이터 생명주기 관리가 다른상황

> 실무적으로 식별자/비식별자 관계를 모두 고려하여 설계하기엔 난이도가 높다. 부모 엔티티의 식별자를 모두 상속받되, 독자적인 식별자도 따로 만드는 방식으로 테이블을 구현하면 실무상 더 관리하기가 편하므로 해당 방식으로 구현을 추천(- 김영한님 JPA 강의에서 발췌)<br>
> 하지만 이러한 외부 식별자 사용시에는 중복 데이터 insert에 취약할 수 있음. 결국 개발 편의성과 데이터 정합성간 트레이드 오프라고 생각됨


# 정규화
> 두.부.이.걸.다.줘

- 실무에서는 제3 정규화까지만 하는것이 일반적

## 제 1정규화 : 모든 속성은 반드시 하나의 값을 가져야한다
> (두) 도메인 정의로 속성한개만!

## 제 2정규화 : 엔티티의 일반속성은 주식별자 전체에 종속적이여야한다
, > (부) 부분적 함수 종속 제거
## 제 3정규화 : 엔터티의 일반속성 간에는 서로 종속적이지 않는다
> 이행 종속성 제거

## [참고](https://github.com/jinia91/TIL/blob/master/DB%20%EC%84%A4%EA%B3%84%20%EB%AA%A8%EB%8D%B8%EB%A7%81/CH.03%20%EB%85%BC%EB%A6%AC%EC%A0%81%EB%AA%A8%EB%8D%B8%EB%A7%81.md)

## 반정규화와 성능
- 데이터 중복을 허용하는 대신 선응 향상을 야기하는 것
- 조회성능은 향상되지만, 일반적으로 입력 수정 삭제 성능은 저하된다.

### 반정규화 절차
1. 반정규화 대상 조사
    - 범위처리빈도수 조사
    - 대량의 범위 처리 조사
    - 통계성 프로세스 조사
    - 테이블 조인 개수
2. 다른 방법유도 검토
    - 뷰 테이블
    - 클러스터링
    - 인덱스 조정
    - 앱단위 로직
3. 반정규화 적용
    - 테이블 반정규화
    - 속성의 반정규화
    - 관계의 반정규화

### 슈퍼 / 서브 타입 데이터 모델 변환 기술
- 개별로 발생되는 트랜잭션에 대해서는 개별 테이블로 구성
- 슈퍼타입 + 서브타입에 대해 발생되는 트랜잭션에 대해서는 슈퍼타입 + 서브타입 테이블로 구성
- 전체를 하나로 묶어 트랜잭션이 발생시엔 하나의 테이블로 구성


# SQL 기본
## SELECT
> SELECT [ALL/DISTINCT] 출력 대상 칼럼명....., FROM 출력대상 칼럼의 테이블명

### Alias 사용시 주의사항
- 공백이 들어가는 경우 `" "` 사용
- SQL Server의 경우 `" "`, `' '`, `[ ]` 가능

### 산술연산자와 합성 연산자
#### 산술 연산자
> +, -, *, /
- 수학의 4칙연산과 동일, 우선순위를 위한 `()` 적용 가능
#### 합성 연산자(Concatenation)
- 문자와 문자 연결시 `||`(Oracle) 또는 `+`(SQL  Server)
- CONCAT(st1, st2) 함수(Mysql도 가능)
- 칼럼과 문자, 또는 칼럼과 칼럼 연결

### SQL 문장 실행 순서

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY

## 함수 - 단일행함수만
### 함수 분류
![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/edae1656-4d3b-4a4b-b8dc-89062446c14d.png)


### 단일행 함수 - 문자열 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/27f02e7b-f284-45ef-a67f-1513d2c54150.png)


![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/cd6fec3c-c62b-49e6-b6cc-19adf8e5deff.png)
### 단일행 함수 - 숫자형 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/af237faf-480a-4d7c-bab7-cb8d6eadbe69.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/48298974-6b90-454e-be45-63985b813f1e.png)


### 단일행 함수 - 날짜형 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/b79ee7ff-424d-45b0-8d80-ef9c30a4657d.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/f07d194b-a6a3-4427-9e58-3ec8d82dec4a.png)


### 단일행 함수 - 변환 함수

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/4514ec71-6bb3-4521-8220-6b4d601fbaf8.png)

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/7d28a33f-f6a4-4ca8-971d-26acff4e4fbe.png)

### 단일행 함수 - CASE 표현식
e.g)

    SELECT NAME,
           CASE
               WHEN SAL > 2000 THEN SAL
               ELSE 2000
           END AS REVISED_SAL
     FROM EMP   


- CASE - WHEN - ELS - END

- ORACLE은 `DECODE()`함수로도 같은 표현 가능 

### 단일행 함수 - NULL 관련 함수

#### NULL 의 특성
- 널값은 아직 정의되지 않은 값으로 0, 공백과는 다르다. 0은 숫자, 공백은 하나의 문자
- 테이블을 생성할때 NOT NULL / PRIMARY KEY로 정의되지 않은 모든 데이터 유형은 NULLABLE
- NULL값을 포함하는 연산의 경우 결과 값도 NULL 값이다. 모르는 데이터에 숫자를 더하거나 뺴도 결과는 마찬가지로 모르는 데이터인셈

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/53f570df-c070-4f3b-a3ae-d5ff68194a3d.png)


#### NVL/ISNULL(SQL Server)
- 결과값을 NULL이 아닌 다른 값을 얻고자 할때, NVL/ISNULL사용

- 데이터 조회시 컬럼이 NULL인것과 공집합(조회되는 데이터가 아예 없는것)은 다른 상황이다.
- NVL/ISNULL을 사용하더라도 공집합은 공집합으로 처리됨!
    
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT NVL (SAL, 1000) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 결과값없음
       
- 해당 경우 적절한 집계함수를 사용해 대처
     
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT MAX (SAL) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 1개의 행 선택, 빈 컬럼
       
       // CWJ의 SAL컬럼 데이터가 NULL 인 경우
       SELECT NVL(MAX(SAL),1000) AS SAL
       FROM EMP
       WHERE NAME = 'CWJ'
       // 1개의 행 선택, 1000으로 출력
       

## WHERE

### 연산자 우선순위


| 연산자 우선순위 | 설명 |
| --- | --- |
| 1 | 괄호 () |
|  2| 비교연산자, SQL 연산자 |
|  3| NOT 연산자 |
|  4| AND |
|  5|  OR|

### SQL 연산자

#### IN
아래 처럼 사용도 가능
> WHERE (JOB, DEP_NO) IN (('MANAGER', 20), ('EMP', 30));

#### LIKE

- 와일드 카드 `%`, `_`
    - `%` : 0개 이상의 어떤 문자
    - `_` : 1개인 단일 문자

## GROUP BY, HAVING 절
### 다중행함수 - 집계함수
- 여러 행들의 그룹이 모여, 그룹당 단 하나의 결과를 돌려주는 함수
- GROUP BY 절은 행들을 소그룹화한다
- SELECT, HAVING, ORDER BY 절에서 사용가능

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/3ee8d4cb-2cb6-4671-bad5-f6e87c181063.png)

- GROUP BY 절 없이도 사용 가능!(테이블 전체도 하나의 그룹이므로)
- `COUNT(col1)`에서 `col1`에 NULL이 존재하는 경우 카운팅 x

### GROUP BY
- 데이터들을 작은 그룹으로 분류해 소그룹에 대한 항목별 통계정보를 얻을때 사용
- GROUP BY 절을 통해 소그룹별 기준을 정한 뒤, SELECT 절에 집계함수 사용
- 집계함수의 통계정보는 NULL 값을 가진행을 제외하고 수행
     - e.g) SUM 연산에서는 NVL을 사용해 NULL을 0으로 바꾸는 연산이 불필요하지만, AVG 같은경우 NVL을 통해 0으로 만들어야 제대로된 평균값을 구할수 있음
- GROUP BY 절에선 ALIAS 사용 불가
- WHERE 절 실행 후 GROUP BY 절 실행
- 따라서 GROUP BY 이후 조건절 필요시 HAVING 절 사용

## ORDER BY 절
### 정렬 데이터에 NULL 존재시
- ORACLE :  NULL 값을 가장 큰값으로 간주
- SQL Server, MYSQL : NULL 값을 가장 작은 값으로 간주

## JOIN
### EQUI JOIN
> 등가 조인

두개의 테이블 간에 컬럼값들이 정확하게 일치하는 경우 사용되는 방법

       SELECT T1.COL1, T2.COL1
       FROM T1 JOIN T2
           ON T2.COL2 = T1.COL2
           
### NON EQUI JOIN
> `=`연산자가 아닌 다른 연산자들을 사용해 JOIN 수행(Between, >, >=, <,<= 등)

- 예를들어 연봉컬럼(SAL)이 있는 회원테이블과 연봉 범위에 따라 연봉 등급이 있는 연봉등급 테이블 두개가 존재한다고 가정할 때,

        SELECT A.NAME, A.SAL, B.GRADE
        FROM EMP A, SALGRADE B
        WHERE A.SAL BETWEEN B.LOW_SAL AND B.HIGH_SAL
        
위와같은 join도 가능

### JOIN 구문 표현법

#### 1. INNER JOIN

- JOIN - ON 표현

#### 2. NATURAL JOIN
- FROM 절에 `NATURAL JOIN` 표시하면 알아서 EQUI JOIN 실행
- ORACLE 만 가능
- 가독성과 유지보수 차원에서 안쓰는것 권장
- `USING`가 `ON`과 같은 기능 수행

# SQL 활용
## 서브 쿼리
> 하나의 SQL 문안에 포함돼 있는 또다른 SQL 문. 서브쿼리는 메인쿼리가 서브쿼리를 포함하는 구조의 종속적인 관계

- 서브쿼리는 괄호로 감싸서 기술
- 서브쿼리는 단일 행 또는 복수행 비교 연산자와 함께 사용 가능
- 중첩서브쿼리, 스칼라서브쿼리에서는 ORDER BY 사용 불가능

### 단일행 서브쿼리
> 서브쿼리의 결과가 반드시 한건이하로 나오는 서브쿼리 

- 단일행 비교 연산자와 함께 사용 가능
- e.g) A회원이 포함한 팀이 맴버를 찾는 구분
        
        SELECT *
        FROM MEMBER
        WHERE TEAM_ID = (SELECT TEAM_ID
                            FROM MEMBER
                            WHERE MEMBER_NAME = 'JINIA')
        ORDER BY MEMBER_NAME;
        

### 다중행 서브쿼리
> 서브쿼리 결과가 2건이상 반환되는 케이스

- 다중행 비교 연산자와 함께 사용해야한다(IN, ALL, ANY, SOME)
- e.g) 단일행 서브쿼리 예시에서 JINIA 가  동명이인이 존재, 서로 다른팀인경우 

        
        SELECT *
        FROM MEMBER
        WHERE TEAM_ID IN (SELECT TEAM_ID
                            FROM MEMBER
                            WHERE MEMBER_NAME = 'JINIA')
        ORDER BY MEMBER_NAME;
        
   - 실행결과 : 두개의 데이터가 조회됨

### 다중 칼럼 서브 쿼리
> 서브쿼리의 결과로 여러개의 컬럼이 반환되며, 메인쿼리의 조건과 동시에 비교

- SQL Server에서는 지원하지 않는 문법

        
        SELECT *
        FROM MEMBER
        WHERE (TEAM_ID, HEIGHT) IN (SELECT TEAM_ID, MIN(HEIGHT)
                            FROM MEMBER
                            GROUP BY TEAM_ID)
        ORDER BY MEMBER_NAME;
        

### 연관 서브 쿼리

> 서브 쿼리 내에 메인 쿼리 칼럼이 사용된 서브 쿼리

### 스칼라 서브 쿼리
> SELECT 절 위치에서 사용하는 쿼리로 한행, 한 칼럼만을 반환하는 쿼리

### 인라인뷰
> FROM 절에서 사용하는 서브쿼리
- 마치 테이블처럼 사용 가능

## 집합 연산자

> 두개 이상의 테이블에서 조인을 사용하지 않고 연관된 데이터를 조회하는 방법


| 집합 연산자 |연산자의 의미  |
| --- | --- |
| UNION | 개별 SQL 에 대한 합집합, 중복 하나로 압축 |
| UNION ALL | 개별 SQL 에 대해 합집합시 중복된 행도 그대로 표시 |
| INTERSECT | 개별 SQL 에 대한 교집합 |
| EXCEPT | 차집합 연산(ORACLE- MINUS, SQL Server - EXCEPT) |

- INTERSECT 연산은 WHERE EXISTS 또는 IN 서브 쿼리로 변경 가능


## 다중행 함수 - 그룹 함수
> 하나 이상의 행을 그룹으로 묶어 연산하여 하나의 결과를 나타내는 것

### 그룹함수의 필요성

- 결산 개념의 업무를 가지는 원가나 판매 시스템의 경우는 소계, 중계, 합계, 총 합계 등 여러 레벨의 결산 보고서를 만드는 것이 중요 업무. 
- 개발자들이 이런 보고서를 작성하기 위해서는 SQL이 포함된 3GL으로 배치 프로그램을 작성하거나, 레벨별 집계를 위한 여러 단계의 SQL을 UNION, UNION ALL로 묶은 후 하나의 테이블을 여러 번 읽어 다시 재정렬하는 복잡한 단계를 거쳐야함

- 하지만, 그룹 함수(GROUP FUNCTION)을 사용한다면, 하나의 SQL로 테이블을 한 번만 읽어서 빠르게 원하는 리포트를 작성 가능

### ROLLUP 함수
> SubTotal을 생성하기 위해 사용

- Grouping Columns의 수를 기준으로 N+1 레벨의 Subtotal이 생성된다.

       SELECT B.NAME, A.JOB, COUNT(*) AS EMP_CNT, SUM(A.SAL) AS SAL_SUM 
       FROM EMP A, DEPT B
       WHERE B.DEPTNO = A.DEPTNO
       GROUP BY ROLLUP (B.DNAME, A.JOB);
       

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/90ab6c8d-0457-478e-b05c-35da7b112b28.png)


### CUBE 함수
- ROLLUP 에서는 단지 가능한 Subtotal만 생성하지만, CUBE는 결합가능한 모든 값에 대해 다차원 집계를 생성
- CUBE 를 사용할 때는 내부적으로 Grouping Columns의 순서를 바꾸어서 또 한번의 쿼리를 추가 수행하며, Grand Total은 양쪽의 쿼리에서 모두 생성되고 중복제거 되므로, ROLLUP에 비해 시스템 연산이 많다.

### GROUPING SETS 함수
> GROUPING SETS는 여러 그룹핑 쿼리를 UNION ALL 한 것과 같은 결과를 만들어 다양한 소계 집합을 만들때 사용

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/1bef04e4-2eee-45a6-acdd-c095db5159b4.png)


## 윈도우 함수
### 개요
기존 관계형 데이터베이스는 칼럼과 칼럼간의 연산, 비교,연결이나 집합에대한 집계는 쉬운 반면, 행과 행간 관계정의, 비교,연산을 sql로 처리하기가 어려웠다.

Window Function을 사용하면 이런 행간 관계정의, 연산을 SQL로 해결하기 훨씬 쉬워진다.

### 종류
1. 그룹 내 순위 관련 함수(RANK)
2. 그룹 내 집계 관련 함수(기존 집계함수들)
3. 그룹 내행 순서 관련 함수
4. 그룹 내 비율 관련 함수
5. 선형분석을 포함한 통계분석 관련 함수

### 문법
> SELECT WINDOW_FUNCTION (ARGUMNETS) OVER ([PARTITION BY 칼럼] [ORDER BY 절] [WINDOWING 절]) FROM 테이블;

- WINDOW_FUNCTION : 기존에 사용하던 함수도 있고, 새롭게 Window 함수용으로 추가된 함수도 있음
- ARGUMENTS : 함수에 따른 인수
- PARTITION BY 절 : 전체 집합을 기준에 의해 소그룹으로 나눌 수 있음
- ORDER BY :  정렬
- WINDOWING : WINDOWING 절은 함수의 대상이 되는 행 기준의 범위를 강력하게 지정할 수 있다.

### 순위 함수

#### RANK
> ORDER BY 를 포함한 쿼리문에서 특정 항목에 대한 순위를 구하는 함수

        SELECT JOB, NAME, SAL, RANK () OVER (ORDER BY SAL DESC) AS SAL_RANK
        FROM EMP;
        
#### DENSE_RANK
> RANK 와 유사하나 동일한 순위를 하나의 건수로 취급

- RANK 가 1, 2,2,2, 3 순으로 간다면, DENSE_RANK는 1,2,2,2,5 순으로

#### ROW_NUMBER
> 동일 값에 대해 다른값을 부여
- 무조건 1,2,3,4,5...

### 일반 집계함수 -> 윈도우함수

> 파티션별 값을 구하기 위해 사용

        SELECT NO, NAME, SAL, SUM(SAL) OVER(PARTITION BY NO)
        FROM EMP;
        
- SUM, MAX,MIN,AVG,COUNT

### 그룹 내 행 순서 함수
- FIRST_VALUE
- LAST_VALUE
- LAG
- LEAD

### 그룹 내 비율 함수

- RATIO_TO_REPORT
- PERCENT_RANK
- CUME_DIST
- NTILE

### TOP N 쿼리
#### ROWNUM 슈도 컬럼
 - 오라클만의 문법

        SELECT NAME, SAL
        FROM EMP
        WHERE ROWNUM < 4
        ORDER BY SAL DESC;
        
#### TOP 절
> TOP (Expression) [PERCENT] [WITH TIES]

- SQL Server 문법
- Expression : 반환할 행 수를 지정하는 숫자


### ROW LIMITING
- ANSI 표준 SQL 문법
- ORACLE 12.1, SQL Server 2012부터 가능

## 계층형 질의와 셀프 조인
관계형 데이터베이스는 계층형 데이터를 저장할 때, 순환 관계 데이터 모델을 사용하여 저장 가능하고, 이를 조회하기 위해서 계층형 질의를 사용하거나, 셀프조인으로 계층형 구조를 흉내낼 수 있다.

### 셀프 조인
> 동일 테이블 사이의 조인

- 깊은 레벨의 노드를 조회하기위해서는 셀프 조인 반복이 필요
- Oracle과 SQL Server는 순환관계를 가진 데이터르 조회할수 있는 계층형 질의 기능을 제공한다.
- MYSQL은 계층형 쿼리가 존재하지 않음

### 계층형 질의
#### ORACLE

    SELECT ...
    FROM ...
     AND ...
    START WITH ..
            AND ..
    CONNECT BY ..
            AND ..
   
    
- 계층 표현을 위해 가상 컬럼 사용
    - LEVEL : 루트 데이터면 1, 그 하위 데이터면 2, 3...
    - CONNECT_BY_ISLEAF : 리프 데이터인지 여부 0/1
    - CONNECT_BY_ISCYCLE : 노드간 사이클링이 발생할경우, 옵셔날하게 출력 가능

#### SQL Server
- CTE(Common Table Expression)을 재귀호출하여 구현 가능

## PIVOT 절과 UNPIVOT절
- 행을 열로, 열을 행으로 회전시키는 문법

## 정규 표현식
### 정규 표현식 조건과 함수
- REGEXP_LIKE ()
- REGEXP_REPLACE ()
- REGEXP_SUBSTR()
- REGEXP_INSTR ()
- REGEXP_COUNT ()


#### CROSS JOIN

- 양 테이블을 모두 조인하는것
- M * N 건의 데이터 조합 발생

#### OUTER JOIN
- 오라클은 조인 컬럼뒤에 `+` 표시 추가로 OUTER JOIN 실시 가능하지만 ANSI 표준을 사용하자
- LEFT OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN

# 관리 구문
## DML
### INSERT 
> INSERT INTO TABLE [(COL1, COL2....)] VALUES (DATA1,DATA2 ...);

OR

> INSERT INTO TABLE VALUES (DATA1, DATE2 ...);

#### 서브 쿼리를 사용해 다중 행 INSERT

> INSERT INTO TABLE [(COL1, COL2, ... )] 서브 쿼리;

### UPDATE
> UPDATE table SET 수정할 칼럼1  = 수정될 값1 [, 수정할 컬럼2 = 수정될 값2] [WHERE 수정 대상 식별 조건식];

### DELETE
> DELETE [FROM] 테이블명 [WHERE 삭제 대상 식별 조건식];

- where 절 없을 경우 테이블 전체 데이터삭제

### MERGE 
- 데이터 수정/ 삭제/ 입력을 한번에 하는 쿼리
- 여러 DML문을 단일문으로 대체 가능하다.

        MERGE 타겟 테이블
        USING 소스 테이블
        ON (조인 조건식)
        WHEN MATCHED THEN
            UPDATE
            SET 수정할 컬럼1 = 수정될 새로운 값 1
        WHEN NOT MATCHED THEN
            INSERT [(칼럼1, 칼럼2, ...)]
            VALUES (값1, 값2, ...) ;
            
### DDL과 DML 명령어 처리 방식 차이
- DDL은 데이터구조의 변경이 즉각 반영
- DML은 COMMIT이 필요
- 데이터 삭제에 있어서 DELETE는 로그를 저장하므로 ROLLBACK 가능
- TRUNCTATE TABLE 사용시 데이터 로그가 없어 ROLLBACK 불가능

## TCL
### 트랜잭션 개요
- 데이터 베이스의 논리적 연산단위
- 하나의 트랜잭션에는 하나 이상의 SQL 문장이 포함
- 분할할 수 없는 최소의 단위
- ALL or NOTHING 

### ACID
1. Atomicity : 트랜잭션으로 정의된 연산은 최소 원자성을 유지해야하므로, 모두 성공하던지 모두 실패하던지 해야한다(All or Nothing)
2. Consitency : 트랜잭션이 실행되기전 데이터가 잘못되지 않았다면, 트랜잭션 수행후에도 데이터는 무결해야한다.
3. Isolation : 트랜잭션이 실행되는 도중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다.
4. Durability : 트랜잭션이 성공한다면 그 갱신내역은 영속화되어야 한다.

### COMMIT
> 트랜 잭션 완료

- 변경사항이 데이터베이스에 반영
- 이전 데이터는 영원히 잃어버림
- 모든 사용자는 결과를 볼 수 있음
- 관련된 행에 대한 잠금이 풀리고, 다른 사용자들이 행을 조작할 수 있게된다.

### ROLLBACK
> COMMIT을 하지 않았다면 실행한 SQL문 이전 COMMIT시점으로 되돌리는 명령어


- 데이터에 대한 변경사항 취소
- 데이터가 트랜잭셔 시작 이전으로 되돌려진다
- 관련된 행의 LOCKING 해제


### SAVEPOINT
> ROLLBACK 할 지점을 임의로 지정하는 명령어

## DDL

### CREATE TABLE

        CREATE TABLE TABLE_NAME (
         칼럼명1 데이터유형 [기본값] [NOTNULL],
         칼럼명2 데이터유형 [기본값] [NOTNULL],
         칼럼명3 데이터유형 [기본값] [NOTNULL],
        );
        
            
#### 주의할 규칙
 - 테이블 명은 객체를 의미할 수 있는 적절한 이름일것, 가능한 단수형을 권고
 - 테이블명은 다른 테이블의 이름과 중복되지 않아야한다
 - 한 테이블 내에서는 칼럼명 중복x
 - 반드시 숫자가 아닌 문자로 시작
 - `-` 사용 금지
 
#### 제약 조건(CONSTRAINT)

| 구분 | 설명 |
| --- | --- |
| PRIMARY KEY | 테이블에 저장된 행 데이터를 고유하게 식별하기 위한 기본키 정의<br> 하나의 테이블에 하나의 기본키 <br> 자동으로 UNIQUE 인덱스 생성, NOTNULL |
|  UNIQUE| 저장된 행 데이터를 고유하게 식별하기 위한 고유키 정의 |
| NOT NULL | NULL 금지 |
| CHECK | 입력할 값을 제한, T/F로 평가 가능한 논리식이 와야됨 |
| FOREIGN KEY |   테이블간 관계를 정의하기 위해 기본키를 다른 테이블의 외래키로 복사하는 경우 외래키 생성, 참조 무결성 제약옵션을 선택할 수 있다.|


#### ALTER TABLE
1. ADD COLUMN
    - 기존 테이블에 컬럼 추가

            ALTER TABLE 테이블명
            ADD (추가할 컬럼명1 데이터 유형 [기본값] [NOT NULL]
            [, ...]);
            
           
2. DROP COLUMN
    - 테이블에 필요없는 컬럼 삭제

            ALTER TABLE 테이블명
            DROP (삭제할 컬럼명1 [, 삭제할 컬럼명2,...]);
            
3. MODIFY COLUMN
    - 테이블에 존재하는 칼럼에 대해 칼럼의 데이터유형, 디폴트값, 제약조건등을 변경

            ALTER TABLE 테이블명
            MODIFY ( 컬럼명1 데이터 유형 [기본값] [NOT NULL]
            [, ...]);
            
    - 해당 칼럼의 크기를 늘릴수는 있지만, 테이블에 데이터가 존재한다면 칼럼의 크기를 줄이는데는 제약이 있다.
    - 해당 칼럼이 NULL만 존재한다면 데이터 유형을 변경할 수 있다.
    - DEFULT 변경은 이후 발생하는 행 삽입에만 영향
    - NULL이 없을경우에만 NOT NULL 제약조건 추가가능

4. RENAME COLUMN
    - 칼럼명 변경시 사용

            ALTER TABLE 테이블명
            RENAME COLUMN 기존 컬럼명 TO 새로운 컬럼명;
            
5. DROP CONSTRAINT
    - 제약조건 삭제

            ALTER TABLE 테이블명
            DROP CONSTRAINT 제약조건명;
            
6. ADD CONSTRAINT
    - 제약 조건 추가
    

            ALTER TABLE 테이블명
            ADD CONSTRAINT 제약조건명 제약조건 (칼럼명);
            

### RENAME TABLE

- 테이블 명 변경

> RENAME 기존 테이블명 TO 새로운 테이블명;

### DROP TABLE
- 테이블 삭제

 > DROP TABLE 테이블명 [CASCADE CONSTRAINT];

### TRUNCATE TABLE
- 해당 테이블에 들어 있던 모든 행들이 제거, 제약조건등은 남아있음

> TRUNCATE TABLE 테이블명;

## DCL
- 유저 권한을 생성하고 제어하는 명령어


