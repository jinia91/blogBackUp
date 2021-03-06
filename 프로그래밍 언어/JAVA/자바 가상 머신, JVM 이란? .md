
## Java Virtual Machine(JVM) 개요
### 정의

JVM이란 자바 가상 머신의 약어로 여기서 가상머신은 **시스템 가상머신**의 의미보다는 **프로세스 가상머신**의 뜻으로 사용되어

> 자바 바이트 코드를 OS에 특화된 코드로 변환하고 실행하는 주체 혹은 사양(Spec)

을 의미한다.

### JVM의 역할
![가상머신](https://files.itworld.co.kr/archive/image/2018/09/jw_jvm_overview_3x2_1200x800-100758586-large%281%29.jpg)

*[Credit: JavaWorld/ IDG]*

1. 자바 프로그램을 어느 운영체제 상에서도 실행 시킬수 있게 하는 것
    - WORA(Write Once Run Anywhere)
2. 프로그램 메모리 관리의 최적화

### 자바 개발자는 왜 JVM을 알아둬야 하는가?
 사실 프로그램을 개발할 때 JVM을 전혀 모르더라도 큰 지장은 없다.  기능 구현 잘하고, 알고리즘 채택 잘 해서 적당히 성능이 나오면 되는거 아닌가?

 하지만 프로그램의 성능은 알고리즘 외에도 메모리 관리와도 매우 밀접하게 연결되어 있고, JVM은 자바 프로그램의 수명 주기 전체를 관장하며 리소스를 할당하고 메모리를 관리한다.

따라서 자바 개발자라면 한정된 메모리를 효율적으로 사용하고 최적의 퍼포먼스를 내는 프로그래밍을 위해서 JVM을 잘 알아둬야 한다.


### 자바 바이트 코드

>바이트 코드란 특정 하드웨어가 아닌 가상 컴퓨터에서 돌아가는 실행 프로그램을 위한 이진 표현법이다.

 C/C++ 의 컴파일러들은 고수준의 언어를 바로 기계어로 번역하지만, 자바 컴파일러는 자바 언어로 된 코드를 자바 바이트 코드로 번역한다.

JVM은 번역된 자바 바이트 코드를 구동시키는 실행기이며, 바이트 코드는 JVM에 의존할 뿐, 플랫폼에 의존적인 코드가 없으므로 WORA를 구현하는 핵심 역할을 한다.

### JVM의 주요 특징
- 스택 기반의 가상머신 : 레지스터 기반으로 동작하는 물리 머신과 달리 스택기반으로 작동한다.
- 심볼릭 레퍼런스 동적 연결 : 기본 자료형을 제외한 모든 타입을 명시적 메모리 기반이 아니라 심볼릭 레퍼런스로 참조하고 Dynamic Linking을 실행한다.
    - 컴파일된 시점에서 .class 파일, 즉 바이트 코드는 심볼릭 레퍼런스로만 참조하며 해당 파일이 JVM에 로드될 때, 그 이름에 맞는 객체의 주소를 찾아가 연결하는 작업을 한다. 이 기술로 class 파일의 크기를 작게 유지할 수 있다.
- GC(Garbage Collecion)을 사용하여 자동 메모리 관리



## JVM 구조
![jvm](https://github.com/jinia91/blogBackUp/blob/main/img/4fd2d1ff-c79b-4972-8758-07850304a6b5.png?raw=true)

*[백기선, 더 자바, 코드를 조작하는 다양한 방법 中]*

### 클래스 로더

![클래스 로더](https://github.com/jinia91/blogBackUp/blob/main/img/5798d803-9a7e-42d6-8211-a4e89e6fc90b.png?raw=true)

*[백기선, 더 자바, 코드를 조작하는 다양한 방법 中]*


클래스 로더는 자바 바이트 코드를 읽어서 JVM의 Execution Engine이 사용할 수 있도록 Runtime Data Area에 적재하는 역할을 한다.

즉, 자바 클래스들은 한번에 메모리에 올라오는 것이 아니라, 런타임 시 필요할 때 동적으로 클래스를 로딩하게 된다.

#### 구동 과정
1. 로딩
    - 위 그림은 클래스 로더의 위임 모델을 나타내는 것으로 계층 구조로서 `BootStrap 클래스 로더`가 최상위 조상에 위치한다.
    - 자바 애플리케이션을 최초 구동하게 되면 **부트스트랩 클래스 로더**는 JVM을 기동할 때 생성되며, Object 클래스들을 비롯하여 자바 API들을 로드한다. 다른 클래스 로더와 달리 자바가 아니라 네이티브 코드로 구현되어 있다.
    - **익스텐션 클래스 로더**는 기본 자바 API를 제외한 확장 클래스들을 로드 한다. 다양한 보안 확장 기능 등을 여기에서 로드하게 된다.
    - **애플리케이션 클래스 로더**는 애플리케이션 클래스패스(애플리케이션 실행할 때주는 -classpath)에서 클래스를 읽고 로드한다.
    -  각 클래스 로더는 클래스를 읽다가 로드 요청을 받으면, 클래스 로더 캐시, 최상위 클래스로더, 순차적으로 하위 클래스로더 순으로 해당 클래스를 이미 로드했는지/ 로드할수 있는지를 확인한뒤, 불가한경우 클래스를 읽은 해당 로더가 클래스를 로드하게 된다.

2. 링크
   -  로딩된 자바 바이트 코드는 링크 과정으로 넘어간다.
   - Verify : .class 파일이 유효한지 유효성 검사를 수행, 클래스 로드의 전 과정 중에서 가장 까다로운 검사를 수행하는 과정으로서 가장 복잡하고 시간이 많이 걸린다. 
   - Preparation : 클래스가 필요로 하는 메모리를 할당하고, 클래스에서 정의된 필드, 메서드, 인터페이스들을 나타내는 데이터 구조를 준비한다.
   - Resolve : 심볼릭 메모리 레퍼런스를 메서드 영역에 있는 실제 레퍼런스로 교체한다.

3. 초기화
    - 클래스 변수들(static)을 적절한 값으로 초기화한다. 

### 메모리(런타임 데이터 영역)

런타임 데이터 영역은 JVM이 OS 위에서 구동되면서 할당받는 메모리영역을 총칭하는것이다. 런타임 데이터 영역은 위의 그림처럼 6개 영역으로 나눌 수 있다.![image](https://d2.naver.com/content/images/2015/06/helloworld-1230-4.png)

이중 PC 레지스터, JVM 스택, 네이티브 메서드 스택은 쓰레드마다 개별적으로 할당되며, 힙 메서드 영역, 런타임 상수풀은 모든 스레드가 공유해서 사용한다.

#### 쓰레드 개별 영역
  - PC 레지스터 : PC(Program Counter)레지스터는 각 스레드마다 하나씩 존재하며 스레드가 시작될 때 생성되고, 현재 스레드가 수행중인 코드의 주소를 갖는다.

- JVM 스택 : 각 스레드마다 하나씩 존재하며 스레드가 시작될때 생성된다. 스택 프레임(Stack Frame)이라는 구조체만 저장 가능하며, push, pop으로 작동한다.
 ![image](https://d2.naver.com/content/images/2015/06/helloworld-1230-5.png)

- 네이티브 스택 : 자바 외에 언어로 작성된 네이티브 코드를 위한 스택. 
-
#### 공용
- 메서드 영역 : class 영역이라고도 불리우며, 모든 스레드가 공유하는 영역이다. JVM이 시작될때 생성되고, JVM이 읽어들인 각각의 클래스와 인터페이스, **런타임 상수풀**, 필드와 메서드 정보, static변수, 메서드의 바이트 코드등 정적으로 판단할 수 있는 대부분의 데이터를 모두 보관한다.
    - 런타임 상수풀 : 클래스 파일 포맷상 constant_pool 테이블에 해당하는 영역으로 각 클래스와 인터페이스의 상수뿐만 아니라, 메서드와 필드에 대한 모든 레퍼런스까지 담고 있는 테이블이다. 즉, 어떤 메서드나 필드를 참조할 때 JVM은 런타임 상수 풀을 통해 해당 메서드나 필드의 실제 메모리상 주소를 찾아서 참조한다.
- 힙 : 동적으로 인스턴스가 저장되는 공간으로 가비지 컬렉션의 대상. 모든 쓰레드가 접근 가능하며 성능 이슈에도 많은 지분을 차지한다.


### 실행엔진

#### 인터프리터

간혹 자바가 컴파일 언어라고 하는 글들이 보이는데, 자바는 1차적으로 자바 바이트 코드로 컴파일이 되지만, 

클래스 로더를 통해 JVM 내의 런타임 데이터 영역에 배치된 바이트 코드를 **인터프리터**가  명령어단위로 한줄씩 읽어나가며 실행하므로 

컴파일과 인터프리터방식을 혼합한 하이브리드 언어라 칭하는게 더 맞는 표현이다.

#### JIT 컴파일러(Just In Time)
모든 코드를 한줄씩 읽어나가는 인터프리터는 아무리 기계어에 가까운 바이트코드로 변환된 상태라 하더라도 순수 컴파일언어에 비해 느릴 수 밖에 없다.

따라서 JIT 컴파일러는 코드를 처음 읽는 시점(동적시점)에 먼저 중복코드들을 찾아 네이티브 코드로 번역해 놓는다. 이후 인터프리터가 중복코드를 만날때 인터프리팅을 건너뛰고 캐시에 저장된 네이티브 코드를 사용함으로서 느린 속도를 보완한다.

다만 JIT 컴파일러가 컴파일하는 과정은 바이트코드를 인터프리팅하는 것 보다 훨씬 오래 걸리므로 한 번만 실행되는 코드는 컴파일하지 않고 인터프리팅하는 것이 유리하다.


따라서, JIT 컴파일러 사용하는 JVM들은 내부적으로 특정 메소드가 얼마나 자주 수행되는지 체크하고, 일정 정도를 넘을 때에만 컴파일을 수행한다.

#### GC(Garbage Collection)

더이상 참조되지 않는 힙영역의 인스턴스들을 자동으로 삭제해주는 역할


## References
- [네이버 D2](https://d2.naver.com/helloworld/1230)
- [백기선, 더 자바, 코드를 조작하는 다양한 방법 강의](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)
- [https://asfirstalways.tistory.com/158](https://asfirstalways.tistory.com/158)
- [https://loosie.tistory.com/116#h6]()