# Command Pattern?
> 커맨드패턴은 정보 전문가(`information expert`)라는 객체에게 요청을 맡기는 패턴으로 디자인 패턴중 행위 패턴에 해당한다.

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/892d134e-eab2-4bfa-848b-c102e9715fee.png)

- Command 패턴에서 중요한 책임을 가진 객체들은 총 4개로 `Invoker`, `Receiver`, `Client`, 그리고 `Command`가 있다.
- `Client` 는 `Context`와 결합되어, 객체들을 생성하거나 오케스트레이션하여 진행하는 역할을 한다.
- `Command`는 요청을 캡슐화하고 요청을 실행할 `Reciever`를 알고 있으며, 요청을 하는 역할을 한다.
- `Invoker`는 추상화된 `Command`를 주입받을수 있고, 이를 실행하는 일을 하며, `Template Method` 패턴으로서 다른 부수적인 책임을 수행하기도 한다
- `Reciver` 는 실질적으로 요청을 실행하는 일을 한다.

  
## 왜 필요한가? Pros
- 단순히 `Client`와 `Server`의 역할에서 벗어나 보다 구체적인 책임을 할당함에 따라, `SRP`를 준수하게된다.
- 책임에 따라 작게 분할된 객체들은 `OCP`를 준수하기 더 쉬워진다
- `Invoker`의 패턴에 따라 부수적인 다양한 기능을 구현하기 수월하다.
    - `Undo, Logging` 등
 - 기존의 요청들을 조합하는 복잡한 요청이 새로 생겨도, 여러 `Command`를 조합하여 구현 가능하다.
 

## 문제점은? Cons

- 단순히 `Server`와 `Client`가 존재하는것에 비해 여러 계층이 추가되므로 코드가 복잡해진다.

# 구현 코드

## Ver 1. Before

### Controller(Server)

    public class Controller {
        private final Service1 service1;
    
        public Controller(Service1 service1) {
            this.service1 = service1;
        }
    
        public void doService(){
            service1.doIt();
        }
    }
    
### Services(Server)
    public class Service1 {
        public void doIt() {
            System.out.println("do it! Service1");
        }
    }
    
    public class Service2 {
        public void doIt() {
            System.out.println("do it! Service2");
        }
    }

### Client

    public class Client {
    
        public static void main(String[] args) {
            Controller controller = new Controller(new Service1());
            controller.doService();
    //        new Controller(new Service2());
        }
    }
    
### 설명

간단한 `Client` - `Server(Controller)` 구조의 수도코드를 통해 `Command` 패턴을 구현해보자.



## Ver 2. Command 패턴으로

### Controller(`Invoker`)
    
    public class Controller {
        private final Stack<ServiceCommand> commands = new Stack<>();
    
        public void handle(ServiceCommand command){
            commands.push(command);
            command.doService();
        }
    
        public void revert(ServiceCommand command){
            ServiceCommand revertCommand = commands.pop();
            revertCommand.unDoService();
        }
    }
    
### ServiceCommand와 구현체

#### ServiceCommand 인터페이스

    public interface ServiceCommand {
        void doService();
        void unDoService();
    }

#### Service 구현체(`Reciever`)
    
    
    public class Service1 {
        public void start() {
            System.out.println("Service1 start!");
        }
        public void end() {
            System.out.println("Service1 end!");
        }
    }
    
#### Command

    
    public class Service1EndCommand implements ServiceCommand {
        private final Service1 service1;
    
        public Service1EndCommand(Service1 service1) {
            this.service1 = service1;
        }
    
        @Override
        public void doService() {
            service1.end();
        }
    
        @Override
        public void unDoService() {
            new Service1StartCommand(this.service1).doService();
        }
    }# 다른 패턴과의 관계

    
    public class Service1StartCommand implements ServiceCommand {
            private final Service1 service1;
    
        public Service1StartCommand(Service1 service1) {
            this.service1 = service1;
        }
    
    
        @Override
        public void doService() {
            service1.start();
        }
    
        @Override
        public void unDoService() {
            new Service1EndCommand(this.service1).doService();
        }
    }

### Client
    
    public class Client {
        public static void main(String[] args) {
            Controller controller = new Controller();
            Service1StartCommand c1 = new Service1StartCommand(new Service1());
            Service2StartCommand c2 = new Service2StartCommand(new Service2());
            Service1EndCommand c3 = new Service1EndCommand(new Service1());
            Service2EndCommand c4 = new Service2EndCommand(new Service2());
            controller.handle(c1);
            controller.handle(c2);
            controller.handle(c3);
            controller.handle(c4);
    
        }
    }
    
### 설명
기존의 코드는 `Controller(Invoker)`가 실제 요청을 수행하는 `Service1(Reciever)`를 알고있으며, 만약 다른 요청을 수행하기 위해서는 `Service2(Reciever)`를 추가로 알아야하는 결합도가 강한 구조였다.

Ver 2.는 `Invoker`와 `Reciver` 사이에 실제 요청에 대한 정보 전문가 `Command` 계층을 추가하여, `Invoker`는 추상화된 `Command`에 대한 Do/Undo 만 수행토록하고, 구체적인 요청에대해서는 `Stack`구조로 주입받도록 구현하여 `Invoker`와 `Reciver` 간 결합도를 낮추었다.



# 다른 패턴과의 관계
- 책임연쇄패턴, 커맨드패턴, 중재자패턴, 옵저버 패턴은 모두 요청에 대한 수신자와 송신자간 다양한 결합에 대한 패턴들이다.
    - 책임연쇄패턴은 동적으로 연결된 체인에 요청을 순차적으로 전달하는 패턴이다.
    - 커맨드 패턴은 수신자와 송신자를 오직 단방향으로만 연결하는 패턴이다.
    - 중재자 패턴은 송신자와 수신자간 방향성을 제거하고 중재자 객체가 그 사이에서 소통을 관장한다.
    - 옵저버 패턴은 수신자가 동적으로 구독/구독해제를 통해 메시지 수신을 결정할 수 있게한다.

- 커맨드 패턴과 메멘토 패턴을 사용하여 `Undo` 기능을 구현할 수 있다.

- 커맨드 패턴과 전략 패턴은 구조적으로 매우 유사하지만, 의도가 다르다.
    - 전략패턴은 하나의 `Context`안에 다른 알고리즘이 필요할때 사용하는 패턴이지만, 커맨드 패턴은 기본적으로 요청을 객체화시켜 다루며, 여러 요청들을 큐나 스택 구조에 기록하거나 다루는데 특화되있다.
# References
- [https://refactoring.guru/design-patterns/command](https://refactoring.guru/design-patterns/command)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)