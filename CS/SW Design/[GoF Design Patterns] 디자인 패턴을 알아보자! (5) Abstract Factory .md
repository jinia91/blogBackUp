# Abstract Factory 란?
## 추상 팩토리 패턴 정의, Intend

![image](https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure.png)

> 다양한 구성 요소를 가진 `객체 집합`, 혹은 객체 집합을 가지고 있는 `복합객체`를 생성하는 과정을 캡슐화하는 패턴

## 왜 필요한가? Pros

- 하나의 목적을 가진 여러 객체들을 만들때, 각각의 객체들을 개별적인 클래스에 의존해서 만들지 않고, 추상 팩토리 구현체만 의존하여 만들수 있게 해준다.
- 하나의 추상 팩토리안에서 정의된 구성요소들을 클라이언트는 만들거나 사용하기만 하면 되기때문에, 구성요소들이 서로 같은 목적인지 의심할 필요가 없다.
- 다른목적을 가진, 새로운 객체 집합을 만들 필요가 있을때, 기존 코드를 건드리지않고 새로운 추상팩토리 구현체만 만들면 되므로`OCP`를 준수한다.

## 문제점은? Cons

- 수많은 인터페이스와 클래스를 정의해야 하므로 코드가 복잡해진다.

- `SRP`관점에서 추상 팩토리가 여러 객체를 생성하게 되므로 여러 책임을 가졌다고 해석할 여지가 있다.

# 구현방법

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/65614c1e-3a64-4306-954f-a4144a23569c.png?raw=true)

## Abstract Factory 

        public interface PolicyAbstractFactory {
    
            BrokeragePolicy createBrokeragePolicy();
            OtherPolicy createOtherPolicy();
    
        }
        

        public class PurchasePolicyFactoryImpl implements PolicyAbstractFactory{
            @Override
            public BrokeragePolicy createBrokeragePolicy() {
                return new PurchaseBrokeragePolicy();
            }
        
            @Override
            public OtherPolicy createOtherPolicy() {
                return new PurchaseOtherPolicy();
            }
        }
    

        public class RentalPolicyFactoryImpl implements PolicyAbstractFactory{
            @Override
            public BrokeragePolicy createBrokeragePolicy() {
                return new RentalBrokeragePolicy();
            }
        
            @Override
            public OtherPolicy createOtherPolicy() {
                return new RentalOtherPolicy();
            }
        }

## Abstract Factory Client

    public class PolicyAbstractFactoryClient {
    
        private PolicyAbstractFactory policyAbstractFactory;
    
        public PolicyAbstractFactoryClient(PolicyAbstractFactory policyAbstractFactory) {
            this.policyAbstractFactory = policyAbstractFactory;
        }
    
        public BrokeragePolicy createBrokeragePolicy(){
            return policyAbstractFactory.createBrokeragePolicy();
        }
    
        public OtherPolicy createOtherPolicy(){
            return policyAbstractFactory.createOtherPolicy();
        }
    }
    

## 설명

두 정책군`BrokeragePolicy, OtherPolicy`을 `Rental` 과 `Purcahse` 두가지 목적에 따라 생성하는 추상 팩토리 구현체를 작성하여

추상 팩토리 클라이언트가 각각의 목적에 따라 추상팩토리 구현체에 대한 의존성을 주입받으면, 해당 정책군 두개는 구체적인 의존성 없이 생성하도록 구현했다.

팩토리 메서드 패턴과 상당부분 유사하며, 해당 코드를 사용하는 클라이언트에 초점을 맞춘 패턴으로 생각하면 좀더 이해하기 쉽다.


**사용 코드**

    public class MainClient {
    
        public static void main(String[] args) {
            PolicyAbstractFactoryClient policyAbstractFactoryClient = new PolicyAbstractFactoryClient(new PurchasePolicyFactoryImpl());
    
            BrokeragePolicy purchaseBrokeragePolicy = policyAbstractFactoryClient.createBrokeragePolicy();
            OtherPolicy purchaseOtherPolicy = policyAbstractFactoryClient.createOtherPolicy();
    
            }
    }
 
# 다른 패턴과의 관계

- 추상 팩토리 패턴은 보통 팩터리 메서드 패턴으로 이루어져있지만, 프로토타입 패턴을 사용해서도 구현가능
- 추상 팩토리 패턴은 파사드 패턴의 대안으로 사용될 수도 있다.
- 추상 팩토리 패턴과 브릿지 패턴을 같이 사용하여 코드의 복잡성을 감추는 방법도 존재한다.

# 실무에서 어떻게 사용되는지 사례
- javax.xml.parsers.DocumentBuilderFactory
- javax.xml.transform.TransformerFactory
- 스프링 FactoryBean과 그 구현체

# [학습예제 코드는 깃허브에](https://github.com/jinia91/DesignPattern/tree/main/src/abstractfactory)

# References

- [https://refactoring.guru/design-patterns/abstract-factory](https://refactoring.guru/design-patterns/abstract-factory)
- [위키백과 - 추상 팩토리 패턴](https://ko.wikipedia.org/wiki/%EC%B6%94%EC%83%81_%ED%8C%A9%ED%86%A0%EB%A6%AC_%ED%8C%A8%ED%84%B4)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)

