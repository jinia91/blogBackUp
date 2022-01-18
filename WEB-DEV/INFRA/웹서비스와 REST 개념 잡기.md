# 웹서비스(Web Service)

## 웹 서비스란
> 네트워크에 분산된 정보를 서비스 형태로 개방하여 표준화된 방식으로 공유하는 기술로써, 서비스 지향 아키텍처 개념을 실현하는 대표적인 기술

- 쉽게 말해 서로 다른 플랫폼 위에 구동되는 여러 웹기반 서비스 어플리케이션들을 상호작용할 수 있도록 시스템환경을 제공해주는 소프트웨어 컴포넌트 묶음을 말한다.


## 웹 서비스(1세대) 주요 구성요소

![enter image description here](https://mblogthumb-phinf.pstatic.net/20131104_231/spdlqjdudghl_1383539896921ndJHA_PNG/1.png?type=w2)


### SOAP(Simple Object Access Protocol)
> HTTP, HTTPS, SMTP 등을 사용하여 XML 기반의 메시지를 네트워크 상태에서 교환하는 프로토콜

![enter image description here](https://upload.wikimedia.org/wikipedia/commons/thumb/5/59/SOAP.svg/1200px-SOAP.svg.png)

[SOAP 구조]

- 단순 객체 접근 프로토콜

- HTTP 프로토콜상에 SOAP Envelope, 헤더, 바디 등이 추가도니 xml 문서로 기본적인 송수신은 HTTP로 수행
- RPC기반 메시지 패턴으로 구현하는 경우가 보편적
- 클라이언트에서 서버로 메시지를 요청하고 서버는 응답

### WSDL(Web Service Description Language)
- 웹서비스 기술 언어

- 웹 서비스명, 제공위치, 메시지 포맷, 프로토콜 정보등 웹 서비스에 대한 상세정보가 기술된 XML형식으로 구현된 언어
### UDDI(Universal Description)

- 전역 비지니스 레지스트리

- 웹 서비스에 대한 정보인 WSDL을 등록하고 검색하기 위한 저장소로, 공개적으로 접근, 검색이 가능한 레지스티리이자 표준

- 서비스 제공자는 UDDI라는 서비스 소비자에게 이미 알려진 온라인 저장소에 그들이 제공하는 서비스 목록들을 저장하게 되고 서비스 소비자들은 그 저장소에 접근함으로써 원하는 서비스들의 목록을 찾을수 있음

## SOAP을 준수한 웹서비스 개발방식의 장점
- 프록시와 방화벽에 구애받지 않고 쉽게 통신 가능

- 플랫폼과 프로그래밍 언어에 독립적
- 분산환경에 적합
- 에러 처리에 대한 기본 내용이 내장 

## 웹서비스의 단점
- 복잡한 구조로 인한 오버헤드
- 개발하기 어려움
- RESTful 아키텍처에 비해 무겁고 느림


# REST와 웹 서비스


## REST? RESTful?
> 웹의 창시자중 한명인 Roy Fielding의 박사학위 논문에서 등장한 개념으로 당시의 웹 아키텍처가 웹의 본래 설계 우수성을 활용하지 못하고 있다고 지적하며, 웹의 장점을 최대한 활용할 수 있는 네트워크 기반의 아키텍처를 제안했는데 이것이 바로 REST다.

- Representational State Tranfer의 약자

- **리소스의 표현**에 의한 **상태 전달 방식**을 규정하는 아키텍쳐
	- 리소스의 표현 : 해당 소프트웨어가 관리하는 모든것과 그를 나타내는 이름(URI)
	- 사태전달방식 : JSON / XML 등

- HTTP 메소드를 통해 리소스를 처리


### RESTful?

- Rest API 를 제공하는 웹 서비스


### REST의 장점

- 아키텍쳐일뿐 프로토콜이 아니며, HTTP 프로토콜 인프라를 그대로 사용하므로 별도의 인프라가 필요하지 않다.

- HTTP 표준 프로토콜을 따르는 모든 플랫폼에서 적용 가능
- Hypermedia API의 기본을 충실히 지키면서 범용성을 보장
- REST API 메시지를 파악하기 쉽기때문에 의도를 명확히 파악하기 쉽다
- 서버 / 클라이언트 역할이 분명하다
- 최근 모바일 기기 확산으로 다기종 멀티 플랫폼 상호작용의 수요가 증가하면서, 가볍고 적용하기 쉬운 REST 아키텍쳐가 대두


### REST의 단점

- 명확한 표준이 존재하지않는다. (프로토콜이 아님)

- HTTP 메서드 형태가 제한적


## REST의 구성요소

### 자원(Resource)
- 서버에서 다루는 모든 것을 자원으로 부르며, 해당 자원을 부르는 고유한 `통합자원식별자`(_Uniform Resource Identifier_, _URI_) 가 존재한다.
- Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.

### 행위(HTTP Method)
- 어떤 행위를 할것인지 HTTP Method를 사용해 표현
- HTTP 메서드는 여러 종류가 있지만 주로 사용되는것은 5가지

![enter image description here](https://raw.githubusercontent.com/Songwonseok/CS-Study/main/Web/images/HTTP-Mehtod-1.JPG)

### (자원의) 표현(Representation of Resource)
- Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.
- 자원은 JSON / XML 등 여러 포맷으로 주고받을수 있음, 아키텍쳐 설계에 따른다.



# References
- [한국전자통신연구원(2010), SOAP 기반 웹서비스와 RESTful 웹서비스 기술 비교](https://ettrends.etri.re.kr/ettrends/122/0905001533/25-2_112_120.pdf)
- [위키백과 - 웹서비스](https://ko.wikipedia.org/wiki/%EC%9B%B9_%EC%84%9C%EB%B9%84%EC%8A%A4)
- [https://bcho.tistory.com/948](https://bcho.tistory.com/948)
- [https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)




