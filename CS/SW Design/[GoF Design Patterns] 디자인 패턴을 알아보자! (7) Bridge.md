# Bridge 란?
## 브릿지 패턴 정의, Intend
> 브릿지 패턴은 구조적 디자인 패턴에 속하는 카탈로그로서, 하나의 거대한 클래스나, 기능부/구현부로만 분리되어있던 어플리케이션을, 기능부/ 구현부 각각 독립적인 계층화 를 통해 발전시켜 나가는 설계방식을 말한다.


## 왜 필요한가? Pros

- 좋은 객체지향 코드를 위해서는 구현부와 기능부를 분리하는 것이 가장 중요하다.
- 어플리케이션이 커지고 로직이 복잡해지면 기능부(interface)도 여러가지 상황에 대응할수 있도록 확장이 필요해진다.(다기종 플랫폼, 여러 외부라이브러리 사용 등등)
- 구현부 역시 알고리즘(전략)에 따라 다양한 코드들이 작성되고 확장이 필요해진다.
- 이때 브릿지 패턴은 기능부와 구현부를 **객체 결합을 통해 연결**(Composite)하고, 각각을 고도로 계층화시킴으로서 기능부와 구현부가 독립적이고 유연한 확장이 가능한 솔루션을 제공한다.
- 독립적인 구조덕분에 OCP와 SRP를 준수할 수 있다. 
## 문제점은? Cons

- 응집도 높은 클래스들을 추상화하는 과정에서 클래스가 늘어나므로, 프로그램 복잡도는 증가한다.

# 구현방법

## 시나리오

   > 마스터/비자 신용카드 결제 모듈을 개발하고 운영하고 있는 상황이다. 그런데 기술기 발전함에 따라, 마스터와 비자의 결제 서비스에 의존하면서도 새로운 결제방식인 애플페이와, 삼성페이가 나타났다! 거기다가 유니온페이도 등장한 상황! 모듈 구조를 어떻게 짜야할까?


## before

### CreditcardController(신용카드 결제 인터페이스)

    package bridge.ver1_before;
    
    public class CreditCardPaymentController {
    
        private final MasterCardPaymentService masterCardPaymentService = new MasterCardPaymentService();
        private final VisaCardPaymentService visaCardPaymentService = new VisaCardPaymentService();
    
        public String doPayment(String payMethod) {
            if(payMethod.equals("MASTER")){
                return masterCardPaymentService.doService();
            }
            if(payMethod.equals("VISA")){
                return visaCardPaymentService.doService();
            }
            throw new IllegalArgumentException("결제 지원 안함");
        }
     }

### 결제 전략(Service)
    package bridge.ver1_before;
    
    public class MasterCardPaymentService {
        public String doService() {
            return "마스터카드를 사용해 지불";
        }
    }

    public class VisaCardPaymentService {
        public String doService() {
            return "비자카드를 사용해 지불";
        }
    }
    
### 설명

시나리오대로 신용카드를 이용해 마스터/비자 전략으로 결제하는 모듈을 구현했다.

애플리케이션의 아키텍쳐  측면에서 클라이언트측인 controller와 , 서버측인 service는 각각 결제라는 기능과, 결제 방법이라는 구체적인 구현이 분리되어있다고 볼 수 있다.

## 시나리오대로 일단개발해보자


### 애플페이와 삼성 페이 결제기능

    public class ApplePayPaymentController {
    
        private final MasterCardPaymentService masterCardPaymentService = new MasterCardPaymentService();
        private final VisaCardPaymentService visaCardPaymentService = new VisaCardPaymentService();
        private final UnionPayCardPaymentService unionPaymentService = new UnionPayCardPaymentService();
        private final String USECASE = "애플페이로";
    
        public String doPayment(String payMethod) {
            if(payMethod.equals("MASTER")){
                return USECASE + masterCardPaymentService.doService();
            }
            if(payMethod.equals("VISA")){
                return USECASE + visaCardPaymentService.doService();
            }
            if(payMethod.equals("UNION")){
                return USECASE + unionPaymentService.doService();
            }
            throw new IllegalArgumentException("결제 지원 안함");
        }
    }
    

    public class SamsungPayPayPaymentController {
    
        private final MasterCardPaymentService masterCardPaymentService = new MasterCardPaymentService();
        private final VisaCardPaymentService visaCardPaymentService = new VisaCardPaymentService();
        private final UnionPayCardPaymentService unionPaymentService = new UnionPayCardPaymentService();
        private final String USECASE = "삼성페이로";
    
        public String doPayment(String payMethod) {
    
            if(payMethod.equals("MASTER")){
                return USECASE + masterCardPaymentService.doService();
            }
            if(payMethod.equals("VISA")){
                return USECASE + visaCardPaymentService.doService();
            }
            if(payMethod.equals("UNION")){
                return USECASE + unionPaymentService.doService();
            }
            throw new IllegalArgumentException("결제 지원 안함");
        }
    }
    
### 신용카드 컨트롤러 수정

    public String doPayment(String payMethod) {
            if(payMethod.equals("MASTER")){
                return USECASE + masterCardPaymentService.doService();
            }
            if(payMethod.equals("VISA")){
                return USECASE + visaCardPaymentService.doService();
            }
            if(payMethod.equals("UNION")){
                return USECASE + unionPaymentService.doService();
            }
            throw new IllegalArgumentException("결제 지원 안함");
        }
    }

### 유니온결제전략 추가

    public class UnionPayCardPaymentService {
        public String doService() {
            return "유니온페이카드를 사용해 지불";
        }
    }
    

### 설명

새로운 결제 기종(device, platform)인 애플페이와 삼성페이 등장으로 해당 플랫폼에 적합한 인터페이스를 구현해야했고

여기에 새로운 결제전략인 유니온결제가 추가되면서 기존 인터페이스도 수정이 일어났다.

코드를 보면 굉장히 많은 중복과, 수정으로인한 다른 클래스 수정(ocp 위반)이 일어남을 확인할 수있다.

만약 여기서 모듈이 워낙 잘팔려서 네이버페이, 카카오페이와 같은 전자결제 플랫폼이 등장하고, 결제전략이 10개가 추가된다면?!

어마어마한 중복작업과 클래스들의 난잡한 의존관계에 직면하게 될 것이다. 
## 브릿지 패턴과 전략패턴을 사용해 구조화
## 브릿지 패턴과 전략패턴을 사용해 구조화


### 기능부의 고도화, Payment abstract class

    public abstract class PaymentController {
    
        private PaymentService paymentService;
    
        public PaymentController(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    
        public PaymentService getPaymentService() {
            return paymentService;
        }
    
        public void changePaymentService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    
        public abstract String doPayment();
    }
    
### 구현부의 고도화, PaymetService interface

    public interface PaymentService {
        String  doService();
    }
    
### 설명
변화한 코드가 많기 때문에 [깃허브에서 코드 전문을 꼭 확인](https://github.com/jinia91/DesignPattern/tree/main/src/bridge/ver3_after)
하길 바란다.

기능부를 추상클래스로 계층화하는 한편, 구현부도 PaymentService 인터페이스를 분리하여 계층화를 하였다.

![image](https://refactoring.guru/images/patterns/diagrams/bridge/structure-en.png?id=827afa4b40008dc29d26fe0f4d41b9cc)


의존성을 모두 외부에서 주입받는 방식으로 코드를 바꿨기때문에, 기능부는 모두 구현부의 인터페이스만 바라보게되고, 구현부는 변경에 훨씬 자유로워진다.

또한 계층화를 통해 중복로직을 덜어냄으로서 기능부의 추가 역시 ocp를 위반하지 않고 행할수 있게된다!


# 다른 패턴과의 관계
# 실무에서 어떻게 사용되는지 사례

- 브릿지 패턴은 일반적으로 설계단계에서 도입되는 패턴인 반면, 어댑터 패턴은 기존 앱과 호환되지 않는 외부 모듈을 부착하기 위해 사용된다.
- 브릿지, 상태패턴, 전략패턴은 그 생김새가 매우 비슷하다.(똑같다고 말할수도 있다). 실제로 세 패턴은 기본적으로 협업하는 객체간 결합을 기반으로 하고 있다. 하지만 세 패턴은 `직면한 문제와 그 문제를 해결하려는 의도` 면에서 차이를 보이며, 디자인패턴은 구조가 아니라 `문제 해결 의도`를 파악하는게 핵심이다!
- 추상 팩토리 패턴이나 빌더패턴에서도 브릿지 패턴을 섞어 더 캡슐화된 설계를 해볼 수 있다.


# [학습예제 코드는 깃허브에](https://github.com/jinia91/DesignPattern/tree/main/src/bridge/ver3_after)

# References

- [https://refactoring.guru/design-patterns/bridge](https://refactoring.guru/design-patterns/bridge)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)

