# Factory Method란?
## 팩토리 메서드 정의, Intend
![image](https://refactoring.guru/images/patterns/diagrams/factory-method/example.png)
> 구체적인 객체 생성(`concrete product`)을 하위 클래스(`concrete creator`)에서 구현하여, 부모 클래스(`creator`)가 생성되는 구체 클래스(`concrete product`)를 모르게 캡슐화하는 패턴


## 왜 필요한가?  Pros
- 좋은 객체지향 코드를 위해서는 `OCP`, `SRP`를 준수해야한다.
- 만약 새로운 상품(`product`)가 추가됬을때, 기존에 코드들이 변경된다면 이는 좋은 객체지향 프로그래밍이라고 볼 수 없다.
- 팩토리 메서드 패턴을 사용하면, - `creator`와 `product`간의 결합도를 낮추므로서 , 위와같은 생성로직에서 새로운 아이템이 추가되더라도 기존코드를 거의 손대지 않아도 된다.(`OCP`)

- 단일책임원칙을 고려할 때, 구체적인 `product`를 생성하는 책임은 구체적인 `creator`가 맡는것이 바람직하다.

## 문제점은? Cons

- 많은 하위 클래스를 작성해야하므로 애플리케이션의 코드 복잡도가 올라간다.(추상 골격 클래스까지 작성하게되면 고계층 구조가 되어 더욱 복잡해짐)

# 구현방법

## sol.1 Simple Factory Method 패턴

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/29d664d8-5fe9-4e31-ad6d-6653c0fc192b.png?raw=true)

### Product

    // 상위 Product
    public interface BrokeragePolicy {
    
        default long calculate(Long price) {
            BrokerageRule rule = createBrokerageRule(price);
            return rule.calculateMaxBrokerage(price);
        }
    
        BrokerageRule createBrokerageRule(Long price);
    }
    
        /*
     * 매매일때 중개 수수료를 계산해주는 클래스 Concrete Product 1
     * */
    public class PurchaseBrokeragePolicy implements BrokeragePolicy {
    
        public BrokerageRule createBrokerageRule(Long price) {
            BrokerageRule rule;
            if(price < 50_000_000){
                rule = new BrokerageRule(0.6,250_000L);
            } else if(price < 200_000_000){
                rule = new BrokerageRule(0.5,800_000L);
            } else if(price < 900_000_000){
                rule = new BrokerageRule(0.4,null);
            } else if(price < 1_200_000_000){
                rule = new BrokerageRule(0.5,null);
            } else if(price < 1_500_000_000){
                rule = new BrokerageRule(0.6,null);
            } else {
                rule = new BrokerageRule(0.7,null);
            }
            return rule;
        }
    
    /*
    * 임대일때 중개 수수료를 계산해주는 클래스 Concrete Product 2
    * */
    public class RentalBrokeragePolicy implements BrokeragePolicy{
    
        public BrokerageRule createBrokerageRule(Long price) {
            BrokerageRule rule;
            if(price < 50_000_000){
                rule = new BrokerageRule(0.5,200_000L);
            } else if(price < 200_000_000){
                rule = new BrokerageRule(0.4,300_000L);
            } else if(price < 900_000_000){
                rule = new BrokerageRule(0.3,null);
            } else if(price < 1_200_000_000){
                rule = new BrokerageRule(0.4,null);
            } else if(price < 1_500_000_000){
                rule = new BrokerageRule(0.5,null);
            } else {
                rule = new BrokerageRule(0.6,null);
            }
            return rule;
        }
    
### Creator

    // 상위 creator 
    public interface BrokeragePolicyFactory {
        BrokeragePolicy of(ActionType actionType);
    }
    

    // Concrete Creator
    public class SimpleBrokeragePolicyFactoryImpl implements BrokeragePolicyFactory {
        public BrokeragePolicy of(ActionType actionType){
            switch (actionType){
                case PURCHASE:
                    return new PurchaseBrokeragePolicy();
                case RENT:
                    return new RentalBrokeragePolicy();
                default:
                    throw new IllegalArgumentException("No Valid Policy");
            }
        }
    }
    

### 설명

팩토리 메서드 패턴을 단순하게 구현한 방식으로 추상화 레벨이 같은 `Creator`는 `Product`만을 의존하며, `Concrete Creator`인 `SimpleBrokeragePolicyFactoryImpl`가 `Concete Product`들을 의존하는 구조다.

따라서 클라이언트는 `Creator`만 알고있으면(DI가 있다는 가정하에), `Product`를 받아올 수 있다.

    public class Client {
    
        public static void main(String[] args) {
            orderAndCalculateBrokerageAmount(PURCHASE, 100_000_000L);
            orderAndCalculateBrokerageAmount(RENT, 500_000_000L);
    
        }
    
        private static void orderAndCalculateBrokerageAmount(ActionType actionType, long price) {
            SimpleBrokeragePolicyFactoryImpl brokeragePolicyFactory = new SimpleBrokeragePolicyFactoryImpl();
            BrokeragePolicy brokeragePolicy = brokeragePolicyFactory.of(actionType);
            long calculatedBrokerageAmount = brokeragePolicy.calculate(price);
            System.out.println("calculatedBrokerageAmount = " + calculatedBrokerageAmount);
        }
    } 


### 단점

해당 방식은 `SimpleBrokeragePolicyFactoryImpl`가 

1. `ActionType`을 판별해 
2. `Product`를 생산

하고 있기 때문에 두가지 역할을 하고으며 `SRP`를 준수한다고 볼 수 없다.

또한 새로운 상품이 추가될 경우,  `SimpleBrokeragePolicyFactoryImpl`에도 `case` 로직이 추가되므로 진정한 의미로 `OCP`를 준수했다고 말하긴 힘들다.

## sol.2 Factory Method 패턴

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/b3021d56-cac7-4087-927a-0391341ea310.png?raw=true)

### creator
    // concrete Product - PurchaseBrokeragePolicy 를 만들기 위한 concrete Creator
    public class PurchaseBrokeragePolicyFactoryImpl implements BrokeragePolicyFactory {
        public PurchaseBrokeragePolicyFactoryImpl() {
        }
    
        public BrokeragePolicy of(ActionType actionType){
            if(actionType.equals(ActionType.PURCHASE)) {
                return new PurchaseBrokeragePolicy();
            }
            throw new IllegalArgumentException();
        }
    }

    // concrete Product - RentalBrokeragePolicy 를 만들기 위한 concrete Creator 
    public class RentalBrokeragePolicyFactoryImpl implements BrokeragePolicyFactory {
        public BrokeragePolicy of(ActionType actionType){
            if(actionType.equals(ActionType.RENT)) {
                return new RentalBrokeragePolicy();
            }
            throw new IllegalArgumentException();
        }
    }
    

    // 요청에 따라 어떤 concrete creator를 반환할지 매핑정보 저장소
    static Map<ActionType, BrokeragePolicyFactory> createConcreteFactory;
    static {
        createConcreteFactory = new HashMap<>();
        createConcreteFactory.put(PURCHASE,new PurchaseBrokeragePolicyFactoryImpl());
        createConcreteFactory.put(RENT,new RentalBrokeragePolicyFactoryImpl());
    }

### 설명

sol.1의 문제점을 해결하기 위해 각 `Concrete Product` 별로 `Concrete Creator`를 구현한 모습이다.

이 방법을 통해 구현하면 `SRP`를 준수하여 보다 객체지향적인 코드가 된다.

하지만 위의 코드에서는 요청에 따라 어떤 `Concrete Creator`를 반환해야 할지 누군가는 알아야 하므로  해당 매핑정보를 저장하고 객체를 알고있는 `Map`을 별도로 사용했는데,

`OCP`를 생각하면 새로운 `Product`가 추가될 때, 이 매핑정보에도 수정이 필요하므로 진정한 의미의 `OCP`준수라고 볼 수 는 없다.

이 부분은 프레임워크나 라이브러리 단위에서 `IOC`와 `DI`를 통해 해결하도록 하자.


    public class Client2 {
        static Map<ActionType, BrokeragePolicyFactory> createConcreteFactory;
        static {
            createConcreteFactory = new HashMap<>();
            createConcreteFactory.put(PURCHASE,new PurchaseBrokeragePolicyFactoryImpl());
            createConcreteFactory.put(RENT,new RentalBrokeragePolicyFactoryImpl());
        }
    
        public static void main(String[] args) {
            orderAndCalculateBrokerageAmount(PURCHASE, 100_000_000L);
            orderAndCalculateBrokerageAmount(RENT, 500_000_000L);
        }
    
        private static void orderAndCalculateBrokerageAmount(ActionType actionType, long price) {
    
            BrokeragePolicyFactory brokeragePolicyFactory = createConcreteFactory.get(actionType);
            BrokeragePolicy brokeragePolicy = brokeragePolicyFactory.of(actionType);
            long calculatedBrokerageAmount = brokeragePolicy.calculate(price);
            System.out.println("calculatedBrokerageAmount = " + calculatedBrokerageAmount);
        }
    }   

# 다른 패턴과의 관계
- 많은 설계가 최초엔 팩토리 메서드 패턴을 사용해서 구현되지만, 점차 추상팩토리, 프로토타입 혹은 빌더패턴으로 변화해가는 경향이 있다.
- 추상 팩토리 클래스들은 종종 여러 팩토리 메서드 패턴을 가지고 만들어지기도 한다.
- 팩토리 메서드 패턴은 템플릿 메서드 패턴의 생성패턴이라고 볼 수 있다. 

# 실무에서 어떻게 사용되는지? 실제 사용된 사례

- java.util.Calendar
- java.util.ResourceBundle
- java.text.NumberFormat
- java.util.EnumSet

## [깃허브에서 학습 코드 보기](https://github.com/jinia91/DesignPattern/tree/main/src/factorymethod)

# References

- [https://refactoring.guru/design-patterns/factory-method](https://refactoring.guru/design-patterns/factory-method)
- [위키백과 - 팩토리 메서드 패턴](https://ko.wikipedia.org/wiki/%ED%8C%A9%ED%86%A0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C_%ED%8C%A8%ED%84%B4)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
- [https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html](https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html)