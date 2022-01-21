# 인덱스(색인)란?

## 정의

> DB 테이블에 대한 검색 성능 속도를 높여주는 자료구조

- 관계형 디비의 궁극적 목적은 데이터 영속화와 관리
- 인덱스는 관리시 성능 향상을 목적으로 사용하는 기술

## 대략적인 동작 방식 설명

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcQi8RP%2Fbtq8BkRrRfb%2Fa5C0jH5pfSA2KKz7C9fB7k%2Fimg.png)

1. DB에 데이터를 조회하는 요청(쿼리)
2. DBMS 옵티마이저의 판단하에 생성된 인덱스를 선택하여 데이터 탐색
3. 일종의 목차, 사전의 목차를 생각해보면 이해하기 쉽다


## 인덱스 사용 이유, 장점

### WHERE 절의 문제점
- WHERE절은 조건절이며 인덱스가 없을 시 기본적으로 모든 데이터를 순차적으로 조회하여 해당 조건에 맞는 데이터를 탐색하는 시간복잡도 O(N)으로 작동
- 이러한 `FULL TABLE SCAN`은 데이터가 많을수록 시간이 비례해서 증가하기 때문에 데이터 규모가 클수록 성능 문제 야기
- 인덱스를 사용하면 시간복잡도를 대폭 감소가능(세그먼트 트리에 쿼리를 날리는 알고리즘 등을 생각해보면 이해가 쉬움)

### 인덱스는 정렬된 자료구조

- 인덱스는 생성시 정렬을 1차로 한 뒤 만들어지므로 정렬이 필요한 쿼리에 훨씬 빠른 대응이 가능


## 인덱스의 단점

- 인덱스가 기준으로 삼는 컬럼이 변경되는 경우(`UPDATE`, `DELETE`,`INSERT`) 인덱스를 재생성해야한다.
- 만약 잦은 변경이 있는 컬럼이라면 오버헤드가 걸림
- 인덱스라는 새로운 자료구조를 생성해야 하기 때문에 추가 저장공간이 필요(약 10%)


## 그럼 인덱스는 어디에 써야하나?

1. 조건절이 자주등장하는 컬럼(검색)
2. 항상 = 으로 비교되는 컬럼
3. 중복되는 데이터가 최소한인 컬럼
4. ORDER BY절에서 자주 사용되는 컬럼
5. 조인 조건으로 자주 사용되는 컬럼

## 인덱스는 특별한게 아니다!

- 인덱스를 별도로 생성할 수 있기는 하지만, RDBMS는 데이터 영속화의 기본적략으로 PK를 사용하며 테이블 생성시 PK를 기준으로 기본 인덱스를 생성한다.

- PK를 기준으로 조회를 할 때 인덱스를 타서 조회가 가능

# 인덱스에 대해 더 알아보자

## 인덱스의 구조

### B TREE 

![image](http://1.bp.blogspot.com/-Djz8SoTFVGI/VKw3tQ7FenI/AAAAAAABlUE/5hHN6zZe_Gg/s1600/btree-6.png)

- 하나의 노드가 가질수 있는 키가 2개 이상 허용
- 이진탐색트리처럼 중위순으로 정렬되있어야함
- 생성/삭제 작업이 자주 이뤄지는 필드라면 인덱스를 함부로 생성하지 않는 것이 좋다.

### B+ TREE 

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRiL19%2FbtqBTMSBCWF%2FJ3nKw2qympUVxGThnVdLK0%2Fimg.png)

- B TREE 구조를 확장한 자료구조
- 상위 노드에 데이터를 담지 않고 키값만 존재
- 상위 노드의 볼륨이 작기때문에 더 많은 키를 담을수 있고 수평적으로 더 길게 자료구조 형성가능
- 따라서 트리 높이를 낮춰 보다 빠른 탐색 가능
- 리프노드에 모든 데이터를 저장하여 풀스캔시 리프노드에 도달하면 선형으로 바로 데이터 조회가능
- 리프노드끼리 링크드리스트로 연결되어 수평 이동 가능
- MYSQL의 INODB는 B+TREE 기반으로 구현

## 인덱스 사용 분류

### 1. 인덱스 레인지 스캔(INDEX RANGE SCAN)
- 인덱스를 통해 해당 범위만 탐색하는 방식
- 효율적인 인덱스 사용!

### 2. 인덱스 풀 스캔(INDEX FULL SCAN)
- 인덱스를 쓰긴 하지만 인덱스 자체는 처음부터 끝까지 조회
- 비효율적으로 인덱스를 사용한경우

### 3. 루스 인덱스 스캔(LOOSE INDEX SCAN)

- 인덱스 레인지 스캔처럼 일부만 읽는데 한 번에 쭉 읽는 게 아니라 중간에 필요치 않는 인덱스 키 값은 무시하고 듬성듬성 읽는 방법


## 비효율적인 인덱스 사용 쿼리

- 인덱스 컬럼절을 변형하는 경우 풀테이블 스캔
- LIKE 연산자 사용시 인덱스 풀스캔
- NULL 사용시 대부분 인덱스 풀스캔
- 부정형 조건 사용시 풀테이블 스캔


# References

- [https://coding-factory.tistory.com/746](https://coding-factory.tistory.com/746)
- [https://jeong-pro.tistory.com/242](https://jeong-pro.tistory.com/242)
- [https://zorba91.tistory.com/293](https://zorba91.tistory.com/293)
- [https://7day.tistory.com/214](https://7day.tistory.com/214)