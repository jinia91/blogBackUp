# RestTemplate
## 정의
> RestTemplate은 Spring 3.0 부터 지원하는 템플릿으로, 애플리케이션 내에서 외부 서버로 Rest규약에 맞는 Http 요청을 보내고 응답을 받는 **동기방식** 통신 템플릿이다. <br>
> 스프링에서 제공하는 다른 템플릿(ex. JdbcTemplate)처럼 메서드호출만으로 복잡한 내부 작업을 쉽게 처리할수 있게 되어있다.

## 동기 / Blocking 방식 동작

   동기 / 비동기는 개념적으로 두개의 연관된 행위가 반드시 같은 시점에 일어남을 정의하는 개념이며, Blocking/NonBlocking은 함수의 코드 제어권이 특정 함수 호출시 반환이 있기전까지 존재하는지 여부를 정의하는 개념이다.
<br>
즉 동기 + Blocking 방식은 개념적으로 연관된 두 행위(요청과 응답)가 반드시 동(同)시점에서 일어나야하며, 이때 호출한 함수(Client)는 호출받은 함수(Server)가 값을 반환하기 전까지 코드의 제어권이 존재하지 않는 상태일때를 의미한다.

## RestTemplate 사용해보기

### Bean 등록

RestTemplate은 TreadSafe한 객체이므로 빈등록을 통해 싱글톤으로 관리해주는 것이 바람직하다.

    /**
     * Timeout 미설정시 커넥션을 무한정 물고있는 상황이 발생할 위험 존재
     * resttemplate 기본설정은 커넥션을 무한정 만드므로 아파치 HttpComponents를 통해 커넥션풀로 관리할것
     */
    @Bean
    public RestTemplate buildRestTemplate(){
        HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
        factory.setConnectTimeout(5000); // 연결 시간 초과 ms
        factory.setReadTimeout(5000); // 읽기 시간 초과 ms
        HttpClient httpClient = HttpClientBuilder.create()
                .setMaxConnTotal(100)
                .setMaxConnPerRoute(5)
                .build();
        factory.setHttpClient(httpClient);
        return new RestTemplate(factory);    
        }

`WebMvcConfigurer`를 구현한 설정 클래스를 만들어 위와같이 빈등록을 하면 된다.

RestTemplate은 아무 설정이 없을 경우 해당요청에 응답이 올때까지 무한정 대기하기가 default인데 이는 외부 api 장애시 본 서버의 요청도 무한정 대기하는 장애가 발생할 수 있기때문에 커넥션의 설정을 해주는것이 바람직하다.

또한 기본동작으로 요청이 올때마다 항상 로컬 포트를 열고 tcp Connection을 만드는데 요청량이 많을경우 커넥션을 만드는과정에서 오는 레이턴시로 성능상 문제가 발생할 수 있다.

따라서 `Apachi httpComponents`를 사용하여 커넥션풀을 만들어 사용하는것이 좋다.

`Apachi httpComponents`를 사용하기 위해서는 `implementation group: 'org.apache.httpcomponents', name: 'httpclient'` 를 통해 의존성을 추가해주자.


### RestTemplate 주요 메서드와 요청해보기

![image](https://media.vlpt.us/images/soosungp33/post/d02efacc-56e6-4abc-a390-12ecbb565c6b/image.png)

    String response;
            try {
            response = restTemplate.getForObject(url, String.class);
                        } catch (RuntimeException e){
                throw new IllegalArgumentException(String.format("Provider consider that URL{%s} is not valid",url));
            }
            



# WebClient
## 개요
WebCleint는 스프링 5.0에서 추가된 인터페이스다. 스프링 5.0 이전에는 비동기 클라이언트로 AsyncRestTemplate을 사용했었다. 하지만 스프링 5.0 이후부터는 WebClient를 사용할 것을 권장한다.

>NOTE: As of 5.0 this class is in maintenance mode, with only minor requests for changes and bugs to be accepted going forward. Please, consider using the org.springframework.web.reactive.client.WebClient which has a more modern API and supports sync, async, and streaming scenarios. <br>
> \- RestTemplate Api Note

## 특징

### Non-Blocking / 비동기 방식
비동기 방식이므로 연관된 두행위인 요청과 응답이 동시에 일어나지 않아도 되며, 논블로킹방식이므로 요청이 일어난 시점에서 코드의 제어권은 요청을 호출한 함수가 그대로 가진채 다음 코드를 진행하게 된다.
### 싱글 스레드 방식

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbmXoLk%2FbtqXXVBywK0%2F9jWK84VgwC4NqNWnTkY9H0%2Fimg.jpg)

Spring WebClient는 마치 노드js처럼 싱글스레드 방식으로 작동한다. webClient로 여러 요청이 들어오게되면 이를 관장하는 하나의 이벤트 루프 스레드가 해당 요청들을 job으로 관리하고 응답이 오면(callback) 이를 webClient에 메시지를 보냈던 객체로 응답을 보내주게된다.

### 이런 방식이 왜 필요하지??
![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsMFrh%2FbtqCMoJkmiO%2FC1DKIoWLdkPTUKoeO4GOuK%2Ftfile.svg)

전통적인 서버의 구조는 Servlet Stack으로 요청이 들어올때 Servlet 컨테이너에서 멀티쓰레드 환경으로 처리를 했다.

문제는 많은 요청이 동시에 일어날 경우, 쓰레드 풀의 쓰레드가 고갈되어 요청이 대기큐에서 오랜기간 대기하며 지연되는 문제가 발생한다는 점이다.( **Thread pool hell**)

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp5S5V%2Fbtqwg3zeAQP%2FtNjcoOvjL3LBOIRPa7Z61k%2Fimg.png)

여기서 일반적으로 반응속도의 상당 지분을 차지하는 행동은 I/O에서 발생하는데, 네트워크를 타는 행위 또한 여기에 해당한다.

그런데 이런 I/O 작업을 이벤트 루프를 사용하여 논블로킹/비동기 방식으로 처리하면, 싱글스레드만으로 밀려오는 요청은 요청대로 job으로 다루어 던져버리고, I/O 응답을 받는 족족 다시 이벤트 루프를 타고 원 요청에 반환해주므로 훨씬 효율적인 동작이 가능해진다.

![image](https://3.bp.blogspot.com/-3i759KJap_U/We6baQQFc2I/AAAAAAAAfTs/0G7gLgD2BWsmVbPluFFoeGhViOafX1QqgCLcBGAs/s1600/Boot1VsBoot2.png)

[동시 요청량이 많아질수록 기존 servlet 스택과 reactive 스택의 성능차이가 급격히 벌어진다.]


### Spring Mvc 기존 어플리케이션에 WebClient 적용하는 방법은?

처음부터 `Spring WebFlux`를 사용해 반응형 프로그래밍을 하여 어플리케이션 만들었다면 상관없지만, 기존의 `spring mvc` 를 사용해 어플리케이션을 구현했다면 `implementation 'org.springframework.boot:spring-boot-starter-webflux'` 의존성 추가를 해줘야한다.

WebClient를 사용하는 올바른 방법은 [스프링-부트2.1.18 공식 레퍼런스](https://docs.spring.io/spring-boot/docs/2.1.18.RELEASE/reference/html/boot-features-webclient.html)에 의하면 WebClient.Builder를 DI로 받아 각 컴포넌트별로 WebClient를 생성하라고 되어있다.


    @Service
    public class MyService {
    
    	private final WebClient webClient;
    
    	public MyService(WebClient.Builder webClientBuilder) {
    		this.webClient = webClientBuilder.baseUrl("https://example.org").build();
    	}
    
    	public Mono<Details> someRestCall(String name) {
    		return this.webClient.get().uri("/{name}/details", name)
    						.retrieve().bodyToMono(Details.class);
    	}
    }


### (2) 에서는 WebClient의 본격적인 사용법에 대해 알아보자

# References
- [https://velog.io/@soosungp33/%EC%8A%A4%ED%94%84%EB%A7%81-RestTemplate-%EC%A0%95%EB%A6%AC%EC%9A%94%EC%B2%AD-%ED%95%A8](https://velog.io/@soosungp33/%EC%8A%A4%ED%94%84%EB%A7%81-RestTemplate-%EC%A0%95%EB%A6%AC%EC%9A%94%EC%B2%AD-%ED%95%A8)
- [https://musma.github.io/2019/04/17/blocking-and-synchronous.html](https://musma.github.io/2019/04/17/blocking-and-synchronous.html)
- [https://alwayspr.tistory.com/44](https://alwayspr.tistory.com/44)