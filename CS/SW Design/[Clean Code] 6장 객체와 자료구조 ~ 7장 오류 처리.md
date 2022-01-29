# 6장 객체와 자료 구조

## 자료구조 vs 객체
| 자료구조(Data Structure) |객체(Object) |
| --- | --- |
| 데이터 그 자체 | 비지니스 로직 관련 |
| 자료를 공개한다 | 자료를 숨기고, 추상화<br>자료를 다루는 함수만 공개한다 |
|  변수 사이에 조회함수와 설정함수를 사용한다고 객체가 되진 않는다.(getter, setter)| 추상 인터페이스를 제공해 사용자가 구현을 모른채 자료의 추상화된 부분을 조작 가능하다 |


### 클린코드에서 말하는 자료구조 예시


    public class Car{
        double fuelTankCapacityInGallons;
        double gallonsOfGasoline;
        
        public double getFuelTankCapacityInGallons(){
        return this.guelTankCapacityInGallons;
        }
        
        public double getgallonsOfGasoline(){
        return this.gallonsOfGasoline;
        }
         
        public void setFuelTankCapacityInGallons(double amount){
        this.guelTankCapacityInGallons = amount;
        }
        
        public void setgallonsOfGasoline(double amount){
        this.gallonsOfGasoline = amount;
        }
    }

- 단순히 필드를 갖고 이를 반환하거나 설정하는 함수만 갖는다고 이것이 객체가 되는게 아니다.
- 클린코드에 따르면 위의 클래스는 사이비 캡슐화를 한 아무 이익없는 자료구조일뿐 객체라고 할 수 없다.
- 우리가 흔히 말하는 `java beans` 방식으로 작성된 `DTO`클래스에 대해 부정적인 의견

### 의미있는 객체로 쓰인 예시


    public class Car{
        double fuelTankCapacityInGallons;
        double gallonsOfGasoline;
    
        // 필드값을 비지니스로직에 맞는 형태로 가공해서 제공
        public double getPercentageFuelRemain(){
            return this.gallonsOfGasoline / this.fuelTankCapacityInGallons*100;
        }    
    }
    

### 객체지향이 진리라고 믿는건 '미신'

- 절차지향프로그래밍에 따라 자료구조를 만들고 이를 다루는 클래스를 작성하는 방식 
 
- 객체지향에 따라 상태값을 가진 클래스들을 다형성 아래 정의하고 상위 인터페이스로 이를 다루는 메서드를 정의하는 방식 

- 위의 두가지 방식중 전자는 메서드의 추가가 용이하지만 새로운 자료구조가 생겼을 경우 이를 다루는 클래스를 고쳐야하는 문제점 존재

- 반면 후자는 인터페이스를 구현한 새로운 클래스가 추가되어도 다른 어떤 코드도 고칠필요 없으나, 반대로 메서드를 추가하는 경우 (default 메서드를 사용하지 않는이상) 모든 구현체에 해당 메서드를 다 추가하는 문제점 발생

절차지향 프로그래밍과 객체지향 프로그래밍은 모두 장단이 있는 방법론이며 절대적으로 옳은 방식은 없다.

>상황에 맞는 선택을 할것!

## 객체 - 디미터 법칙(law of demeter)

![image](https://miro.medium.com/max/1076/1*I3FTpK5ra_w4Vse31eGB-A.png)

> 클래스 C의 메서드 F는 호출할수 있는 메서드의 SCOPE를 제한해야한다.<BR>
> - 클래스C 자신의 메서드
> - 자신이 생성한 객체의 메서드
> - 자신의 인수로 넘어온 객체의 메서드
> - C인스턴스 변수에 저장된(참조된) 객체의 메서드

### 디미터 법칙을 어긴 코드(기차충돌 코드)

`final long amount = kim.getProfile().getPayment().getPaymentAmount();`

어떤 객체가 어떤일을하는지 명확히 모르고 지나치게 이어져있으므로 좋지 못한코드다.

    User kim = repository.findById(id);
    Profile profileOfKim = kim.getProfile();
    Payment paymentOfKim = profileOfKim.getPayment();
    long payAmountOfKim = paymentOfKim.getPaymentAmount();
    
위와같이 표현하더라도 이는 자료구조를 객체를 흉내내 억지로 그래프를 타고 값을 조회하는 방식일 뿐 좋은코드라고 할 수 없다.

      User kim = repository.findById(id);
      long payAmountOfKim = kim.calcTotalPayAmount();

    
이처럼 외부에서 몰라도 되는 여러 객체들을 감추고 비지니스로직에 필요한 반환만 하는 메서드를 정의하면 해결된다.


## Java Beans와 DTO

### DTO란?(Data Transfer Object)

> 계층간 데이터 이동을 위한 자료운반용 클래스, 자료구조

- 로직없이 필드만 갖는다.
- 일반적으로 Dto(or DTO)로 끝난다.
- 자바빈즈 작성법에 따라 필드는 private으로 막고  getter / setter로 접근한다.

### Bean 구조의 DTO가 필요한가?

- DTO는 어차피 아무런 비지니스 로직을 갖지 못하며, 순수하게 자료구조, 구조체로 사용되는경우가 많다.

- 실제 코틀린에서 dto 역할을 하는 Data Class는 모든 필드를 public으로 갖고 getter/setter를 사용하지 않으며 자료구조 접근법을 사용한다.

- **순수하게 DTO역할에 충실하다면 굳이 getter, setter를 정의하지 않고 public으로 써도 무방하지 않을까?**

## Active Record와 Entity

### Active Record


![image](https://4.bp.blogspot.com/-iQzSJ9vndDY/WtGhAhCEvZI/AAAAAAAACCE/ZN2QeVXK5OYeTC7HwoB-OzktNCoMDQqVQCLcBGAs/s400/active-record.png)

> DB row를 객체에 매핑하는 orm 패턴

- 객체 안에 Repository 로직과 엔티티로서의 구조가 혼용된 패턴
- Ruby On Rails에서 주로 사용한다.
- 이 패턴의 경우 비즈니스로직 메서드를 추가해 객체로 취급하지말고 자료구조로 사용할 것을 책에서는 권장


### Entity(Data Mapper)

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/45f4a39c-39bf-4ae3-9666-dbaaa127b7cb.gif?raw=true)

- row를 담는 객체(Entity)와 db에 접근할 객체(Repository)가 분리되어있다.

# 7장 오류 처리

## Checked > Unchecked

### 오류코드보다 예외를 사용해라

- 오류코드를 반환하면 분기가 많아지고 코드가 복잡해져서 가독성이 떨어진다.

### Checked Exception을 쓰면 안되는 이유

![image](https://t1.daumcdn.net/thumb/R720x0/?fname=http://t1.daumcdn.net/brunch/service/user/xTa/image/EV_9X_vwDtwXTsoqr6cviQLUTAg.png)

 - Error의 경우 시스템의 비정상적 문제 때문에 발생하므로 예외처리 코드를 통한 수습이 불가능하다. 따라서 RuntimeException을 일반적으로 Unchecked Exception으로 여기면 된다.
 
- RuntimeException을 상속하면 명시적 예외처리가 필요하지 않다.

<br>

- 하위 메서드에서 발생하는 Checked Exception은 상위 메서드들에게도 check를 강요(throw든 try catch든)하므로 **OCP를 위반**한다.
- C#, C++, 파이썬, 루비는 Checked Exception이 존재하지 않지만 안정적인 소프트웨어를 구현하는데 아무런 무리가 없다.
- Checked Exception은 불필요한 비용만 만드므로 Unchecked Exception을 사용하도록 하자
- 만약 Checked Exception이 발생하는경우 이를 try-catch로 먹고 Unchecked Exception을 던져서 처리해보자

## 예외를 잘 쓰는 방법
### 예외에 메시지 담기
- 예외를 던질때 의미있는 정보를 담는다.
- 오류가 발생한 위치, 전후상황을 모두 덧붙이기
- 실패한 연산 이름, 유형등 정보도 포함

### Exception Wrapper 사용
- 여러 Exception이 발생할 경우 이를 묶어서 한번에 처리가능한 Wrapper 클래스를 작성하여 가독성을 높이자
### 커스텀 예외 클래스 사용
- 커스텀 예외클래스를 사용하면 외부 라이브러리가 아닌 현재 프로젝트상에서 에러가 터졌음을 명확히 파악 가능하여, 보다 빠른 디버깅이 가능
- 우리 시스템에서 발생한 에러 종류를 나열가능하여 관리가 수월해진다.

## 실무에서 예외 처리하는 패턴
### getOrElse 패턴
- 예외가 아닌 기본 값을 리턴
    - null이 아닌 기본 값
    - 도메인에 맞는 기본값
 ### getOrElseThrow 패턴
- null 대신 예외를 던진다(null을 던지는 것은 최대한 피하자)

### 파라미터의 null 점검
- 메서드 전면부에 주입되는 파라미터의 null 체크를 해주는것이 좋다.
