# Singleton 패턴이란? 
## 싱글톤 정의, Intend
> Singleton이란 한번에 하나의 사건이 일어난다, 하나의 패만 보여준다라는 뜻이며, SW공학, OOP에서 싱글톤은 해당 객체가 단 하나임을 보장한다는 의미를 뜻한다. 싱글톤 패턴은 이러한 싱글톤을 보장하는 패턴을 가리킨다.

![image](https://better-dev.netlify.app/assets/images/study/dev/2018/8_Singleton-pattern-class-diagram.jpg)

## 왜 필요한가? Pros

객체를 생성하는 것은 비용이 드는 행위다. 기본적인 연산, 객체가 생성되는 힙 영역의 메모리, 그리고 소요 시간까지.

하지만 어플리케이션을 구성하는데 있어서 객체가 단 하나만 있어도 되는 경우는 꽤 많으며 객체가 싱글톤임을 보장한다면 위에서 말한 많은 비용을 줄일 수 있게 된다. 

또한 싱글턴 객체는 전역에서 접근가능한(global) 객체이므로, 해당 객체에 상태(field)가 존재하면 이를 공유하기 매우 편해진다.


## 문제점은? Cons

싱글톤 방식은 여러 클라이언트가 하나의 같은 객체를 공유하기 때문에 상태를 유지(stateful)하게 설계하면 안된다.

즉 싱글톤객체를 설계할 때는 무상태(stateless)를 지향하여 설계해야만 한다.

여기서 무상태란

- 싱글톤 객체는 특정 클라이언트에 의존적인 필드가 있으면 안된다.
- 특정 클라이언트가 값을 변경할 수 있는 필드가 있어서는 안된다.
- 가급적 읽기만 가능해야 한다.

만약 싱글톤 객체가 공유필드를 갖고있다면 멀티쓰레드 환경에서 동시성 문제가 발생하게된다.

또한 일반적으로 기본 생성자를 private으로 닫아두게 되는데, 이러면 상속이 불가능해진다.

# 구현방법

### Sol.1 가장 간단한 구현 패턴

    /**
     * 기본적인 싱글턴 패턴
     */
    public class Singleton1 {
    
        private static Singleton1 singleton1Instance;
    
        private Singleton1() {
        }
    
        public static Singleton1 getInstance() {
            if(singleton1Instance == null){
                singleton1Instance = new Singleton1();
            }
            return singleton1Instance;
        }
    }
    
- 기본 생성자를 private으로 접근제한하고, static 팩터리 메서드 방식으로 객체 생성을 구현한다.
- 이때 객체를 static으로 클래스에 참조시켜 global한 접근을 허용하게하고, null체킹을 통해 객체 중복생성을 방지한다.

**문제점**

- 멀티쓰레드 환경에서 최소 생성 요청시 동시성 문제가 발생할 수 있다.

      public static synchronized Singleton1 getInstance() {
                if(singleton1Instance == null){
                    singleton1Instance = new Singleton1();
                }
                
- `synchronized` 구문 추가시 동시성문제를 해결할 수는 있지만 모든 `getInstance()` 호출마다 동기화처리를 하기때문에 성능 저하가 불가피하다.

### sol.2 즉시 초기화 싱글턴
    public class Singleton2 {
    
        private final static Singleton2 singleton1Instance;
    
        private Singleton2() {
        }
    
        public static Singleton2 getInstance() {
            return singleton1Instance;
        }
    }

- 어플리케이션이 구동되는 클래스 로드 시점에서 객체 초기화를 하는 방식
- 최초에 이미 객체를 생성하고 참조주소만 반환하기때문에, 동시성 문제에서 해방된다.

**문제점**

- 객체를 호출하는 시점이 아니라 어플리케이션이 구동되는 시점부터 객체를 생성하기 때문에, 메모리의 불필요한 낭비로 볼 수도 있다.

### sol.3 지연 초기화 - 이중검사 관용구를 사용한 싱글턴패턴

        public class Singleton3 {
        
            private static volatile Singleton3 singleton3Instance = new Singleton3();
        
            private Singleton3() {
            }
        
            public static Singleton3 getInstance() {
                if(singleton3Instance == null){
                    synchronized (Singleton3.class) {
                     if(singleton3Instance == null)
                        singleton3Instance = new Singleton3();
                    }
                }
        
                return singleton3Instance;
            }
        }
    
[이펙티브 자바 Item. 83에서 제시된 방법](https://www.jiniaslog.co.kr/article/view?articleId=110)으로 지연 초기화시, null 체킹을 2번하되 2번째에만 동기화처리를 함으로써 최초 초기화 이후부터는 동기화 없는 null체킹을 통해 성능저하를 피하는 방식이다.

**문제점**

2중 null 체킹과 volatile 키워드 등 구문이 너무 복잡해지는 느낌이다.

### sol.4 static inner class를 사용한 싱글턴패턴

        public class Singleton4 {
        
            static class InstanceHolder{
                private static Singleton4 singleton3Instance = new Singleton4();
            }
        
            public static Singleton4 getInstance() {
                return InstanceHolder.singleton3Instance;
            }
        }
        

static inner 클래스의 경우 해당 클래스를 호출하는 시점에서 로딩이 일어나고 그때 내부 인스턴스가 static으로 초기화되기때문에, 손수비게 지연 초기화 싱글턴을 구현할 수 있다.

### 그럼에도 문제점...

싱글턴 패턴은 싱글턴임을 보장하는 패턴임에도, 클라이언트는 크게 두가지 방법으로 싱글턴을 깨뜨릴 수 있다.

### 1. Refletion으로 private 생성자 접근하기

자바 리플렉션 api를 사용하면 prviate에 자유롭게 접근 가능하기 때문에 이를 통해 캡슐화된 객체에 접근하여 생성자를 강제로 호출시킬 수 있다.

   
### 2. 직렬화는 캡슐화를 깨뜨린다

[이펙티브 자바 Item. 89에서는](https://www.jiniaslog.co.kr/article/view?articleId=111) 직렬화를 통해 싱글턴이 깨질수 있음을 보여준다.

물론 본 책에서는 readResolve() 메서드를 사용해 인스턴스 숫자 통제가 가능함을 알려준다.


### sol.5 Enum Type으로 싱글턴 구현

        public enum Singleton5 {
            INSTANCE5;
        }
    
[이펙티브 자바 Item. 89와 Item. 3 에서 제시된 방법으로 Enum을 통한 싱글턴 구현 코드다.](https://www.jiniaslog.co.kr/article/view?articleId=111)

Enum은 자바 언어 차원에서 해당 객체가 단 하나임을 보장하기 때문에, Enum을 통해 싱글턴 객체를 구현하면 Reflection을 통한 생성자 접근이나, 직렬화시 객체 재생성을 원천적으로 차단할 수 있다.

**문제점**

Enum 타입의 용도를 볼때, 과연 이 타입을 가지고 싱글턴의 클래스를 정의하는 것이 옳은 방법일지 고민해 볼 필요가 있다.

혹 구현할 필요가 있다면 팀의 컨벤션과 협의에 따라 판단하도록 하자.

# 다른패턴과의 관계

- 파사드 패턴(Facade)은 일반적으로 싱글톤으로 구현되는 편이다.
- 경량 패턴(Flyweight)은 객체의 공유 상태를 관리하기 위해 싱글톤으로 구현되곤 한다. 다만 반드시 싱글턴일필요는 없으며 필요에따라 멀티턴(multiton pattern)으로도 가능하다.
- 추상 팩토리, 빌더, 프로토타입패턴등의 구현체가 싱글턴으로 구현되기도 한다.

# 실무에서 실제로 어디서 사용되는지?

- 대표적으론 스프링의 IOC컨테이너가 빈을 싱글턴 스코프로 관리한다.
- java.lang.Runtime


